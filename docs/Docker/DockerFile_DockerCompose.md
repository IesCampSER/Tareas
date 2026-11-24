# Dockerfile y Docker Compose

## 1. Introducción

Hasta ahora hemos trabajado con contenedores creados a partir de imágenes disponibles en Docker Hub o de imágenes que hemos preparado previamente.

También sabemos que podemos crear y almacenar nuestras propias imágenes en un repositorio. En este tema vamos a dar un paso más: aprenderemos a **construir imágenes mediante un `Dockerfile`** y a **definir y gestionar aplicaciones con varios contenedores mediante Docker Compose**.

Al terminar el tema deberás ser capaz de:

- Explicar qué es un `Dockerfile` y para qué sirve.
- Crear un `Dockerfile` para construir una imagen propia.
- Utilizar las instrucciones más habituales de un `Dockerfile`.
- Construir, etiquetar, consultar y ejecutar una imagen creada por ti.
- Explicar qué problema resuelve Docker Compose.
- Definir varios servicios, redes y volúmenes en un fichero `compose.yaml`.
- Arrancar, detener y consultar una aplicación definida mediante Compose.

> **Idea clave:** un `Dockerfile` describe cómo construir una imagen. Un fichero Compose describe cómo ejecutar y relacionar los contenedores que forman una aplicación.

---

# Parte I. Construcción de imágenes con Dockerfile

## 2. ¿Qué es un Dockerfile?

Un `Dockerfile` es un fichero de texto que contiene una serie de instrucciones para construir una imagen Docker de forma automática.

En lugar de crear un contenedor, instalar programas manualmente, modificar su configuración y guardar después el resultado como una imagen, escribimos las operaciones necesarias en un fichero. Docker ejecuta esas instrucciones mediante el comando `docker build`.

Esto nos permite:

- **Repetir la construcción:** podemos volver a generar la misma imagen cuando la necesitemos.
- **Documentar la configuración:** el fichero deja por escrito qué imagen base utilizamos, qué software instalamos y cómo se configura.
- **Mantener y modificar las imágenes:** cambiar una versión o añadir un programa suele consistir en modificar unas líneas del fichero.
- **Compartir el trabajo:** podemos guardar el `Dockerfile` junto al resto del proyecto y compartirlo mediante un repositorio de código.
- **Automatizar:** la construcción puede integrarse en procesos de desarrollo y despliegue.

Un `Dockerfile` ocupa muy poco espacio en comparación con una imagen. No obstante, su principal ventaja no es únicamente ahorrar almacenamiento: es disponer de una receta reproducible y versionable para construir la imagen.

## 3. Crear nuestro primer Dockerfile

Vamos a construir una imagen basada en Ubuntu que tenga instalado el editor de texto `nano`.

### 3.1. Preparar el directorio de trabajo

Crea un directorio llamado `ubuntu-nano` y entra en él:

```bash
mkdir ubuntu-nano
cd ubuntu-nano
```

Dentro de ese directorio crea un fichero llamado exactamente `Dockerfile`, respetando las mayúsculas y minúsculas.

Puedes utilizar cualquier editor de texto. No es obligatorio instalar Visual Studio Code. Si trabajas habitualmente con él, puedes instalar la extensión de Docker para facilitar la edición.

### 3.2. Contenido del Dockerfile

Escribe lo siguiente:

```dockerfile
FROM ubuntu:24.04

RUN apt-get update \
    && apt-get install -y --no-install-recommends nano \
    && rm -rf /var/lib/apt/lists/*

CMD ["/bin/bash"]
```

Vamos a analizar las instrucciones:

- `FROM ubuntu:24.04`: indica la imagen base desde la que partimos. En este caso, Ubuntu 24.04.
- `RUN`: ejecuta comandos durante la construcción de la imagen. Actualizamos la información de los paquetes e instalamos `nano`.
- `CMD`: establece el comando predeterminado que se ejecutará al iniciar un contenedor si no se indica otro.

Hemos agrupado las operaciones de instalación en un solo `RUN`. Además, eliminamos los archivos temporales de la caché de paquetes para evitar que ocupen espacio innecesario en la imagen.

### 3.3. Construir la imagen

Desde el directorio que contiene el `Dockerfile`, ejecuta:

```bash
docker build -t ubuntu-nano:1.0 .
```

Significado de los elementos:

- `docker build`: construye una imagen a partir de un contexto de construcción y de las instrucciones del Dockerfile.
- `-t ubuntu-nano:1.0`: asigna a la imagen el nombre `ubuntu-nano` y la etiqueta `1.0`.
- `.`: indica que el contexto de construcción es el directorio actual. Docker podrá utilizar los archivos de ese contexto en instrucciones como `COPY`.

Comprueba que la imagen se ha creado:

```bash
docker images
```

### 3.4. Crear un contenedor a partir de nuestra imagen

Ejecuta:

```bash
docker run -it --name prueba-ubuntu ubuntu-nano:1.0
```

Dentro del contenedor, comprueba que `nano` está instalado:

```bash
nano
```

Sal del contenedor con `exit`.

**Importante:** construir una imagen no es lo mismo que crear un contenedor. `docker build` genera la imagen; `docker run` crea y arranca un contenedor a partir de ella.

### 3.5. Consultar el historial de la imagen

Podemos consultar las capas y las instrucciones que han contribuido a construir la imagen:

```bash
docker history ubuntu-nano:1.0
```

Las imágenes se construyen mediante capas. Algunas instrucciones del Dockerfile generan nuevas capas y Docker puede reutilizar las capas que no hayan cambiado para acelerar construcciones posteriores.

### 3.6. Usar otro nombre de Dockerfile

Por convención, el fichero se llama `Dockerfile`. Si utilizamos otro nombre, podemos indicarlo mediante `-f`:

```bash
docker build -t ubuntu-nano:1.0 -f Dockerfile.pruebas .
```

---

## 4. Instrucciones habituales de Dockerfile

Estas son algunas de las instrucciones que debes conocer. No todas se utilizan en todos los proyectos.

### 4.1. `FROM`: elegir la imagen base

Indica la imagen sobre la que construiremos la nuestra.

```dockerfile
FROM ubuntu:24.04
```

Es recomendable indicar una versión concreta en lugar de utilizar `latest`. Así reducimos el riesgo de que una construcción futura utilice una versión diferente sin que hayamos modificado el Dockerfile.

### 4.2. `RUN`: ejecutar comandos durante la construcción

Ejecuta comandos mientras se construye la imagen. Se utiliza, por ejemplo, para instalar paquetes o preparar directorios.

```dockerfile
RUN apt-get update && apt-get install -y nano
```

Se pueden agrupar varios comandos con `&&`. Esto permite reducir capas y, si se eliminan los archivos temporales en la misma instrucción, evitar que queden almacenados en una capa anterior.

### 4.3. `CMD`: comando predeterminado

Indica el comando que se ejecutará al iniciar un contenedor si no se proporciona otro.

```dockerfile
CMD ["python3", "app.py"]
```

En un Dockerfile puede haber varias instrucciones `CMD`, pero **solo se tendrá en cuenta la última**.

Es habitual utilizar el formato de lista, también llamado *exec form*, porque evita algunos problemas relacionados con la interpretación de comandos mediante una shell.

### 4.4. `ENTRYPOINT`: comando principal del contenedor

Define el ejecutable principal del contenedor. Se suele utilizar cuando queremos que el contenedor se comporte como una aplicación o herramienta concreta.

```dockerfile
ENTRYPOINT ["cat"]
CMD ["/etc/os-release"]
```

Al iniciar el contenedor se ejecutará:

```bash
cat /etc/os-release
```

En este ejemplo, `ENTRYPOINT` define el programa (`cat`) y `CMD` proporciona sus argumentos predeterminados.

**Diferencia básica:**

- `CMD` establece el comando o los argumentos predeterminados.
- `ENTRYPOINT` establece el ejecutable principal.
- Si se combinan en formato de lista, los argumentos de `CMD` se añaden a `ENTRYPOINT`.
- Los argumentos indicados al ejecutar `docker run` pueden sustituir los valores predeterminados de `CMD`.

### 4.5. `COPY`: copiar archivos al interior de la imagen

Copia archivos o directorios del contexto de construcción al sistema de archivos de la imagen.

```dockerfile
COPY index.html /var/www/html/index.html
```

El archivo `index.html` debe encontrarse dentro del contexto de construcción.

En la mayoría de los casos utilizaremos `COPY` para incorporar archivos propios del proyecto.

### 4.6. `ADD`: copiar y añadir archivos

También copia archivos, pero incorpora algunas funcionalidades adicionales, como la posibilidad de extraer determinados archivos comprimidos locales y de utilizar URL remotas.

```dockerfile
ADD sitio.tar.gz /var/www/html/
```

Como norma general, **utiliza `COPY`**. Reserva `ADD` para los casos en los que necesites expresamente alguna de sus funcionalidades adicionales. Para descargar recursos remotos suele ser preferible utilizar una herramienta de descarga de forma explícita y controlar el resultado.

### 4.7. `WORKDIR`: establecer el directorio de trabajo

Establece el directorio en el que se ejecutarán las instrucciones posteriores, como `RUN`, `CMD` o `ENTRYPOINT`.

Si el directorio no existe, Docker lo crea.

```dockerfile
WORKDIR /app
COPY . .
```

Después de establecer `/app` como directorio de trabajo, el contenido del contexto se copiará en ese directorio.

### 4.8. `ENV`: definir variables de entorno

Define variables de entorno que estarán disponibles durante la ejecución del contenedor.

```dockerfile
ENV APP_MODE=produccion
ENV APP_PORT=8080
```

Se pueden consultar dentro del contenedor:

```bash
echo "$APP_MODE"
echo "$APP_PORT"
```

No debemos utilizar `ENV` para guardar contraseñas o secretos, ya que pueden quedar incorporados a la imagen.

### 4.9. `EXPOSE`: documentar los puertos utilizados

Indica qué puerto utiliza la aplicación dentro del contenedor:

```dockerfile
EXPOSE 80
```

**Atención:** `EXPOSE` no publica el puerto en el anfitrión ni crea por sí mismo una regla de acceso. Es principalmente una indicación de documentación sobre los puertos que utiliza el contenedor.

Para publicar un puerto al crear el contenedor utilizamos, por ejemplo:

```bash
docker run -d -p 8080:80 nginx
```

En este caso, el puerto 8080 del anfitrión se dirige al puerto 80 del contenedor.

### 4.10. `USER`: ejecutar con un usuario determinado

Permite indicar el usuario que ejecutará las instrucciones posteriores y, por defecto, el proceso principal del contenedor.

```dockerfile
USER www-data
```

Si no se especifica otro usuario, muchos contenedores ejecutan sus procesos como `root`. Siempre que sea posible, conviene utilizar un usuario con los permisos mínimos necesarios.

Si necesitamos crear un usuario propio, debemos hacerlo antes de cambiar a él:

```dockerfile
RUN useradd --create-home aplicacion
USER aplicacion
```

### 4.11. `ARG`: parámetros durante la construcción

Permite definir variables que se pueden proporcionar al construir la imagen.

```dockerfile
ARG VERSION=1.0
LABEL version=$VERSION
```

Podemos cambiar el valor al construir:

```bash
docker build --build-arg VERSION=2.0 -t miapp:2.0 .
```

`ARG` se utiliza durante la construcción. No equivale a `ENV`, que define variables disponibles en el contenedor en ejecución.

No utilices `ARG` para introducir contraseñas o secretos: pueden quedar expuestos en los metadatos o en el historial de construcción.

### 4.12. Otras instrucciones

| Instrucción | Función |
|---|---|
| `VOLUME` | Indica puntos de montaje para datos que deben almacenarse fuera del sistema de archivos efímero del contenedor. |
| `LABEL` | Añade metadatos a la imagen, como el autor, la versión o una descripción. |
| `HEALTHCHECK` | Define una comprobación para que Docker pueda determinar si el contenedor está funcionando correctamente. |
| `SHELL` | Cambia la shell utilizada para interpretar instrucciones en formato shell, como `RUN`. |

Ejemplo de etiquetas:

```dockerfile
LABEL maintainer="Equipo de administración de sistemas"
LABEL version="1.0"
```

Ejemplo de comprobación de estado:

```dockerfile
HEALTHCHECK --interval=30s --timeout=5s \
  CMD curl -f http://localhost/ || exit 1
```

Este ejemplo requiere que `curl` esté instalado en la imagen y que exista un servicio HTTP que responda en el puerto 80.

---

## 5. El archivo `.dockerignore`

Cuando ejecutamos `docker build`, Docker utiliza un **contexto de construcción**: el conjunto de archivos que puede necesitar durante la construcción de la imagen.

No siempre interesa enviar todos los archivos del directorio. Por ejemplo, podemos tener archivos temporales, copias de seguridad o directorios muy grandes que no deben formar parte de la construcción.

Para excluirlos creamos un fichero llamado `.dockerignore` en el contexto de construcción.

Ejemplo:

```text
.git
*.log
*.bak
tmp/
```

Así evitamos enviar esos archivos al contexto de construcción. Esto puede acelerar la construcción y reducir el riesgo de incorporar información que no debería estar en la imagen.

---

## 6. Buenas prácticas al escribir Dockerfiles

Ten en cuenta estas recomendaciones:

1. **Utiliza imágenes base concretas**, por ejemplo `ubuntu:24.04`, en lugar de depender de `latest`.
2. **Agrupa operaciones relacionadas** en un mismo `RUN`, especialmente las operaciones de actualización e instalación de paquetes.
3. **Elimina archivos temporales y cachés** que no sean necesarios después de instalar el software.
4. **Utiliza `COPY` por defecto** y reserva `ADD` para necesidades concretas.
5. **Incluye un `.dockerignore`** para excluir archivos que no deben formar parte del contexto.
6. **Evita ejecutar aplicaciones como `root`** cuando no sea necesario.
7. **No incluyas contraseñas, claves privadas ni otros secretos** en el Dockerfile ni en la imagen.
8. **Utiliza etiquetas claras** para identificar las versiones de tus imágenes.

---

# Parte II. Docker Compose

## 7. ¿Qué problema resuelve Docker Compose?

Hasta ahora hemos creado y ejecutado contenedores de forma individual con comandos como `docker run`.

Pero una aplicación real puede necesitar varios contenedores que trabajen conjuntamente. Por ejemplo:

- Un servidor web.
- Un servidor de bases de datos.
- Una aplicación que acceda a esa base de datos.
- Una red para que los servicios se comuniquen.
- Volúmenes para conservar los datos.

Podríamos crear cada contenedor con un comando `docker run` diferente, configurar sus puertos, redes y volúmenes y recordar todas las opciones. Sin embargo, gestionar todo esto mediante comandos independientes puede resultar complicado.

**Docker Compose permite definir y gestionar una aplicación formada por varios servicios mediante un único fichero YAML.**

En ese fichero podemos declarar:

- Los servicios que forman la aplicación.
- Las imágenes que utilizará cada servicio o cómo construirlas.
- Los puertos que se publicarán.
- Las redes que utilizarán los servicios.
- Los volúmenes para conservar datos.
- Las variables de entorno y otras opciones de configuración.

El fichero habitual se llama `compose.yaml`. También es frecuente encontrar proyectos que utilizan `docker-compose.yml`.

Docker Compose forma parte de Docker y se utiliza actualmente mediante el comando `docker compose`, con un espacio entre ambas palabras.

## 8. Estructura básica de un fichero Compose

Un fichero Compose se escribe en formato YAML. Por ejemplo:

```yaml
services:
  web:
    image: nginx:1.27
    ports:
      - "8080:80"
```

Vamos a interpretar el ejemplo:

- `services`: contiene la definición de los servicios de la aplicación.
- `web`: es el nombre que hemos asignado al servicio.
- `image`: indica la imagen que se utilizará.
- `ports`: publica puertos del contenedor en el anfitrión.
- `"8080:80"`: conecta el puerto 8080 del anfitrión con el puerto 80 del contenedor.

Guarda el contenido en un fichero llamado `compose.yaml` y ejecuta:

```bash
docker compose up -d
```

Abre un navegador y visita:

```text
http://localhost:8080
```

Deberías ver la página de bienvenida de Nginx.

Para detener y eliminar los contenedores y la red creados por Compose:

```bash
docker compose down
```

La imagen descargada no se elimina con este comando. Tampoco se eliminan los volúmenes, salvo que se indique expresamente.

## 9. Comandos básicos de Docker Compose

Ejecuta estos comandos desde el directorio donde se encuentra `compose.yaml`:

| Comando | Función |
|---|---|
| `docker compose up` | Crea y arranca los servicios definidos. |
| `docker compose up -d` | Arranca los servicios en segundo plano. |
| `docker compose down` | Detiene y elimina los contenedores y las redes creadas por Compose. |
| `docker compose ps` | Muestra el estado de los servicios y contenedores del proyecto. |
| `docker compose logs` | Muestra los registros de los servicios. |
| `docker compose logs -f` | Muestra los registros y continúa actualizándolos. |
| `docker compose stop` | Detiene los servicios sin eliminar los contenedores. |
| `docker compose start` | Arranca servicios que estaban detenidos. |
| `docker compose build` | Construye o reconstruye las imágenes definidas mediante `build`. |
| `docker compose pull` | Descarga las imágenes indicadas en el fichero. |

Para consultar los registros de un único servicio:

```bash
docker compose logs web
```

Para arrancar de nuevo los servicios después de modificarlos:

```bash
docker compose up -d
```

Si has cambiado el Dockerfile o los archivos que se copian a la imagen, puedes forzar la reconstrucción:

```bash
docker compose up -d --build
```

## 10. Construir una imagen propia desde Compose

Compose no solo permite utilizar imágenes descargadas de Docker Hub. También puede construir una imagen a partir de un Dockerfile.

Vamos a crear un pequeño sitio web estático servido por Nginx.

### 10.1. Preparar el proyecto

Crea esta estructura de directorios:

```text
sitio-web/
├── compose.yaml
├── Dockerfile
├── .dockerignore
└── html/
    └── index.html
```

### 10.2. Crear la página web

En `html/index.html` escribe:

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Mi sitio web</title>
</head>
<body>
    <h1>Mi primer sitio web con Docker</h1>
    <p>Esta página se sirve desde un contenedor construido por mí.</p>
</body>
</html>
```

### 10.3. Crear el Dockerfile

En el fichero `Dockerfile` escribe:

```dockerfile
FROM nginx:1.27

COPY html/ /usr/share/nginx/html/

EXPOSE 80
```

Explicación:

- Partimos de una imagen de Nginx.
- Copiamos nuestra página web al directorio que Nginx utiliza para servir contenido.
- Documentamos que el servicio utiliza el puerto 80.

### 10.4. Crear el fichero `.dockerignore`

En `.dockerignore` escribe:

```text
.git
*.log
```

### 10.5. Crear el fichero `compose.yaml`

En `compose.yaml` escribe:

```yaml
services:
  web:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8080:80"
    restart: unless-stopped
```

Explicación:

- `web`: nombre del servicio.
- `build`: indica que Compose debe construir una imagen.
- `context: .`: el contexto de construcción es el directorio actual.
- `dockerfile: Dockerfile`: indica qué fichero contiene las instrucciones de construcción.
- `ports`: publica el puerto 80 del contenedor en el puerto 8080 del anfitrión.
- `restart: unless-stopped`: establece una política para que Docker reinicie el contenedor si se detiene, salvo que se haya parado expresamente.

### 10.6. Arrancar y comprobar la aplicación

Desde el directorio `sitio-web`, ejecuta:

```bash
docker compose up -d --build
```

Abre el navegador y visita:

```text
http://localhost:8080
```

Debe aparecer la página que has creado.

Comprueba los servicios:

```bash
docker compose ps
```

Consulta los registros:

```bash
docker compose logs web
```

Detén y elimina la aplicación:

```bash
docker compose down
```

**Fíjate en la relación entre ambos ficheros:**

- El `Dockerfile` explica cómo construir la imagen de Nginx con nuestra página web.
- El `compose.yaml` explica cómo construir esa imagen y cómo ejecutar el contenedor, incluyendo la publicación de puertos.

---

## 11. Redes y comunicación entre servicios

Cuando Docker Compose crea una aplicación con varios servicios, normalmente crea una red para que estos puedan comunicarse entre sí.

Los servicios de esa red pueden localizarse utilizando el nombre del servicio como nombre de host.

Por ejemplo, si tenemos un servicio llamado `db`, otro servicio puede conectarse a él utilizando `db` como dirección del servidor, siempre que ambos estén conectados a una red común.

**Importante:** desde un contenedor se utiliza el puerto interno del servicio de destino, no el puerto publicado en el anfitrión.

Ejemplo:

```yaml
services:
  web:
    image: nginx:1.27
    ports:
      - "8080:80"

  db:
    image: mariadb:11.4
```

En este ejemplo:

- El servidor web es accesible desde el anfitrión a través de `localhost:8080`.
- Los servicios de la aplicación pueden comunicarse entre sí por la red de Compose.
- Otro contenedor que necesite acceder a MariaDB utilizará `db` como nombre de host y el puerto interno de MariaDB, normalmente `3306`.
- No es necesario publicar el puerto de la base de datos en el anfitrión si solo van a acceder a ella otros servicios de la aplicación.

## 12. Volúmenes: conservar los datos

Los contenedores son reemplazables. Si eliminamos un contenedor y creamos otro, los datos que solo estaban almacenados en su sistema de archivos pueden perderse.

Para conservar datos utilizamos volúmenes.

En Compose podemos declarar un volumen y asociarlo a un servicio:

```yaml
services:
  db:
    image: mariadb:11.4
    volumes:
      - datos_db:/var/lib/mysql

volumes:
  datos_db:
```

En este ejemplo:

- `datos_db` es un volumen gestionado por Docker.
- `/var/lib/mysql` es el directorio del contenedor donde MariaDB almacena sus datos.
- Los datos se conservan aunque se elimine y se vuelva a crear el contenedor.

**Atención:** `docker compose down` no elimina normalmente los volúmenes con nombre. Para eliminarlos también se puede utilizar:

```bash
docker compose down -v
```

Este comando elimina los volúmenes declarados por el proyecto y puede provocar la pérdida de los datos almacenados en ellos. No lo ejecutes si necesitas conservar la información.

## 13. Variables de entorno en Compose

Las variables de entorno permiten configurar los servicios sin tener que modificar la imagen.

Por ejemplo:

```yaml
services:
  aplicacion:
    image: mi-aplicacion:1.0
    environment:
      APP_MODE: produccion
      APP_PORT: "8080"
```

También se pueden utilizar variables definidas en un fichero `.env` situado junto al fichero Compose:

```text
APP_MODE=produccion
APP_PORT=8080
```

Y referenciarlas desde `compose.yaml`:

```yaml
services:
  aplicacion:
    image: mi-aplicacion:1.0
    environment:
      APP_MODE: ${APP_MODE}
      APP_PORT: ${APP_PORT}
```

El fichero `.env` facilita la configuración, pero no debe confundirse con un sistema seguro de gestión de secretos. No subas contraseñas reales ni credenciales a un repositorio público.

---

**Licencia:** Lourdes Tamarit. Esta obra está bajo una licencia Creative Commons Atribución-CompartirIgual 4.0 Internacional (CC BY-SA 4.0).
