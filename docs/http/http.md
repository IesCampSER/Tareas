# HTTP (Hypertext Transfer Protocol)

## 1. ¿Qué es HTTP?

**HTTP (Hypertext Transfer Protocol)** es un protocolo de la capa de aplicación que permite intercambiar información entre clientes y servidores. Se utiliza principalmente para acceder a páginas web, pero también para transferir imágenes, documentos, vídeos, datos de aplicaciones y otros recursos.

HTTP sigue un modelo de comunicación **cliente-servidor** basado en peticiones y respuestas:

- El **cliente HTTP** solicita un recurso o realiza una operación. Normalmente es un navegador, pero también puede ser una aplicación, un programa o una herramienta de consola.
- El **servidor HTTP** recibe la petición, la procesa y devuelve una respuesta. Puede entregar un fichero, ejecutar una aplicación o consultar una base de datos antes de generar el contenido.
- Entre ambos pueden existir **intermediarios**, como proxies, cachés, pasarelas (*gateways*) o balanceadores de carga.

HTTP es un protocolo **sin estado (stateless)**: cada petición se interpreta de forma independiente y el protocolo no recuerda automáticamente las peticiones anteriores. Las aplicaciones pueden mantener información de sesión mediante mecanismos como las cookies.

### 1.1. Funcionamiento general

El intercambio básico se puede resumir en los siguientes pasos:

1. El usuario introduce una URL o pulsa un enlace.
2. El navegador interpreta la URL y obtiene el nombre del servidor al que debe acceder.
3. El cliente establece la comunicación con el servidor utilizando el transporte correspondiente a la versión HTTP negociada.
4. El cliente envía una **petición HTTP**, indicando el método, el recurso solicitado y otros datos mediante cabeceras.
5. El servidor procesa la petición y devuelve una **respuesta HTTP**, que contiene un código de estado, cabeceras y, normalmente, un cuerpo con los datos.
6. El navegador interpreta la respuesta y presenta el contenido. Si el documento HTML necesita imágenes, hojas de estilo, scripts u otros recursos, realizará nuevas peticiones.

Por ejemplo, al abrir una página que contiene un documento HTML, dos imágenes y una hoja CSS, el navegador puede realizar varias peticiones HTTP: una para el HTML, otras para las imágenes y otra para la hoja de estilos. Estas peticiones no tienen por qué utilizar conexiones independientes.

### 1.2. URL y recurso solicitado

Una URL identifica un recurso y puede incluir diferentes componentes:

```text
esquema://servidor:puerto/ruta?consulta#fragmento
```

Ejemplo:

```text
http://www.ejemplo.net:8080/documentos/manual.html?idioma=es
```

- **Esquema:** `http`, que indica el protocolo.
- **Servidor:** `www.ejemplo.net`, nombre que se resolverá mediante DNS.
- **Puerto:** `8080`. Si no se indica, el puerto predeterminado de HTTP es TCP 80.
- **Ruta:** `/documentos/manual.html`, que identifica el recurso.
- **Consulta:** `?idioma=es`, que contiene parámetros enviados al servidor.

El fragmento, si existe (por ejemplo, `#apartado2`), se utiliza normalmente en el navegador para localizar una parte del documento y no se envía como parte de la petición HTTP al servidor.

## 2. Versiones de HTTP

HTTP ha evolucionado para mejorar el rendimiento, reducir la latencia y aprovechar mejor las redes actuales. Las versiones mantienen los conceptos básicos de petición, respuesta, métodos, códigos de estado y cabeceras, pero no utilizan todas el mismo formato ni el mismo transporte.

| Versión | Transporte habitual | Características principales |
|---|---|---|
| HTTP/1.0 | TCP | Primera versión ampliamente utilizada. Las conexiones solían cerrarse al terminar cada intercambio. |
| HTTP/1.1 | TCP | Introduce conexiones persistentes por defecto, permite reutilizar una conexión y exige la cabecera `Host` en las peticiones. |
| HTTP/2 | TCP | Utiliza tramas binarias, comprime cabeceras y permite multiplexar varias peticiones simultáneas en una conexión. |
| HTTP/3 | QUIC sobre UDP | Mantiene la semántica de HTTP, pero utiliza QUIC, con flujos independientes y menor bloqueo entre peticiones. |

**HTTP/1.1 sigue siendo importante** porque permite comprender la estructura textual de las peticiones y respuestas y se utiliza en numerosos servicios y herramientas. HTTP/2 y HTTP/3 optimizan la forma de transportar esos mensajes.

### 2.1. HTTP/1.1

HTTP/1.1 utiliza normalmente una conexión TCP. Una de sus mejoras fundamentales respecto a HTTP/1.0 son las **conexiones persistentes**: la conexión puede mantenerse abierta para enviar varias peticiones y recibir sus respuestas, evitando establecer una conexión TCP nueva para cada recurso.

En HTTP/1.1 la cabecera `Host` permite indicar el nombre del sitio web al que se dirige la petición. Esto es necesario porque un mismo servidor o dirección IP puede alojar varios sitios web (alojamiento virtual).

### 2.2. HTTP/2

HTTP/2 mejora el rendimiento sin cambiar el significado de los métodos, las URL, los códigos de estado o las cabeceras HTTP.

Sus características más importantes son:

- **Formato binario:** los mensajes se transmiten mediante tramas binarias, en lugar de utilizar directamente el formato textual de HTTP/1.1.
- **Multiplexación:** una misma conexión TCP puede transportar varias peticiones y respuestas simultáneamente, organizadas en flujos (*streams*).
- **Compresión de cabeceras:** utiliza HPACK para reducir la información repetida en las cabeceras.
- **Control de flujo:** permite regular la cantidad de datos que se envía.

La multiplexación evita tener que esperar a que termine una respuesta para comenzar a recibir otra. Sin embargo, como HTTP/2 utiliza TCP, la pérdida de un segmento TCP puede afectar temporalmente a todos los flujos que comparten esa conexión.

### 2.3. HTTP/3

HTTP/3 utiliza **QUIC**, un protocolo de transporte que funciona sobre UDP. No utiliza TCP.

Entre sus características destacan:

- Multiplexación de flujos independientes.
- Menor bloqueo entre peticiones cuando se pierden paquetes.
- Establecimiento de conexiones más eficiente en determinadas circunstancias.
- Compresión de cabeceras mediante QPACK.

HTTP/3 conserva los métodos, códigos de estado y conceptos generales de HTTP. Lo que cambia es la forma de transportar los mensajes. QUIC integra mecanismos de seguridad basados en TLS 1.3; el estudio detallado de HTTPS y de los certificados se aborda en el tema correspondiente.

## 3. Métodos HTTP

El **método HTTP** indica qué operación pretende realizar el cliente sobre el recurso identificado por la URL.

Los métodos más importantes son:

| Método | Función | Ejemplo de uso |
|---|---|---|
| `GET` | Solicita la representación de un recurso. | Descargar una página HTML o una imagen. |
| `HEAD` | Igual que GET, pero solicita únicamente las cabeceras de la respuesta, sin el cuerpo. | Consultar el tipo o tamaño de un recurso. |
| `POST` | Envía datos para que el servidor los procese según la lógica de la aplicación. | Enviar un formulario o crear un registro mediante una API. |
| `PUT` | Crea o sustituye el estado del recurso identificado por la URL. | Actualizar completamente un recurso. |
| `PATCH` | Aplica modificaciones parciales a un recurso. | Cambiar únicamente el teléfono de un usuario. |
| `DELETE` | Solicita eliminar el recurso indicado. | Borrar un registro. |
| `OPTIONS` | Consulta las opciones de comunicación disponibles para un recurso o servidor. | Conocer los métodos admitidos. |
| `CONNECT` | Solicita establecer un túnel hacia el servidor indicado. | Crear un túnel a través de un proxy. |
| `TRACE` | Solicita una prueba de retorno de la petición. | Diagnóstico de intermediarios; suele deshabilitarse por motivos de seguridad. |

No todos los servidores permiten todos los métodos. Por ejemplo, un servidor que publica una web puede aceptar `GET` y `HEAD`, pero rechazar `PUT` o `DELETE`.

### 3.1. Diferencia entre GET y POST

Aunque ambos métodos pueden aparecer al trabajar con formularios, tienen finalidades diferentes:

- **GET** se utiliza para solicitar información. Los parámetros de consulta suelen aparecer en la URL. Es adecuado para operaciones de consulta que no modifican el estado del servidor.
- **POST** envía datos en el cuerpo de la petición para que la aplicación los procese. Se utiliza, por ejemplo, para enviar formularios, iniciar operaciones o crear recursos.

Ejemplo de GET:

```http
GET /buscar?q=linux HTTP/1.1
Host: www.ejemplo.net
```

Ejemplo de POST:

```http
POST /usuarios HTTP/1.1
Host: www.ejemplo.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 25

nombre=Ana&curso=ASIR
```

Los datos de una petición POST no aparecen necesariamente en la URL, pero **esto no significa que estén cifrados**. El cifrado de la comunicación corresponde a HTTPS, que se estudia en otro tema.

### 3.2. Diferencia entre POST y PUT

La diferencia no consiste simplemente en que uno envíe datos y el otro los escriba:

- Con **POST**, el servidor decide cómo procesar los datos enviados a la URL. Por ejemplo, puede crear un nuevo registro y asignarle un identificador.
- Con **PUT**, el cliente indica la URI del recurso que quiere crear o sustituir. Repetir la misma petición PUT debe tener, en condiciones equivalentes, el mismo efecto final.

Por ejemplo, `POST /usuarios` puede crear un usuario nuevo, mientras que `PUT /usuarios/25` puede crear o sustituir el usuario identificado con el número 25.

## 4. Estructura de los mensajes HTTP

En HTTP/1.1, tanto las peticiones como las respuestas se componen de una línea inicial, cabeceras, una línea en blanco y un cuerpo opcional.

```text
Línea inicial
Cabecera: valor
Cabecera: valor
...

Línea en blanco
Cuerpo del mensaje (opcional)
```

La línea en blanco es importante: indica dónde terminan las cabeceras y dónde comienza el cuerpo.

### 4.1. Petición HTTP

Una petición HTTP/1.1 tiene esta estructura:

```text
MÉTODO ruta versión
Cabecera: valor
Cabecera: valor

Cuerpo opcional
```

Ejemplo:

```http
GET /index.html HTTP/1.1
Host: www.ejemplo.net
User-Agent: Mozilla/5.0
Accept: text/html
Accept-Language: es-ES
Connection: keep-alive
```

En este ejemplo:

- `GET` es el método utilizado.
- `/index.html` es el recurso solicitado.
- `HTTP/1.1` indica la versión del protocolo.
- `Host` identifica el sitio web solicitado.
- `User-Agent` aporta información sobre el cliente.
- `Accept` indica qué tipos de contenido acepta el cliente.
- `Accept-Language` expresa las preferencias de idioma.
- `Connection: keep-alive` solicita mantener la conexión, aunque en HTTP/1.1 la persistencia ya es el comportamiento predeterminado.

Las cabeceras y el cuerpo dependen del tipo de petición. Por ejemplo, una petición GET sencilla normalmente no necesita cuerpo, mientras que una petición POST puede incluirlo.

### 4.2. Respuesta HTTP

La respuesta tiene esta estructura:

```text
Versión código estado descripción
Cabecera: valor
Cabecera: valor
...

Línea en blanco
Cuerpo de la respuesta (opcional)
```

Ejemplo:

```http
HTTP/1.1 200 OK
Date: Mon, 02 Nov 2026 10:30:00 GMT
Server: nginx
Content-Type: text/html; charset=UTF-8
Content-Length: 1250
Cache-Control: max-age=3600

<!DOCTYPE html>
<html>
  <head><title>Ejemplo</title></head>
  <body><h1>Hola</h1></body>
</html>
```

En este caso:

- `HTTP/1.1` indica la versión.
- `200 OK` indica que la petición se ha atendido correctamente.
- `Date` indica la fecha de generación de la respuesta.
- `Server` identifica el software del servidor, si este decide comunicarlo.
- `Content-Type` indica el tipo de contenido devuelto.
- `Content-Length` indica el tamaño del cuerpo en bytes.
- `Cache-Control` establece instrucciones para las cachés.
- Después de la línea en blanco aparece el documento HTML.

En HTTP/2 y HTTP/3 la información equivalente se transmite mediante tramas y no con esta representación textual. Los ejemplos anteriores muestran el formato legible de HTTP/1.1.

## 5. Códigos de estado HTTP

El servidor comunica el resultado de una petición mediante un **código de estado de tres cifras**. El primer dígito indica la categoría:

| Rango | Categoría | Significado |
|---|---|---|
| 1xx | Informativo | La petición se ha recibido y el proceso continúa. |
| 2xx | Éxito | La petición se ha recibido, comprendido y aceptado correctamente. |
| 3xx | Redirección | El cliente debe realizar otra acción, normalmente acceder a otra URL o utilizar una respuesta almacenada. |
| 4xx | Error del cliente | La petición contiene un error o no se puede atender por una condición atribuible al cliente. |
| 5xx | Error del servidor | El servidor no ha podido completar una petición aparentemente válida. |

### 5.1. Códigos más habituales

| Código | Nombre | Descripción |
|---|---|---|
| `100` | Continue | El servidor indica que el cliente puede continuar enviando la petición. |
| `101` | Switching Protocols | El servidor acepta cambiar de protocolo. |
| `200` | OK | La petición se ha realizado correctamente. |
| `201` | Created | Se ha creado un recurso. |
| `202` | Accepted | La petición se ha aceptado para su procesamiento, pero este todavía no ha terminado. |
| `204` | No Content | La petición se ha realizado correctamente, pero no hay contenido que devolver. |
| `206` | Partial Content | Se devuelve una parte del recurso solicitado. |
| `301` | Moved Permanently | El recurso tiene una nueva ubicación permanente. |
| `302` | Found | Redirección temporal. |
| `303` | See Other | El cliente debe consultar otra URL, normalmente mediante GET. |
| `304` | Not Modified | El recurso no ha cambiado respecto a la copia que el cliente puede utilizar. |
| `307` | Temporary Redirect | Redirección temporal que conserva el método de la petición. |
| `308` | Permanent Redirect | Redirección permanente que conserva el método de la petición. |
| `400` | Bad Request | La petición es incorrecta o no tiene una sintaxis válida. |
| `401` | Unauthorized | Se requiere autenticación o las credenciales no son válidas. |
| `403` | Forbidden | El servidor rechaza el acceso al recurso. |
| `404` | Not Found | No se encuentra el recurso solicitado. |
| `405` | Method Not Allowed | El método utilizado no está permitido para ese recurso. |
| `408` | Request Timeout | El servidor ha esperado demasiado tiempo a que el cliente completase la petición. |
| `409` | Conflict | La petición entra en conflicto con el estado actual del recurso. |
| `410` | Gone | El recurso ya no está disponible y esta situación se considera permanente. |
| `413` | Content Too Large | El cuerpo de la petición es demasiado grande. |
| `415` | Unsupported Media Type | El servidor no admite el formato del contenido enviado. |
| `429` | Too Many Requests | El cliente ha enviado demasiadas peticiones en un intervalo de tiempo. |
| `500` | Internal Server Error | Error interno del servidor. |
| `501` | Not Implemented | El servidor no admite la funcionalidad necesaria para atender la petición. |
| `502` | Bad Gateway | Un servidor que actúa como proxy o pasarela ha recibido una respuesta no válida del servidor al que accede. |
| `503` | Service Unavailable | El servicio no está disponible temporalmente, por ejemplo por mantenimiento o sobrecarga. |
| `504` | Gateway Timeout | Un proxy o pasarela no ha recibido a tiempo la respuesta del servidor de destino. |

**Importante:** `401 Unauthorized` suele indicar que falta autenticación o que las credenciales no son válidas. `403 Forbidden` indica que el servidor rechaza el acceso, aunque el cliente esté identificado.

## 6. Cabeceras HTTP

Las **cabeceras (headers)** son campos que aportan información adicional sobre la petición o la respuesta. Cada campo se escribe como un nombre, dos puntos y un valor:

```text
Nombre-Cabecera: valor
```

Los nombres de las cabeceras no distinguen entre mayúsculas y minúsculas. Por ejemplo, `Content-Type` y `content-type` representan el mismo campo.

Las cabeceras permiten negociar el formato del contenido, identificar el cliente, controlar la caché, gestionar la autenticación, definir cookies y proporcionar información sobre el recurso.

### 6.1. Cabeceras habituales de petición

| Cabecera | Función |
|---|---|
| `Host` | Nombre del servidor al que se dirige la petición. Obligatoria en HTTP/1.1. |
| `User-Agent` | Identifica el cliente o aporta información sobre él. |
| `Accept` | Indica los tipos de contenido que el cliente puede aceptar. |
| `Accept-Language` | Indica los idiomas preferidos. |
| `Accept-Encoding` | Indica los algoritmos de compresión que acepta el cliente. |
| `Authorization` | Incluye credenciales o información de autenticación. |
| `Content-Type` | Indica el formato del cuerpo enviado por el cliente. |
| `Content-Length` | Indica el tamaño del cuerpo de la petición, cuando se utiliza este mecanismo de delimitación. |
| `Cookie` | Envía al servidor cookies almacenadas por el navegador. |
| `Referer` | Indica la página desde la que se ha seguido un enlace o iniciado la petición. |
| `If-Modified-Since` | Permite solicitar el recurso solo si ha cambiado desde una fecha determinada. |
| `If-None-Match` | Permite comprobar si el recurso coincide con una etiqueta ETag conocida por el cliente. |

### 6.2. Cabeceras habituales de respuesta

| Cabecera | Función |
|---|---|
| `Date` | Fecha y hora de generación de la respuesta. |
| `Server` | Información sobre el software del servidor, si se facilita. |
| `Content-Type` | Tipo de contenido del cuerpo de la respuesta. |
| `Content-Length` | Tamaño del cuerpo en bytes, cuando se indica. |
| `Content-Encoding` | Codificación aplicada al contenido, por ejemplo `gzip` o `br`. |
| `Content-Language` | Idioma del contenido. |
| `Location` | URL de destino en determinadas redirecciones o respuestas de creación. |
| `Cache-Control` | Directivas para controlar el almacenamiento en caché. |
| `ETag` | Identificador de una versión concreta del recurso. |
| `Last-Modified` | Fecha de la última modificación del recurso. |
| `Expires` | Fecha a partir de la cual la respuesta se considera caducada. |
| `Set-Cookie` | Solicita al navegador que almacene una cookie. |
| `Allow` | Indica los métodos permitidos para un recurso, por ejemplo en una respuesta 405. |
| `Retry-After` | Indica cuánto tiempo conviene esperar antes de repetir una petición. |

No todas las cabeceras aparecen en todas las respuestas. El servidor incluye las que necesita según el recurso y la operación realizada.

## 7. Cookies, sesiones y estado

HTTP es un protocolo sin estado: por sí mismo no conserva información sobre las peticiones anteriores de un cliente. Sin embargo, muchas aplicaciones necesitan recordar que un usuario ha iniciado sesión, qué productos tiene en un carrito o qué preferencias ha elegido.

Para ello se utilizan, entre otros mecanismos, las **cookies**:

1. El servidor envía una cabecera `Set-Cookie` en una respuesta.
2. El navegador almacena la cookie de acuerdo con sus atributos.
3. En peticiones posteriores al ámbito correspondiente, el navegador envía la cookie mediante la cabecera `Cookie`.
4. El servidor puede utilizar su valor para reconocer una sesión o recuperar información asociada al usuario.

Ejemplo de respuesta:

```http
HTTP/1.1 200 OK
Set-Cookie: sesion=abc123; Path=/; HttpOnly; SameSite=Lax
```

Ejemplo de petición posterior:

```http
GET /cuenta HTTP/1.1
Host: www.ejemplo.net
Cookie: sesion=abc123
```

Una cookie puede contener un identificador de sesión, no necesariamente todos los datos de la sesión. Es habitual que el servidor guarde la información de sesión y que el navegador conserve únicamente un identificador.

## 8. Caché HTTP

La **caché** permite guardar temporalmente copias de recursos para evitar descargarlos de nuevo si no es necesario. Puede estar en el navegador, en un proxy o en una red de distribución de contenidos (CDN).

Sus objetivos son reducir el tiempo de carga, ahorrar ancho de banda y disminuir la carga de los servidores.

Algunas cabeceras importantes son:

- `Cache-Control`: establece cuánto tiempo puede almacenarse una respuesta y bajo qué condiciones.
- `Expires`: indica una fecha de caducidad.
- `ETag`: identifica una versión del recurso.
- `Last-Modified`: indica cuándo se modificó por última vez.

Ejemplo de respuesta:

```http
HTTP/1.1 200 OK
Content-Type: text/css
Cache-Control: max-age=3600
ETag: "v27-css"
```

En este ejemplo, la respuesta puede almacenarse durante 3600 segundos (una hora), salvo otras condiciones aplicables.

Cuando la copia almacenada puede estar desactualizada, el navegador puede realizar una petición condicional:

```http
GET /estilos.css HTTP/1.1
Host: www.ejemplo.net
If-None-Match: "v27-css"
```

Si el recurso no ha cambiado, el servidor puede responder:

```http
HTTP/1.1 304 Not Modified
ETag: "v27-css"
```

La respuesta `304 Not Modified` no necesita volver a enviar el cuerpo del recurso: el navegador puede utilizar la copia que ya tiene almacenada.

## 9. Tipos de contenido: MIME y Content-Type

HTTP puede transportar distintos tipos de datos: texto, imágenes, audio, vídeo, documentos, archivos comprimidos y datos de aplicaciones. Para que el receptor sepa cómo interpretar el contenido, se utiliza el campo **`Content-Type`**.

Los tipos de contenido se identifican mediante un *media type*, tradicionalmente conocido como tipo MIME, con esta estructura:

```text
tipo/subtipo
```

Ejemplos:

```http
Content-Type: text/html; charset=UTF-8
Content-Type: image/jpeg
Content-Type: application/pdf
```

El tipo MIME no transforma los datos a texto ASCII. HTTP puede transportar contenido binario. El tipo indica cómo debe interpretarse el cuerpo del mensaje.

### 9.1. Tipos principales

| Tipo | Contenido | Ejemplos |
|---|---|---|
| `text` | Contenido textual | `text/plain`, `text/html`, `text/css`, `text/csv` |
| `image` | Imágenes | `image/jpeg`, `image/png`, `image/gif`, `image/webp`, `image/svg+xml` |
| `audio` | Audio | `audio/mpeg`, `audio/ogg`, `audio/wav` |
| `video` | Vídeo | `video/mp4`, `video/webm` |
| `application` | Otros formatos y datos de aplicaciones | `application/pdf`, `application/json`, `application/zip`, `application/octet-stream` |
| `font` | Fuentes tipográficas | `font/woff`, `font/woff2` |

Otros tipos frecuentes:

| Tipo MIME | Uso habitual |
|---|---|
| `application/javascript` | JavaScript (en la práctica actual también se encuentra `text/javascript`). |
| `application/xml` | Documentos XML. |
| `application/json` | Datos en formato JSON, muy utilizado en API web. |
| `application/x-www-form-urlencoded` | Datos de formularios codificados como pares nombre-valor. |
| `multipart/form-data` | Formularios que pueden incluir archivos adjuntos. |

La lista oficial de tipos registrados se puede consultar en el registro de IANA:  
https://www.iana.org/assignments/media-types/media-types.xhtml

### 9.2. ¿Quién utiliza el tipo MIME?

El servidor web debe asociar los recursos que sirve con un tipo de contenido adecuado y enviarlo, normalmente, en la cabecera `Content-Type`. El navegador utiliza esa información para decidir cómo tratar el contenido: mostrarlo, descargarlo o entregarlo a una aplicación compatible.

Si un servidor configura incorrectamente el tipo MIME, el navegador puede interpretar el recurso de forma inadecuada. También es importante configurar correctamente `Content-Type` al enviar datos desde formularios o aplicaciones.

En sistemas GNU/Linux, el fichero `/etc/mime.types` contiene asociaciones entre tipos MIME y extensiones. Puede consultarse, por ejemplo, con:

```bash
cat /etc/mime.types
```

La configuración concreta del servidor web determina cómo se sirven los archivos; el contenido de `/etc/mime.types` no implica que todos los servidores instalados utilicen automáticamente esa misma configuración.

## 10. Clientes y servidores web

### 10.1. Clientes HTTP

Un cliente HTTP es cualquier aplicación que envía peticiones HTTP y recibe respuestas. El ejemplo más conocido es el navegador web.

Entre los navegadores actuales se encuentran Firefox, Chrome, Edge, Safari y Opera. También existen clientes de terminal y herramientas para probar servicios:

- `curl`: permite realizar peticiones HTTP desde la línea de comandos.
- `wget`: permite descargar recursos.
- Navegadores en modo texto, como Lynx.

El navegador interpreta HTML y CSS, ejecuta JavaScript y presenta los recursos al usuario. Parte del procesamiento se realiza en el cliente y otra parte puede realizarse en el servidor.

### 10.2. Servidores HTTP

Un servidor web es un programa que recibe peticiones HTTP y devuelve las respuestas correspondientes. Puede servir archivos estáticos (HTML, imágenes, CSS, JavaScript, etc.) o generar contenido dinámico mediante aplicaciones.

Ejemplos de servidores web:

- **Apache HTTP Server**
- **Nginx**
- **Microsoft IIS**
- **Caddy**
- **LiteSpeed**

En una arquitectura habitual, el servidor web puede actuar como intermediario entre el cliente y una aplicación. Por ejemplo, Nginx o Apache pueden recibir la petición y reenviarla a una aplicación desarrollada en PHP, Python, Java u otro lenguaje.

## 11. Práctica: analizar peticiones HTTP desde GNU/Linux

Podemos observar el funcionamiento de HTTP desde un terminal utilizando `curl`. Es una herramienta más actual y flexible que Telnet para realizar estas pruebas.

> **Nota:** Telnet transmite los datos sin cifrar y no permite negociar directamente HTTP/2 o HTTP/3. Para practicar la sintaxis textual de HTTP/1.1 se puede utilizar un servidor que acepte conexiones HTTP sin cifrar en el puerto 80. En los sitios que redirigen automáticamente a HTTPS, es preferible utilizar `curl`.

### 11.1. Consultar una página con GET

```bash
curl -v http://example.com/
```

La opción `-v` (*verbose*) muestra información de la conexión, las cabeceras de petición y las cabeceras de respuesta. El cuerpo de la respuesta se muestra a continuación.

Para mostrar únicamente las cabeceras de respuesta:

```bash
curl -I http://example.com/
```

Esta operación utiliza el método HEAD.

### 11.2. Consultar cabeceras con HEAD

```bash
curl -I http://example.com/
```

Observa campos como `Content-Type`, `Content-Length`, `Date`, `Last-Modified` o `Cache-Control`, si el servidor los devuelve.

### 11.3. Solicitar una página inexistente

```bash
curl -i http://example.com/recurso-que-no-existe
```

La opción `-i` muestra las cabeceras de respuesta junto con el cuerpo. Comprueba el código de estado devuelto y explica su significado.

### 11.4. Consultar las opciones disponibles

```bash
curl -i -X OPTIONS http://example.com/
```

El servidor puede devolver una cabecera `Allow` con los métodos permitidos, aunque no todos los servidores responden a OPTIONS de la misma manera.

### 11.5. Enviar datos mediante POST

Se puede simular el envío de un formulario:

```bash
curl -i -X POST \
  -H "Content-Type: application/x-www-form-urlencoded" \
  --data "nombre=Ana&curso=ASIR" \
  http://httpbin.org/post
```

El servicio de pruebas muestra los datos recibidos. No se debe enviar información personal real a servicios públicos de prueba.

### 11.6. Comprobar la versión HTTP utilizada

```bash
curl -I --http1.1 https://example.com/
curl -I --http2 https://example.com/
curl -I --http3 https://example.com/
```

La disponibilidad de HTTP/2 y HTTP/3 depende de que el servidor y la versión instalada de `curl` los soporten. Puede comprobarse con:

```bash
curl --version
```

La opción `-v` también permite observar información sobre la versión negociada.

## 12. Actividades

1. Utiliza `curl -v` para acceder a tres sitios web diferentes. Identifica en cada caso el método, la URL solicitada, las cabeceras principales y el código de estado.
2. Realiza una petición HEAD con `curl -I`. ¿Qué diferencias observas respecto a GET? ¿Se recibe el cuerpo de la página?
3. Solicita una ruta que no exista en uno de los servidores consultados. ¿Qué código de estado devuelve? Explica su significado.
4. Ejecuta una petición OPTIONS contra un servidor. ¿Devuelve información sobre los métodos admitidos? ¿Aparece la cabecera `Allow`?
5. Busca un ejemplo de respuesta que incluya una cabecera `Set-Cookie`. Explica para qué sirve y qué información puede almacenar una cookie.
6. Localiza una respuesta que incluya `Cache-Control` o `ETag`. Explica qué función cumple la cabecera encontrada.
7. Compara HTTP/1.1, HTTP/2 y HTTP/3: transporte utilizado, formato de los mensajes y principal mejora de cada versión.
8. Explica por qué una página web que contiene HTML, imágenes, CSS y JavaScript puede provocar varias peticiones HTTP.

## 13. Resumen

- HTTP es un protocolo de aplicación basado en el intercambio de peticiones y respuestas entre clientes y servidores.
- Los mensajes HTTP contienen una línea inicial, cabeceras, una línea en blanco y un cuerpo opcional.
- Los métodos indican la operación que se quiere realizar sobre un recurso: GET, POST, PUT, DELETE, HEAD, OPTIONS, entre otros.
- Los códigos de estado informan del resultado de la petición: 2xx éxito, 3xx redirección, 4xx error del cliente y 5xx error del servidor.
- Las cabeceras aportan información sobre el cliente, el servidor, el contenido, la caché y las cookies.
- Los tipos MIME identifican el formato del contenido transmitido.
- HTTP/1.1 utiliza normalmente TCP y conexiones persistentes; HTTP/2 añade multiplexación y compresión de cabeceras; HTTP/3 utiliza QUIC sobre UDP.
- HTTP es *stateless*, pero las aplicaciones pueden mantener sesiones mediante cookies y otros mecanismos.

## 14. Referencias

- RFC 9110 – HTTP Semantics: https://www.rfc-editor.org/rfc/rfc9110
- RFC 9112 – HTTP/1.1: https://www.rfc-editor.org/rfc/rfc9112
- RFC 9113 – HTTP/2: https://www.rfc-editor.org/rfc/rfc9113
- RFC 9114 – HTTP/3: https://www.rfc-editor.org/rfc/rfc9114
- MDN – HTTP overview: https://developer.mozilla.org/en-US/docs/Web/HTTP
- IANA – Media Types: https://www.iana.org/assignments/media-types/media-types.xhtml
- curl – documentación oficial: https://curl.se/docs/

---

Material docente elaborado y actualizado para la asignatura **Servicios en Red – ASIR**.
