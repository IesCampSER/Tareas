# HTTP (Hypertext Transfer Protocol)

## 1. ¿Qué es HTTP?

**HTTP (Hypertext Transfer Protocol)** es el protocolo que permite que
un navegador web y un servidor intercambien información. Gracias a HTTP
podemos visitar páginas web, consultar imágenes, descargar documentos y
utilizar servicios disponibles en Internet.

HTTP trabaja siguiendo un modelo **cliente-servidor**:

-   El **cliente** es el programa que solicita información. Normalmente
    es un navegador web, como Firefox, Chrome o Edge.
-   El **servidor web** recibe la petición y envía una respuesta. Puede
    entregar una página, una imagen, un documento u otro recurso.
-   La comunicación se realiza mediante una red, como la red local o
    Internet.

HTTP es un protocolo **sin estado (stateless)**. Esto significa que cada
petición se trata de forma independiente. Para recordar información de
un usuario, las páginas web pueden utilizar mecanismos como las cookies.

### 1.1. ¿Qué ocurre cuando visitamos una página web?

1.  Escribimos una dirección web en el navegador o hacemos clic en un
    enlace.
2.  El navegador obtiene la dirección IP del servidor mediante DNS.
3.  El navegador establece una comunicación con el servidor.
4.  El navegador envía una **petición HTTP** para solicitar un recurso.
5.  El servidor procesa la petición y envía una **respuesta HTTP**.
6.  El navegador interpreta la respuesta y muestra el contenido.

Una página web puede estar formada por varios recursos: un documento
HTML, imágenes, hojas de estilo CSS y archivos JavaScript. Por ello, el
navegador puede realizar varias peticiones para mostrar una página
completa.

### 1.2. La URL

Una **URL (Uniform Resource Locator)** es la dirección que permite
localizar un recurso en la web.

Su estructura general es:

``` text
esquema://servidor:puerto/ruta?consulta#fragmento
```

Ejemplo:

``` text
http://www.ejemplo.net:8080/documentos/manual.html?idioma=es
```

-   **Esquema:** `http`, indica el protocolo utilizado.
-   **Servidor:** `www.ejemplo.net`, identifica el servidor mediante un
    nombre que puede resolverse con DNS.
-   **Puerto:** `8080`, identifica el servicio al que se quiere acceder.
    Si no se indica, HTTP utiliza normalmente el puerto TCP 80.
-   **Ruta:** `/documentos/manual.html`, indica el recurso solicitado.
-   **Consulta:** `?idioma=es`, permite enviar parámetros al servidor.

El fragmento, como `#apartado2`, permite localizar una parte del
documento en el navegador y normalmente no se envía al servidor.

## 2. HTTP y sus versiones

HTTP ha evolucionado para mejorar la velocidad y el rendimiento de la
navegación.

  -----------------------------------------------------------------------
 |  Versión            |  Transporte habitual   |  Características
                                                  principales
  ----------------------- ----------------------- -----------------------
 |  HTTP/1.0           |  TCP                   |  Las conexiones solían
                                                  cerrarse después de
                                                  cada intercambio.

 |  HTTP/1.1           |  TCP                   |  Permite mantener
                                                  conexiones abiertas y
                                                  reutilizarlas para
                                                  varias peticiones.

 |  HTTP/2             |  TCP                   |  Mejora el rendimiento
                                                  mediante multiplexación
                                                  y compresión de
                                                  cabeceras.

 | HTTP/3              |  QUIC sobre UDP        |  Utiliza QUIC para
                                                  mejorar el transporte
                                                  de las comunicaciones.  
                                                  
  -----------------------------------------------------------------------

Para comprender el funcionamiento básico de HTTP es especialmente útil
estudiar HTTP/1.1, porque sus peticiones y respuestas tienen una
estructura textual fácil de observar.

No es necesario memorizar los detalles internos de HTTP/2 y HTTP/3 en
este nivel. Lo importante es saber que son versiones más modernas que
mejoran la comunicación entre el navegador y el servidor.

### 2.1. Puertos habituales

  Servicio     Puerto habitual
  ---------- -----------------
  HTTP                  TCP 80
  HTTPS                TCP 443

HTTP transmite los datos sin cifrar. HTTPS añade protección mediante
cifrado TLS. Por eso, actualmente la mayoría de páginas web utilizan
HTTPS.

## 3. Métodos HTTP

El **método HTTP** indica qué quiere hacer el cliente con el recurso
solicitado.

Los métodos más habituales son:

  -----------------------------------------------------------------------
  Método                  Función                 Ejemplo
  ----------------------- ----------------------- -----------------------
  `GET`                   Solicita información.   Consultar una página
                                                  web o descargar una
                                                  imagen.

  `HEAD`                  Solicita las cabeceras, Comprobar información
                          sin recibir el          de un recurso.
                          contenido.              

  `POST`                  Envía datos al          Enviar un formulario.
                          servidor.               

  `PUT`                   Crea o sustituye un     Actualizar un recurso
                          recurso en una          de una aplicación.
                          dirección concreta.     

  `DELETE`                Solicita eliminar un    Borrar un elemento de
                          recurso.                una aplicación.

  `OPTIONS`               Consulta las opciones   Consultar los métodos
                          de comunicación         admitidos.
                          disponibles.            
  -----------------------------------------------------------------------

En la navegación habitual, los métodos que más veremos son `GET` y
`POST`.

### 3.1. Diferencia entre GET y POST

-   **GET:** se utiliza principalmente para solicitar información. Los
    parámetros de búsqueda pueden aparecer en la URL.
-   **POST:** se utiliza para enviar datos al servidor, por ejemplo, al
    rellenar un formulario.

Ejemplo de una petición GET:

``` http
GET /buscar?q=impresoras HTTP/1.1
Host: www.ejemplo.net
```

Ejemplo de una petición POST:

``` http
POST /contacto HTTP/1.1
Host: www.ejemplo.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 27

nombre=Ana&mensaje=Consulta
```

Los datos enviados mediante POST no aparecen necesariamente en la
dirección web, pero **eso no significa que estén cifrados**. Para
proteger la comunicación se utiliza HTTPS.

## 4. Estructura de los mensajes HTTP

En HTTP/1.1, una petición y una respuesta están formadas por:

1.  Una línea inicial.
2.  Una o varias cabeceras.
3.  Una línea en blanco que separa las cabeceras del contenido.
4.  Un cuerpo, que puede estar presente o no.

Esquema general:

``` text
Línea inicial
Cabecera: valor
Cabecera: valor

Cuerpo del mensaje (opcional)
```

La línea en blanco es importante porque indica dónde terminan las
cabeceras y dónde comienza el cuerpo.

### 4.1. Petición HTTP

Una petición HTTP es el mensaje que envía el cliente al servidor.

Ejemplo:

``` http
GET /index.html HTTP/1.1
Host: www.ejemplo.net
User-Agent: Mozilla/5.0
Accept: text/html
Accept-Language: es-ES
```

En este ejemplo:

-   `GET` es el método utilizado.
-   `/index.html` es el recurso solicitado.
-   `HTTP/1.1` indica la versión del protocolo.
-   `Host` indica el nombre del servidor al que queremos acceder.
-   `User-Agent` aporta información sobre el navegador o cliente.
-   `Accept` indica qué tipo de contenido acepta el cliente.
-   `Accept-Language` indica el idioma preferido.

### 4.2. Respuesta HTTP

La respuesta es el mensaje que devuelve el servidor al cliente.

Ejemplo:

``` http
HTTP/1.1 200 OK
Date: Mon, 02 Nov 2026 10:30:00 GMT
Server: nginx
Content-Type: text/html; charset=UTF-8
Content-Length: 1250

<!DOCTYPE html>
<html>
  <head><title>Ejemplo</title></head>
  <body><h1>Hola</h1></body>
</html>
```

En este ejemplo:

-   `HTTP/1.1` indica la versión del protocolo.
-   `200 OK` indica que la petición se ha atendido correctamente.
-   `Date` indica la fecha de la respuesta.
-   `Server` informa del software del servidor, si este decide
    mostrarlo.
-   `Content-Type` indica el tipo de contenido enviado.
-   `Content-Length` indica el tamaño del contenido en bytes.
-   Después de la línea en blanco aparece el documento HTML.

Los ejemplos anteriores muestran la representación textual de HTTP/1.1.
Las versiones HTTP/2 y HTTP/3 utilizan otros formatos internos para
transportar la información.

## 5. Códigos de estado HTTP

Cuando el servidor responde a una petición, incluye un **código de
estado de tres cifras**. Este código informa del resultado de la
operación.

El primer número indica la categoría:

  -----------------------------------------------------------------------
  Rango                   Categoría               Significado
  ----------------------- ----------------------- -----------------------
  1xx                     Informativo             La petición se ha
                                                  recibido y el proceso
                                                  continúa.

  2xx                     Éxito                   La petición se ha
                                                  procesado
                                                  correctamente.

  3xx                     Redirección             Es necesario realizar
                                                  otra acción o acceder a
                                                  otra dirección.

  4xx                     Error del cliente       La petición contiene un
                                                  error o no se permite
                                                  el acceso.

  5xx                     Error del servidor      El servidor no ha
                                                  podido completar la
                                                  petición.  
                                                  
  -----------------------------------------------------------------------

### 5.1. Códigos más habituales  


| Código | Nombre                  | Significado                         |
|--------|-------------------------|-------------------------------------|
| 200    | OK                      | La petición se ha realizado bien.   |
| 301    | Moved Permanently       | El recurso se ha trasladado.        |
| 403    | Forbidden               | El servidor deniega el acceso.      |
| 404    | Not Found               | No se encuentra el recurso.         |
| 500    | Internal Server Error   | Error interno del servidor.         |
  -----------------------------------------------------------------------
  Código                  Nombre                  Significado
  ----------------------- ----------------------- -----------------------
  `200`                   OK                      La petición se ha
                                                  realizado
                                                  correctamente.

  `201`                   Created                 Se ha creado un
                                                  recurso.

  `301`                   Moved Permanently       El recurso se ha
                                                  trasladado de forma
                                                  permanente.

  `302`                   Found                   Redirección temporal.

  `304`                   Not Modified            El recurso no ha
                                                  cambiado y puede
                                                  utilizarse una copia
                                                  almacenada.

  `400`                   Bad Request             La petición es
                                                  incorrecta.

  `401`                   Unauthorized            Se necesita
                                                  autenticación o las
                                                  credenciales no son
                                                  válidas.

  `403`                   Forbidden               El servidor deniega el
                                                  acceso.

  `404`                   Not Found               No se encuentra el
                                                  recurso solicitado.

  `405`                   Method Not Allowed      El método utilizado no
                                                  está permitido.

  `500`                   Internal Server Error   Se ha producido un
                                                  error interno en el
                                                  servidor.

  `502`                   Bad Gateway             Un servidor
                                                  intermediario ha
                                                  recibido una respuesta
                                                  no válida.

  `503`                   Service Unavailable     El servicio no está
                                                  disponible
                                                  temporalmente.

  `504`                   Gateway Timeout         Un servidor
                                                  intermediario no ha
                                                  recibido la respuesta a
                                                  tiempo.
  -----------------------------------------------------------------------

**Ejemplos cotidianos:**

-   Si escribimos una dirección incorrecta y la página no existe,
    podemos recibir un `404`.
-   Si una página funciona correctamente, normalmente recibiremos un
    `200`.
-   Si un servidor está temporalmente saturado o en mantenimiento, puede
    responder con un `503`.
-   Si intentamos acceder a una zona restringida, podemos recibir un
    `403`.

Es importante distinguir `401` y `403`: el primero está relacionado con
la autenticación; el segundo indica que el servidor rechaza el acceso.

## 6. Cabeceras HTTP

Las **cabeceras (headers)** son campos que proporcionan información
adicional sobre la petición o la respuesta.

Su formato es:

``` text
Nombre-Cabecera: valor
```

Por ejemplo:

``` http
Content-Type: text/html; charset=UTF-8
```

Esta cabecera indica que el contenido es HTML y utiliza la codificación
de caracteres UTF-8.

### 6.1. Cabeceras habituales de petición

  Cabecera            Función
  ------------------- -------------------------------------------------------------
  `Host`              Indica el nombre del servidor al que se dirige la petición.
  `User-Agent`        Aporta información sobre el navegador o cliente.
  `Accept`            Indica los tipos de contenido que acepta el cliente.
  `Accept-Language`   Indica los idiomas preferidos.
  `Authorization`     Incluye información para la autenticación.
  `Content-Type`      Indica el formato de los datos enviados.
  `Cookie`            Envía cookies almacenadas por el navegador.
  `Referer`           Indica desde qué página se ha iniciado la petición.

### 6.2. Cabeceras habituales de respuesta

  -----------------------------------------------------------------------
  Cabecera                            Función
  ----------------------------------- -----------------------------------
  `Date`                              Indica la fecha y hora de la
                                      respuesta.

  `Server`                            Informa del software del servidor,
                                      si se facilita.

  `Content-Type`                      Indica el tipo de contenido
                                      enviado.

  `Content-Length`                    Indica el tamaño del contenido en
                                      bytes, cuando se incluye.

  `Location`                          Indica una dirección de destino en
                                      determinadas respuestas.

  `Cache-Control`                     Establece instrucciones para las
                                      cachés.

  `Set-Cookie`                        Solicita al navegador que almacene
                                      una cookie.

  `Last-Modified`                     Indica la fecha de última
                                      modificación del recurso.
  -----------------------------------------------------------------------

No todas las cabeceras aparecen en todas las comunicaciones. Dependen de
la petición y de la respuesta.

## 7. Cookies y sesiones

HTTP es un protocolo sin estado. Por sí mismo, no recuerda las visitas
anteriores de un usuario.

Sin embargo, muchas páginas necesitan recordar información, por ejemplo:

-   Si el usuario ha iniciado sesión.
-   Las preferencias de idioma.
-   Los productos añadidos a un carrito de compra.
-   Algunas preferencias de navegación.

Para ello se pueden utilizar **cookies**. Una cookie es un pequeño dato
que el navegador almacena y que puede enviar posteriormente al servidor.

Funcionamiento básico:

1.  El servidor envía una cabecera `Set-Cookie`.
2.  El navegador almacena la cookie.
3.  En posteriores peticiones al sitio correspondiente, el navegador
    envía la cookie mediante la cabecera `Cookie`.
4.  El servidor puede utilizar ese dato para reconocer al usuario o su
    sesión.

Ejemplo de respuesta:

``` http
HTTP/1.1 200 OK
Set-Cookie: sesion=abc123; Path=/; HttpOnly; SameSite=Lax
```

Ejemplo de petición posterior:

``` http
GET /cuenta HTTP/1.1
Host: www.ejemplo.net
Cookie: sesion=abc123
```

La cookie puede guardar un identificador de sesión. No tiene por qué
contener todos los datos del usuario: es habitual que la información se
almacene en el servidor.

## 8. Caché del navegador

La **caché** permite guardar temporalmente copias de recursos
descargados, como imágenes, hojas de estilo o archivos JavaScript.

Su objetivo es:

-   Reducir el tiempo de carga de las páginas.
-   Evitar descargar repetidamente los mismos archivos.
-   Ahorrar ancho de banda.
-   Reducir el trabajo del servidor.

Una de las cabeceras utilizadas para controlar la caché es
`Cache-Control`.

Ejemplo:

``` http
HTTP/1.1 200 OK
Content-Type: text/css
Cache-Control: max-age=3600
```

En este ejemplo, la respuesta puede almacenarse durante 3600 segundos
(una hora), salvo que existan otras condiciones que lo impidan.

Cuando el navegador necesita comprobar si un recurso ha cambiado, puede
realizar una petición condicional. Si no ha cambiado, el servidor puede
responder con `304 Not Modified`, indicando que se puede utilizar la
copia almacenada.

## 9. Tipos de contenido y MIME

Una comunicación HTTP puede transportar muchos tipos de información:
texto, imágenes, audio, vídeo, documentos y archivos comprimidos.

El campo **`Content-Type`** indica al receptor cómo debe interpretar el
contenido del mensaje.

Los tipos MIME tienen esta estructura:

``` text
tipo/subtipo
```

Ejemplos:

``` http
Content-Type: text/html; charset=UTF-8
Content-Type: image/jpeg
Content-Type: application/pdf
```

### 9.1. Tipos MIME frecuentes

  Tipo MIME            Contenido
  -------------------- ------------------------
  `text/plain`         Texto sin formato.
  `text/html`          Documentos HTML.
  `text/css`           Hojas de estilo CSS.
  `image/jpeg`         Imágenes JPEG.
  `image/png`          Imágenes PNG.
  `image/gif`          Imágenes GIF.
  `application/pdf`    Documentos PDF.
  `application/zip`    Archivos ZIP.
  `application/json`   Datos en formato JSON.
  `audio/mpeg`         Audio MP3.
  `video/mp4`          Vídeo MP4.

El tipo MIME no transforma los datos. Simplemente informa de cómo debe
interpretarse el contenido recibido.

Por ejemplo, cuando un servidor envía una imagen, el navegador puede
utilizar el tipo MIME para saber que debe tratar el contenido como una
imagen.

## 10. Clientes y servidores web

### 10.1. Clientes HTTP

Un cliente HTTP es cualquier programa que realiza peticiones HTTP y
recibe respuestas.

El cliente más habitual es el **navegador web**. Algunos ejemplos son:

-   Mozilla Firefox.
-   Google Chrome.
-   Microsoft Edge.
-   Safari.
-   Opera.

También existen herramientas que permiten realizar peticiones desde una
terminal:

-   `curl`: permite realizar peticiones HTTP y consultar las respuestas.
-   `wget`: permite descargar recursos de Internet.

### 10.2. Servidores web

Un **servidor web** es un programa que recibe peticiones HTTP y envía
respuestas.

Puede entregar archivos almacenados en el equipo, como páginas HTML,
imágenes, documentos o archivos CSS.

Algunos servidores web conocidos son:

-   **Apache HTTP Server**.
-   **Nginx**.
-   **Microsoft IIS**.
-   **Caddy**.

Un servidor web puede estar instalado en un equipo de una empresa, en un
servidor de un centro educativo o en un servicio de alojamiento web de
Internet.

En una red local, por ejemplo, podríamos instalar un servidor web en un
ordenador y acceder a una página desde otro equipo utilizando el
navegador y la dirección IP del servidor.

## 11. Práctica: observar HTTP desde GNU/Linux

En esta práctica utilizaremos `curl`, una herramienta de terminal que
permite realizar peticiones HTTP.

Si no está instalada, en distribuciones basadas en Debian o Ubuntu se
puede instalar con:

``` bash
sudo apt update
sudo apt install curl
```

> **Nota:** Las pruebas de este apartado utilizan direcciones de ejemplo
> de Internet. Es posible que algunos servidores redirijan
> automáticamente a HTTPS o cambien sus respuestas con el tiempo.

### 11.1. Consultar una página web

Ejecuta:

``` bash
curl -v http://example.com/
```

La opción `-v` muestra información detallada de la comunicación, como
las cabeceras de petición y respuesta.

Observa qué método se utiliza y qué código de estado devuelve el
servidor.

### 11.2. Consultar únicamente las cabeceras

Ejecuta:

``` bash
curl -I https://example.com/
```

Esta opción realiza una petición HEAD. Muestra las cabeceras de
respuesta, pero no descarga el cuerpo de la página.

Fíjate en campos como `Content-Type`, `Date` o `Server`, si aparecen.

### 11.3. Consultar una página que no existe

Ejecuta:

``` bash
curl -i https://example.com/recurso-que-no-existe
```

La opción `-i` muestra las cabeceras de respuesta junto con el
contenido.

Identifica el código de estado y explica qué significa.

### 11.4. Descargar una página a un archivo

Ejecuta:

``` bash
curl -o pagina.html https://example.com/
```

Comprueba que se ha creado el archivo:

``` bash
ls -l pagina.html
```

Puedes consultar su contenido con:

``` bash
less pagina.html
```

### 11.5. Enviar datos mediante POST

Podemos simular el envío de un formulario utilizando un servicio público
de pruebas:

``` bash
curl -i -X POST \
  -H "Content-Type: application/x-www-form-urlencoded" \
  --data "nombre=Ana&curso=SMR" \
  https://httpbin.org/post
```

El servicio muestra los datos que ha recibido. No envíes información
personal real a servicios públicos de pruebas.

### 11.6. Comprobar la versión HTTP

Podemos indicar a `curl` que utilice HTTP/1.1 o HTTP/2:

``` bash
curl -I --http1.1 https://example.com/
curl -I --http2 https://example.com/
```

La disponibilidad de cada versión depende de la instalación de `curl` y
del servidor.

Para consultar las capacidades de la herramienta:

``` bash
curl --version
```

## 12. Actividades

1.  Explica con tus palabras qué es HTTP y para qué se utiliza.
2.  Indica qué diferencia hay entre un cliente HTTP y un servidor web.
    Pon un ejemplo de cada uno.
3.  Describe los pasos que se producen cuando escribes una dirección web
    en el navegador y pulsas Intro.
4.  ¿Qué es una URL? Identifica el esquema, servidor, puerto y ruta de
    esta dirección:
    `http://www.ejemplo.net:8080/documentos/manual.html`.
5.  ¿Qué diferencia hay entre HTTP y HTTPS? ¿Qué puerto utiliza
    normalmente cada uno?
6.  Explica la diferencia entre los métodos GET y POST. Pon un ejemplo
    de uso de cada uno.
7.  ¿Qué información contiene una petición HTTP? ¿Y una respuesta?
8.  Relaciona los siguientes códigos de estado con su significado:
    `200`, `301`, `403`, `404` y `500`.
9.  Un usuario puede abrir la página principal de una web, pero no puede
    acceder a una carpeta restringida. ¿Qué código de estado podría
    recibir? Explica por qué.
10. ¿Para qué sirven las cabeceras `Content-Type`, `Host` y
    `User-Agent`?
11. ¿Qué es una cookie y para qué puede utilizarla una página web?
12. ¿Qué ventajas ofrece la caché del navegador?
13. ¿Qué indica la cabecera `Content-Type: image/png`?
14. Nombra dos programas que puedan actuar como clientes HTTP y dos
    programas que puedan actuar como servidores web.
15. Realiza las pruebas de `curl` del apartado práctico. Anota el código
    de estado y dos cabeceras de respuesta de cada prueba.

## 13. Resumen

-   HTTP es un protocolo de aplicación que permite intercambiar
    información entre clientes y servidores web.
-   El navegador actúa normalmente como cliente y solicita recursos a un
    servidor.
-   Una URL identifica el recurso al que queremos acceder.
-   HTTP utiliza peticiones y respuestas. En HTTP/1.1, los mensajes
    incluyen una línea inicial, cabeceras, una línea en blanco y un
    cuerpo opcional.
-   Los métodos indican la operación que se quiere realizar. Los más
    habituales en la navegación son GET y POST.
-   Los códigos de estado indican el resultado de una petición: 2xx
    éxito, 3xx redirección, 4xx error del cliente y 5xx error del
    servidor.
-   Las cabeceras proporcionan información adicional sobre la
    comunicación y el contenido.
-   Las cookies permiten que las aplicaciones recuerden cierta
    información entre peticiones.
-   La caché guarda temporalmente recursos para mejorar la velocidad de
    navegación.
-   `Content-Type` indica el tipo de contenido que se está
    transmitiendo.
-   Apache, Nginx e IIS son ejemplos de servidores web.
-   `curl` permite realizar pruebas HTTP desde la terminal.

## 14. Referencias

-   MDN -- Introducción a HTTP:
    https://developer.mozilla.org/es/docs/Web/HTTP
-   RFC 9110 -- HTTP Semantics: https://www.rfc-editor.org/rfc/rfc9110
-   RFC 9112 -- HTTP/1.1: https://www.rfc-editor.org/rfc/rfc9112
-   IANA -- Media Types:
    https://www.iana.org/assignments/media-types/media-types.xhtml
-   curl -- Documentación oficial: https://curl.se/docs/

------------------------------------------------------------------------

**Licencia:** Lourdes Tamarit. Esta obra está bajo una licencia Creative
Commons Atribución-CompartirIgual 4.0 Internacional (CC BY-SA 4.0).
