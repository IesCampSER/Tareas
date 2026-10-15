# Práctica: configuración de red e instalación de un servidor proxy Squid

## Objetivos

- Configurar dos adaptadores de red en una máquina virtual Linux.
- Instalar y configurar Squid como servidor proxy.
- Crear y aplicar listas de control de acceso (ACL).
- Configurar un equipo cliente para navegar a través del proxy.
- Comprobar las reglas de acceso y analizar el registro de actividad de Squid.

## Tarea 1. Configuración de los adaptadores de red

Utiliza un servidor linux diferente al de las tareas anteriores NO UTILICES el server 192.168.20.5 sino luego te liarás con los adaptadores de red.

Configura dos adaptadores de red en la máquina virtual de la siguiente manera (puedes consultar el enlace ![Configurar dos adaptadores de red en VB](../../VBox/dos_adaptadores_VB.md) )  

- **Adaptador 1: Red interna.** Se utilizará para comunicar el servidor proxy con el equipo cliente.
- **Adaptador 2: NAT.** Se utilizará para que el servidor pueda acceder a Internet.

La interfaz conectada a la Red interna debe tener una dirección IP estática distinta de la que utiliza el servidor de las prácticas anteriores. Para esta práctica, configura la IP 192.168.20.7/24.

La interfaz conectada a NAT debe obtener su configuración IP mediante DHCP. En VirtualBox, la dirección asignada por NAT depende de la configuración de la máquina virtual.

Comprueba la configuración con:

```bash
ip address show
```

Verifica también la tabla de rutas:

```bash
ip route
```

La ruta por defecto debe salir por la interfaz conectada a NAT, para que el servidor pueda acceder a Internet.

### Entrega de la tarea 1

Incluye una captura de pantalla en la que se vean las dos interfaces y sus direcciones IP. La captura debe permitir identificar la interfaz conectada a la Red interna y la conectada a NAT.

## Tarea 2. Instalación y configuración de Squid

Instala Squid en la máquina Linux que has configurado en la tarea anterior.

A continuación, configura el servidor proxy siguiendo las indicaciones de los documentos proporcionados para la práctica. Debes crear y utilizar las ACL indicadas:

- `grupo1`
- `webs_bloqueadas`

Configura las reglas `http_access` necesarias para que se aplique el comportamiento solicitado en los documentos. Ten en cuenta que **el orden de las reglas `http_access` es importante**, ya que Squid las evalúa en orden.

Después de modificar la configuración, comprueba que no contiene errores y aplica los cambios. Si necesitas reiniciar el servicio, utiliza el comando correspondiente a tu distribución Linux.

## Tarea 3. Configuración del equipo cliente

Utiliza una máquina virtual Linux o Windows como cliente. Configura su adaptador de red en la **misma Red interna de VirtualBox** que utiliza la interfaz interna del servidor proxy.

El cliente debe tener una dirección IP de la misma subred que el servidor. Por ejemplo, si el servidor utiliza `192.168.20.7/24`, puedes asignar al cliente `192.168.20.8/24`, siempre que esa dirección no esté ya ocupada. Configura la máscara adecuada y comprueba que existe conectividad entre el cliente y el servidor.

En el navegador del cliente, configura manualmente el proxy HTTP con estos datos:

- **Dirección del proxy:** `192.168.20.7`
- **Puerto:** `3128` (puerto habitual de Squid, salvo que la configuración de la práctica indique otro).

El cliente debe acceder a Internet a través del proxy. No es necesario que tenga un segundo adaptador NAT: la finalidad es comprobar que utiliza el servidor proxy para sus peticiones web.

## Tarea 4. Comprobación del funcionamiento

Realiza las pruebas necesarias para demostrar que las reglas configuradas en Squid funcionan correctamente:

1. Intenta acceder desde el cliente a una página que deba estar bloqueada según la ACL `webs_bloqueadas`.
2. Intenta acceder desde el cliente a una página que esté permitida por las reglas de la práctica.
3. Comprueba que las peticiones del cliente aparecen en el registro de Squid.

Utiliza páginas que estén disponibles en el momento de realizar la práctica. Si una página no carga, no des por hecho que Squid la ha bloqueado: comprueba el mensaje del navegador y el registro del proxy para distinguir un bloqueo de un problema de conectividad o de disponibilidad de la página.

El registro de acceso suele encontrarse en:

```text
/var/log/squid/access.log
```

En el registro, localiza las peticiones realizadas durante las pruebas y comprueba cuáles han sido permitidas y cuáles denegadas. Según el tipo de petición, pueden aparecer códigos como `TCP_DENIED/403` para una petición denegada o `TCP_TUNNEL/200` para una conexión HTTPS establecida mediante el método CONNECT. La interpretación exacta depende de la petición y de la configuración.

## Entrega final

Entrega un documento o archivo con las siguientes evidencias:

1. **Configuración de red del servidor:** captura de `ip address show` en la que se vean las dos interfaces.
2. **Página bloqueada:** captura del cliente en la que se vea el intento de acceder a una página que Squid deniega.
3. **Página permitida:** captura del cliente en la que se vea que una página permitida se carga correctamente a través del proxy.
4. **Configuración de Squid:** captura de las partes relevantes de `squid.conf`, incluyendo las ACL `grupo1` y `webs_bloqueadas` y las reglas `http_access` que las aplican.
5. **Registro de acceso:** captura de `access.log` en la que se identifiquen peticiones permitidas y denegadas correspondientes a las pruebas realizadas.

Asegúrate de que las capturas sean legibles y de que se pueda relacionar cada prueba con la configuración que la provoca.

## Comprobaciones antes de entregar

- [ ] La interfaz de Red interna del servidor tiene la IP `192.168.20.7`.
- [ ] La interfaz NAT del servidor obtiene la configuración por DHCP.
- [ ] La ruta por defecto del servidor utiliza la interfaz NAT.
- [ ] El cliente está conectado a la misma Red interna de VirtualBox que el servidor.
- [ ] El navegador del cliente tiene configurados la IP y el puerto del proxy.
- [ ] Las ACL y las reglas `http_access` están configuradas y en el orden adecuado.
- [ ] Se ha comprobado una petición bloqueada y otra permitida.
- [ ] Las peticiones aparecen en el registro de Squid.
- [ ] Se han incluido todas las capturas solicitadas.
