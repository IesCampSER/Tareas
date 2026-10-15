# Instalación y configuración básica de Squid en Linux

## Objetivos

Al terminar esta práctica podrás:

- Instalar el servidor proxy Squid.
- Identificar cómo iniciar, detener, reiniciar y recargar su configuración.
- Crear listas de control de acceso (ACL).
- Permitir el acceso al proxy desde una red local.
- Bloquear determinados sitios web para un grupo de equipos.
- Comprobar desde un cliente que el proxy funciona.

## 1. Instalar Squid y gestionar el servicio

Squid es un servidor proxy que puede actuar como intermediario entre los equipos de una red e Internet. También permite aplicar reglas de acceso y, según la configuración, almacenar en caché determinados contenidos.

Vamos a instalarlo en la máquina virtual Linux preparada en la actividad **«Configurar 2 tarjetas de red en VirtualBox»**. El servidor debe disponer de dos interfaces de red:

- **Interfaz externa:** conectada a una red desde la que el servidor pueda acceder a Internet.
- **Interfaz interna:** conectada a la red interna donde estarán los equipos cliente.

En esta práctica se utilizará como ejemplo la red `192.168.20.0/24`. Si tu práctica utiliza otra red, sustituye estas direcciones por las que hayas configurado.

### 1.1. Actualizar la lista de paquetes

Abre un terminal y ejecuta:

```bash
sudo apt update
```

Este comando actualiza la información de los paquetes disponibles en los repositorios. No actualiza por sí solo todos los programas instalados.

Para actualizar los paquetes que ya están instalados, puedes ejecutar:

```bash
sudo apt upgrade
```

Lee las operaciones que propone el sistema antes de confirmarlas.

### 1.2. Instalar Squid

Instala el paquete actual de Squid con:

```bash
sudo apt install squid
```

El paquete `squid3` que aparece en documentación antigua ha quedado obsoleto en las distribuciones actuales de Debian y Ubuntu; el nombre habitual del paquete es `squid`.

Comprueba el estado del servicio:

```bash
sudo systemctl status squid
```

En muchas instalaciones, el servicio se inicia automáticamente al terminar la instalación. Si está detenido, puedes iniciarlo con:

```bash
sudo systemctl start squid
```

### 1.3. Iniciar, detener, reiniciar y recargar Squid

En sistemas Linux que utilizan `systemd`, administra el servicio con `systemctl`:

| Acción | Comando |
|---|---|
| Iniciar Squid | `sudo systemctl start squid` |
| Detener Squid | `sudo systemctl stop squid` |
| Reiniciar Squid | `sudo systemctl restart squid` |
| Consultar su estado | `sudo systemctl status squid` |

Después de modificar el archivo de configuración, normalmente puedes aplicar los cambios sin detener completamente el servicio:

```bash
sudo systemctl reload squid
```

Si la recarga no está disponible o Squid no aplica los cambios como esperas, reinicia el servicio. Antes de hacerlo, es recomendable comprobar que la configuración no contiene errores:

```bash
sudo squid -k parse
```

Los comandos antiguos basados en `/etc/init.d/squid` y `service` pueden seguir funcionando en algunos sistemas, pero `systemctl` es la forma recomendada en las distribuciones actuales que utilizan `systemd`.

## 2. Configuración básica del proxy

El archivo principal de configuración es:

```text
/etc/squid/squid.conf
```

Antes de editarlo, crea una copia de seguridad:

```bash
sudo cp /etc/squid/squid.conf /etc/squid/squid.conf.bak
```

Edita el archivo con un editor de texto, por ejemplo:

```bash
sudo nano /etc/squid/squid.conf
```

El símbolo `#` indica un comentario. El archivo incluye muchos parámetros; para esta práctica nos centraremos en las listas de control de acceso y en las reglas que determinan quién puede utilizar el proxy y a qué destinos puede acceder.

### 2.1. Cómo funcionan las ACL y las reglas de acceso

Una **ACL** (*Access Control List*, o lista de control de acceso) define un conjunto de equipos, destinos u otras condiciones. Después, las directivas `http_access` indican si las solicitudes que coinciden con esas condiciones se permiten o se deniegan.

Algunos tipos de ACL habituales son:

- `src`: dirección IP de origen del cliente.
- `dst`: dirección IP de destino.
- `dstdomain`: dominio del sitio web de destino.
- `port`: puerto de destino.
- `proto`: protocolo.

Las reglas `http_access` se evalúan en orden. Squid aplica la primera regla que coincide con la solicitud, por lo que el orden es importante. En una configuración segura, el acceso debe limitarse a los clientes autorizados.

### 2.2. Definir la red local, el grupo restringido y los dominios bloqueados

En `squid.conf`, localiza la sección que contiene un comentario parecido a este:

```text
# INSERT YOUR OWN RULE(S) HERE TO ALLOW ACCESS FROM YOUR CLIENTS
```

La ubicación y el texto exactos del comentario pueden variar según la versión. Añade las siguientes líneas en la sección de reglas de acceso, **antes de las reglas generales que deniegan el acceso**:

```conf
# Red local autorizada para utilizar el proxy
acl red_local src 192.168.20.0/24

# Equipos que tendrán restricciones adicionales
acl grupo1 src "/etc/squid/grupo1"

# Dominios bloqueados para los equipos del grupo1
acl webs_bloqueadas dstdomain "/etc/squid/webs_bloqueadas"

# Denegar al grupo1 el acceso a los dominios bloqueados
http_access deny grupo1 webs_bloqueadas

# Permitir el acceso al proxy desde la red local
http_access allow red_local

# Denegar el acceso al resto de equipos
http_access deny all
```

**Importante:** comprueba las reglas `http_access` que ya contiene el archivo. No añadas estas líneas sin revisar el contexto ni dejes reglas anteriores que permitan el acceso de forma más amplia. La regla `http_access deny all` debe quedar después de las reglas que quieras permitir.

Estas reglas hacen lo siguiente:

1. `red_local` identifica los clientes cuyas direcciones pertenecen a `192.168.20.0/24`.
2. `grupo1` identifica las direcciones IP enumeradas en el archivo `/etc/squid/grupo1`.
3. `webs_bloqueadas` identifica los dominios enumerados en `/etc/squid/webs_bloqueadas`.
4. `http_access deny grupo1 webs_bloqueadas` deniega las solicitudes que cumplen **ambas** condiciones: proceder de un equipo del grupo1 y dirigirse a un dominio bloqueado.
5. `http_access allow red_local` permite el uso del proxy al resto de clientes de la red local, incluidos los equipos del grupo1 cuando acceden a dominios no bloqueados.
6. `http_access deny all` deniega el acceso a los equipos que no hayan coincidido con una regla anterior.

La última regla no significa que todos los equipos de la red local queden bloqueados: las solicitudes que ya hayan coincidido con una regla anterior se resuelven antes.

### 2.3. Crear el archivo `grupo1`

Crea el archivo que contendrá las direcciones IP de los equipos con restricciones adicionales:

```bash
sudo nano /etc/squid/grupo1
```

Por ejemplo, si tus clientes utilizan estas direcciones, escribe una por línea:

```text
192.168.20.21
192.168.20.20
```

Sustituye las direcciones de ejemplo por las IP reales de los clientes que quieras incluir. Estas direcciones no quedan completamente sin navegación: con las reglas anteriores, solo se les denegarán los dominios que aparezcan en `webs_bloqueadas`.

Guarda el archivo y cierra el editor.

### 2.4. Crear el archivo `webs_bloqueadas`

Crea el archivo que contendrá los dominios que quieres bloquear para el grupo1:

```bash
sudo nano /etc/squid/webs_bloqueadas
```

Escribe un dominio por línea. Por ejemplo, para bloquear un dominio de prueba, puedes utilizar:

```text
.example.com
.example.org
```

Estos son ejemplos: sustitúyelos por dominios reales adecuados para la práctica. En las ACL `dstdomain`, un dominio precedido por un punto suele permitir que coincidan también sus subdominios. No escribas la URL completa con `https://`, rutas ni páginas concretas; utiliza nombres de dominio.

El filtrado por dominio puede bloquear solicitudes HTTP y conexiones HTTPS mediante el nombre de dominio, pero no permite inspeccionar el contenido cifrado de una conexión HTTPS. El comportamiento exacto depende de la solicitud y de la configuración de Squid.

Guarda el archivo y cierra el editor.

### 2.5. Comprobar la configuración y aplicar los cambios

Antes de recargar Squid, comprueba que el archivo de configuración se puede analizar:

```bash
sudo squid -k parse
```

Si aparecen errores, corrígelos antes de continuar. Después, aplica los cambios:

```bash
sudo systemctl reload squid
```

Si la recarga falla, consulta el estado del servicio:

```bash
sudo systemctl status squid
```

## 3. Memoria y caché en disco (opcional)

Las opciones de caché no son necesarias para realizar la prueba básica de control de acceso. Puedes dejarlas con sus valores predeterminados mientras aprendes a configurar las ACL.

Si más adelante necesitas modificar la caché, hazlo teniendo en cuenta la RAM disponible, el espacio libre en disco y la versión de Squid.

### 3.1. Caché en memoria

La directiva `cache_mem` establece la cantidad de memoria que Squid puede utilizar para determinados objetos de la caché. **No representa toda la memoria RAM que puede consumir el proceso.** Por tanto, no debe calcularse como una fracción fija de la RAM sin considerar el sistema y su carga de trabajo.

Si decides cambiarla, localiza la directiva existente en `squid.conf` y modifica esa línea en lugar de añadir una segunda definición. Para esta práctica introductoria, puedes conservar el valor predeterminado.

### 3.2. Caché en disco

La directiva `cache_dir` configura el almacenamiento de caché en disco. Su sintaxis tradicional con el tipo `ufs` es:

```conf
cache_dir ufs /var/spool/squid 4000 16 256
```

Este ejemplo configura un tamaño de caché de aproximadamente 4000 MB, no 40 GB. Es solo un ejemplo de sintaxis, **no una configuración que debas añadir sin comprobar primero la instalación**. En algunas versiones o configuraciones, el directorio o el método de almacenamiento pueden ser distintos.

No es necesario asignar automáticamente el 50 % del disco a la caché. El tamaño debe decidirse según el espacio disponible y las necesidades reales. Si modificas `cache_dir`, revisa la documentación de la versión instalada y las instrucciones de inicialización del directorio antes de aplicar el cambio.

Para esta práctica, puedes omitir esta sección y trabajar con la configuración de caché que instala Squid por defecto.

## 4. Comprobar el funcionamiento desde un cliente

### 4.1. Preparar la red virtual

1. Mantén el servidor Squid conectado a dos redes virtuales: una con acceso a Internet y otra red interna para los clientes.
2. Conecta la tarjeta de red del cliente a la **misma red interna** que la interfaz interna del servidor.
3. Asigna al cliente una dirección IP de la red `192.168.20.0/24` que no esté utilizada por otro equipo. Por ejemplo:
   - Cliente 1: `192.168.20.20`
   - Cliente 2: `192.168.20.21`
4. Comprueba que el cliente puede comunicarse con la dirección IP interna del servidor. Si no hay comunicación, revisa las direcciones IP, la máscara y la configuración de VirtualBox.

La red interna de VirtualBox no proporciona por sí sola acceso directo a Internet. En esta práctica, el cliente accederá a sitios web a través del proxy, mientras que el servidor Squid debe poder llegar a Internet mediante su interfaz externa.

### 4.2. Configurar el navegador

En el cliente, configura manualmente el proxy HTTP/HTTPS del navegador con:

- **Servidor proxy:** dirección IP interna del servidor Squid.
- **Puerto:** normalmente `3128`, salvo que la configuración del servidor indique otro.

Consulta la actividad **«Configurar el navegador cliente para que use el Proxy»** para ver las instrucciones específicas del navegador utilizado.

### 4.3. Realizar las pruebas

Comprueba los siguientes casos y anota los resultados:

1. **Acceso a un dominio permitido:** desde el cliente, intenta abrir un sitio que no esté incluido en `webs_bloqueadas`. Debe poder acceder si la conexión del servidor a Internet funciona y el navegador utiliza el proxy.
2. **Acceso a un dominio bloqueado desde un equipo del grupo1:** intenta abrir un dominio incluido en `webs_bloqueadas`. Squid debe denegar el acceso.
3. **Acceso al mismo dominio bloqueado desde un equipo que no esté en el grupo1, pero sí en la red local:** según las reglas propuestas, este equipo podrá acceder al dominio porque la restricción se aplica únicamente al grupo1.
4. **Acceso desde un equipo fuera de la red autorizada:** si puedes realizar esta prueba en un entorno controlado, verifica que Squid deniega las solicitudes de equipos que no coincidan con una regla de permiso.

Si una prueba no da el resultado esperado, revisa las ACL, el orden de las reglas, las IP de los clientes, el proxy configurado en el navegador y el estado del servicio.

## 5. Recordatorio sobre máscaras de red

| Prefijo CIDR | Máscara de red |
|---|---|
| `/8` | `255.0.0.0` |
| `/16` | `255.255.0.0` |
| `/24` | `255.255.255.0` |

Por ejemplo, `192.168.20.0/24` representa la red con direcciones comprendidas entre `192.168.20.0` y `192.168.20.255`. Las direcciones utilizables para los equipos suelen ser `192.168.20.1` a `192.168.20.254`, siempre que no estén asignadas a otros dispositivos y de acuerdo con la configuración de la red.  

  
**Licencia:** Lourdes Tamarit. Esta obra está bajo una licencia Creative Commons Atribución-CompartirIgual 4.0 Internacional (CC BY-SA 4.0).
