# Prácticas
# Módulo: Servicios en Red

### Objetivos

Al finalizar estas actividades deberás ser capaz de:

-   Instalar y poner en marcha servidores web Nginx y Apache.
-   Identificar los elementos básicos de una petición y una respuesta
    HTTP.
-   Publicar una página web y acceder a ella desde un equipo cliente.
-   Configurar la resolución de nombres mediante DNS o, para pruebas
    locales, mediante el fichero `hosts`.
-   Configurar varios sitios web en Apache mediante *Virtual Hosts*,
    compartiendo una misma dirección IP y un mismo puerto.
-   Consultar e interpretar los registros de acceso y de errores de un
    servidor web.
-   Utilizar `curl` y `wget` para realizar peticiones HTTP y descargar
    recursos.
-   Comprobar la configuración y diagnosticar problemas habituales.

> **Importante:** realiza las actividades en el entorno indicado. No utilices permisos globales como `777`
> para solucionar problemas de acceso a los archivos web. Configura los
> propietarios, grupos y permisos necesarios.

------------------------------------------------------------------------

## TAREA 1. Instalación y comprobación de Nginx

Siguiendo las indicaciones del documento **«Nginx. Guía de
instalación»**, instala el servidor web Nginx en una máquina virtual
Linux.

Una vez instalado:

1.  Comprueba que el servicio está iniciado y habilitado para arrancar
    automáticamente.
2.  Comprueba que el servidor está escuchando en el puerto HTTP habitual
    (TCP 80).
3.  Desde una máquina cliente de la red de prácticas, accede a la página
    de inicio de Nginx utilizando la dirección IP del servidor.
4.  Identifica el fichero de configuración principal de Nginx y localiza
    el directorio que contiene la página web predeterminada.

**Entrega:**

-   Captura de pantalla del navegador de la máquina cliente mostrando la
    página de inicio de Nginx.
-   Captura o salida de terminal donde se vea el estado del servicio.
-   Indica la dirección IP del servidor y el puerto al que has accedido.

------------------------------------------------------------------------

## TAREA 2. Instalación y configuración básica de Apache

Vamos a instalar un servidor web Apache en nuestro servidor Linux.

Puedes realizar esta tarea en una máquina virtual o en un contenedor.
**Te recomiendo utilizar un contenedor Ubuntu e instalar Apache dentro
de él**, ya que así practicarás también el uso de contenedores.

Hay dos posibilidades:

-   **Contenedor Ubuntu con Apache instalado por ti (opción
    recomendada):** utilizarás una imagen de Ubuntu e instalarás y
    configurarás Apache siguiendo la guía.
-   **Imagen de Apache ya preparada:** es una alternativa válida, pero
    en las siguientes tareas necesitarás conocer la estructura de
    configuración de Apache. Por eso, en esta práctica es preferible que
    instales Apache tú mismo sobre Ubuntu.

Sigue las instrucciones del documento **«Apache. Guía de instalación»**
y lee también el apartado **«Cómo hacer las tareas de esta quincena»**.

### Actividades

1.  Instala el servidor web Apache.
2.  Comprueba que el servicio está iniciado y que escucha en el puerto
    TCP 80.
3.  Localiza el directorio raíz del sitio web predeterminado de Apache.
4.  Configura los permisos necesarios para que tu usuario pueda
    modificar los archivos de la página web. No concedas permisos de
    escritura a todos los usuarios.
5.  Modifica el fichero `index.html` para que muestre una página
    personalizada. Sustituye `Nombre Apellido` por tu nombre y primer
    apellido.

``` html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Ejemplo de página web de Nombre Apellido</title>
</head>
<body>
    <h1>Bienvenidos a la página web de Nombre Apellido</h1>
</body>
</html>
```

### Acceso mediante nombre de dominio

Hasta ahora has accedido al servidor utilizando su dirección IP. Sin
embargo, es habitual acceder a los servidores web mediante un nombre,
por ejemplo:

`www.sercamp.org`

Para que esto funcione, el nombre debe resolverse a la dirección IP del
servidor web. Puedes hacerlo de dos formas:

-   **Mediante DNS:** configura el registro correspondiente en el
    servidor DNS del laboratorio.
-   **Mediante el fichero `hosts` del cliente:** si no dispones de un
    servidor DNS operativo, añade una entrada con la dirección IP del
    servidor y el nombre que quieras utilizar. Esta solución sirve para
    realizar pruebas en el laboratorio; no sustituye a un servicio DNS
    para una red completa.

Ejemplo de entrada (sustituye la IP por la de tu servidor):

``` text
192.168.1.100    www.sercamp.org
```

**Atención:** utiliza `127.0.0.1` únicamente si el navegador se ejecuta
en la misma máquina donde está el servidor web. Si accedes desde otro
equipo, debes indicar la IP del servidor web.

Una vez configurada la resolución de nombres, accede desde el navegador
del cliente a:

`http://www.sercamp.org`

### Consulta de los registros de Apache

Familiarízate con los registros del servidor. En Debian y Ubuntu suelen
encontrarse en:

``` bash
sudo tail -f /var/log/apache2/access.log
sudo tail -f /var/log/apache2/error.log
```

-   `access.log` registra las peticiones recibidas por el servidor y las
    respuestas que genera.
-   `error.log` recoge mensajes de error y otros avisos del servidor.

Realiza tres accesos desde el navegador y localiza las entradas
correspondientes en `access.log`. Identifica, al menos, el recurso
solicitado, el método HTTP y el código de estado de cada respuesta.

### Entrega

-   Captura del navegador de la máquina cliente accediendo a
    `http://www.sercamp.org` y mostrando tu página personalizada.
-   Captura de las tres peticiones identificadas en `access.log`. Señala
    o explica qué entrada corresponde a cada acceso.
-   Indica cómo has configurado la resolución del nombre: DNS o fichero
    `hosts`.
-   Captura o salida de terminal que demuestre que Apache está
    funcionando.

------------------------------------------------------------------------

## TAREA 3. Configuración de dos sitios web mediante Virtual Hosts en Apache

El objetivo de esta práctica es poner en marcha **dos sitios web
utilizando el mismo servidor Apache**, teniendo en cuenta que:

1.  Cada sitio web tendrá un nombre de dominio distinto.
2.  Ambos sitios compartirán la misma dirección IP.
3.  Ambos utilizarán el mismo puerto: TCP 80.

Vamos a crear dos sitios web para la empresa `sercamp.org`.

### Sitio web 1: clientes.sercamp.org

-   Nombre de dominio: `clientes.sercamp.org`
-   Directorio raíz: `/var/www/clientes`
-   Página inicial: `index.html`
-   Contenido: una página sencilla que dé la bienvenida a los clientes
    de la empresa.

### Sitio web 2: departamentos.sercamp.org

-   Nombre de dominio: `departamentos.sercamp.org`
-   Directorio raíz: `/var/www/departamentos`
-   Página inicial: `index.html`
-   Contenido: una página sencilla que dé la bienvenida a la página de
    los departamentos de la empresa.

### Actividades

1.  Crea los directorios `/var/www/clientes` y `/var/www/departamentos`.
2.  Crea en cada directorio un fichero `index.html` con un contenido
    diferente, de forma que puedas distinguir claramente los dos sitios.
3.  Configura los permisos necesarios para que Apache pueda servir los
    archivos.
4.  Crea y habilita los dos *Virtual Hosts* de Apache.
5.  Configura la resolución de los dos nombres mediante el servidor DNS
    del laboratorio. Si no dispones de DNS, utiliza el fichero `hosts`
    del cliente.
6.  Comprueba que ambos nombres resuelven a la misma dirección IP.
7.  Accede desde el cliente a las dos páginas utilizando sus nombres de
    dominio.

Recuerda que Apache utiliza el nombre de dominio solicitado por el
cliente (cabecera HTTP `Host`) para seleccionar el *Virtual Host*
correspondiente. Por eso pueden convivir varios sitios web en una misma
IP y puerto.

### Comprobaciones antes de recargar o reiniciar Apache

Antes de aplicar los cambios, comprueba que la configuración no contiene
errores:

``` bash
sudo apache2ctl configtest
```

También puedes consultar los *Virtual Hosts* que Apache tiene cargados:

``` bash
sudo apache2ctl -S
```

Si la configuración es correcta, recarga el servicio:

``` bash
sudo systemctl reload apache2
```

Si trabajas con una imagen o distribución en la que el nombre del
servicio o los comandos sean diferentes, utiliza los equivalentes
correspondientes.

### Entrega

-   Capturas de pantalla de la configuración de los dos *Virtual Hosts*.
-   Dos capturas del navegador del cliente: una accediendo a
    `http://clientes.sercamp.org` y otra a
    `http://departamentos.sercamp.org`.
-   Captura de la salida de los siguientes comandos:

``` bash
nslookup clientes.sercamp.org
nslookup departamentos.sercamp.org
```

-   El contenido del fichero de zona DNS `/etc/bind/sercamp.org.db`, si
    has utilizado el servidor DNS del curso.
-   Si has utilizado el fichero `hosts`, entrega una captura donde se
    vean las dos entradas. Por ejemplo:

``` text
192.168.1.100    clientes.sercamp.org
192.168.1.100    departamentos.sercamp.org
```

Sustituye la dirección IP del ejemplo por la de tu servidor. Recuerda
que las dos entradas deben apuntar a la misma IP.

------------------------------------------------------------------------

## TAREA 4. Comprobación del servicio HTTP con curl y wget

En esta actividad utilizarás herramientas de línea de comandos para
acceder a los sitios web que has configurado. El objetivo es comprobar
que un servidor web responde correctamente sin necesidad de utilizar un
navegador.

Realiza las pruebas desde la máquina cliente. Si utilizas un contenedor,
asegúrate de que el cliente puede alcanzar el servidor y de que la
resolución de nombres funciona.

### Actividades con curl

1.  Consulta la página principal del primer sitio web:

``` bash
curl http://clientes.sercamp.org
```

2.  Repite la operación con el segundo sitio:

``` bash
curl http://departamentos.sercamp.org
```

3.  Consulta únicamente las cabeceras HTTP de uno de los sitios:

``` bash
curl -I http://clientes.sercamp.org
```

Identifica en la respuesta el código de estado HTTP y algunas cabeceras,
como `Content-Type`, `Date` o `Server` (si el servidor las envía).

4.  Realiza una petición mostrando información adicional sobre la
    conexión y la petición HTTP:

``` bash
curl -v http://clientes.sercamp.org
```

Observa especialmente la cabecera `Host` que se envía al servidor.

5.  Guarda el contenido de una página en un fichero local:

``` bash
curl -o clientes.html http://clientes.sercamp.org
```

Comprueba que el fichero se ha creado y que contiene el HTML esperado.

### Actividades con wget

6.  Descarga la página del segundo sitio web:

``` bash
wget http://departamentos.sercamp.org
```

7.  Comprueba qué fichero se ha descargado y consulta su contenido.

### Entrega

-   Captura o salida de terminal de las pruebas realizadas con `curl` y
    `wget`.
-   Captura donde se vean las cabeceras obtenidas con `curl -I`.
-   Captura donde se aprecie la información relevante de `curl -v`,
    incluida la cabecera `Host`.

------------------------------------------------------------------------

## TAREA 5. (AMPLIACIÓN OPCIONAL) Publicación de puertos de un contenedor

El objetivo de esta actividad es publicar el puerto de un contenedor
para que el servidor web que se ejecuta dentro de él sea accesible desde
otras máquinas de la red.

Hasta ahora, dependiendo de la red utilizada por Docker, es posible que
hayas podido acceder al contenedor desde la propia máquina anfitriona,
pero no desde otros equipos. En esta actividad practicarás la
publicación de puertos.

### Actividades

1.  Lee el apartado **7.3.1** del siguiente material:

    [Gestión de imágenes
    Docker](https://github.com/ltamarit/curso-docker/blob/main/Gestion-Imagenes.md)

2.  Lee también el apartado **14.4** del siguiente material:

    [Principales acciones
    Docker](https://github.com/ltamarit/curso-docker/blob/main/Principales-Acciones-Docker.md)

3.  A partir de la información de esos apartados, crea o ejecuta un
    contenedor que tenga un servidor web funcionando y publica el puerto
    correspondiente.

4.  Comprueba desde otra máquina de la red que puedes acceder a la
    página web utilizando la IP de la máquina anfitriona y el puerto
    publicado.

5.  Explica qué relación existe entre el puerto del contenedor y el
    puerto publicado en la máquina anfitriona.

**Entrega:**

-   Comando utilizado para crear o ejecutar el contenedor, mostrando la
    publicación del puerto.
-   Captura de la salida que permita comprobar que el contenedor está en
    ejecución.
-   Captura del navegador de otra máquina accediendo a la página web
    mediante la IP de la máquina anfitriona y el puerto publicado.
-   Breve explicación de la correspondencia entre ambos puertos.

> Esta actividad es de ampliación y no es necesaria para completar las
> tareas obligatorias de esta quincena. Se trabajará con más detalle la
> publicación de puertos en la siguiente quincena.

------------------------------------------------------------------------
**Licencia:** Lourdes Tamarit. Esta obra está bajo una licencia Creative Commons Atribución-CompartirIgual 4.0 Internacional (CC BY-SA 4.0).
