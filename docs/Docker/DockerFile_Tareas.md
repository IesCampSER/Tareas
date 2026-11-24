# Parte III. Actividades

## Actividad 1. Construcción de una imagen propia

Crea una imagen basada en Ubuntu que incluya los programas `nano` y `curl`.

Requisitos:

1. Crea un directorio para el proyecto.
2. Escribe un `Dockerfile` que parta de Ubuntu 24.04.
3. Instala `nano` y `curl`.
4. Establece `/bin/bash` como comando predeterminado.
5. Construye la imagen con el nombre `ubuntu-herramientas` y la etiqueta `1.0`.
6. Comprueba que la imagen aparece en el listado de imágenes.
7. Crea un contenedor interactivo a partir de ella.
8. Comprueba que los dos programas están instalados.
9. Consulta el historial de la imagen con `docker history`.

**Entrega:** el contenido del Dockerfile y capturas o salidas de los comandos que demuestren que has realizado las comprobaciones.

## Actividad 2. Crear una imagen para un servidor web

Crea una imagen propia basada en Nginx que sirva una página web HTML creada por ti.

Requisitos:

1. Crea la estructura de directorios del apartado 10.
2. Escribe una página `index.html` con un título y un texto propios.
3. Crea un Dockerfile que copie la página al directorio adecuado de Nginx.
4. Incluye la instrucción `EXPOSE` correspondiente.
5. Construye la imagen con el nombre `mi-nginx` y la etiqueta `1.0`.
6. Crea y ejecuta un contenedor publicando el puerto 8080 del anfitrión en el puerto 80 del contenedor.
7. Comprueba el resultado desde el navegador.
8. Detén y elimina el contenedor.

**Entrega:** estructura de directorios, Dockerfile, página HTML y evidencias de funcionamiento.

## Actividad 3. Gestionar el servidor web con Docker Compose

Modifica el proyecto de la actividad anterior para que el contenedor se gestione mediante Docker Compose.

Requisitos:

1. Mantén el Dockerfile y la página HTML.
2. Crea un fichero `compose.yaml`.
3. Configura el servicio para que construya la imagen desde el Dockerfile del proyecto.
4. Publica el puerto 8080 del anfitrión en el puerto 80 del contenedor.
5. Arranca el servicio en segundo plano.
6. Comprueba que la página funciona desde el navegador.
7. Consulta el estado y los registros del servicio.
8. Detén y elimina la aplicación mediante Compose.
9. Modifica el texto de la página, reconstruye la imagen y comprueba el cambio.

**Entrega:** Dockerfile, `compose.yaml`, página HTML y evidencias de las comprobaciones.

## Actividad 4. Desplegar dos servicios con Compose

Crea una aplicación formada por dos servicios: un servidor web Nginx y una base de datos MariaDB.

Requisitos:

1. Define los dos servicios en `compose.yaml`.
2. Utiliza imágenes oficiales con etiquetas de versión concretas.
3. Publica el puerto 8080 del anfitrión en el puerto 80 de Nginx.
4. Configura un volumen con nombre para conservar los datos de MariaDB.
5. Configura las variables de entorno necesarias para inicializar la base de datos, utilizando valores de prueba y sin publicar credenciales reales.
6. No publiques el puerto 3306 en el anfitrión: la base de datos solo necesita ser accesible desde la red interna de Compose.
7. Arranca la aplicación y comprueba el estado de ambos servicios.
8. Detén y vuelve a arrancar la aplicación.
9. Elimina los contenedores con `docker compose down` y comprueba que el volumen sigue existiendo.
10. No elimines el volumen hasta que hayas comprobado que los datos se conservan.

**Entrega:** fichero `compose.yaml`, evidencias del estado de los servicios y comprobación del volumen.

> En esta actividad el objetivo es practicar la definición y gestión de varios servicios, las variables de entorno, la red interna y los volúmenes. No es necesario desarrollar una aplicación que conecte Nginx con MariaDB.

---

# 14. Resumen: Dockerfile frente a Docker Compose

| Dockerfile | Docker Compose |
|---|---|
| Define cómo construir una imagen. | Define cómo ejecutar una aplicación formada por uno o varios servicios. |
| Se utiliza con `docker build`. | Se utiliza con `docker compose up`, `down`, `logs`, etc. |
| Contiene instrucciones como `FROM`, `RUN`, `COPY`, `CMD` o `EXPOSE`. | Contiene secciones como `services`, `ports`, `volumes`, `environment` y `networks`. |
| El resultado es una imagen. | El resultado es un conjunto de recursos para ejecutar la aplicación: contenedores, redes y, si se declaran, volúmenes. |

**Recuerda:**

- Una imagen es una plantilla a partir de la cual se crean contenedores.
- Un Dockerfile es la receta para construir una imagen.
- Un contenedor es una instancia en ejecución (o detenida) de una imagen.
- Docker Compose permite definir y gestionar conjuntamente los servicios de una aplicación.
- Los volúmenes permiten conservar datos aunque se sustituyan los contenedores.

---

## 15. Recursos para ampliar

- [Documentación oficial de Dockerfile](https://docs.docker.com/reference/dockerfile/)
- [Buenas prácticas para escribir Dockerfiles](https://docs.docker.com/build/building/best-practices/)
- [Documentación oficial de Docker Compose](https://docs.docker.com/compose/)
- [Referencia del fichero Compose](https://docs.docker.com/reference/compose-file/)
- [Documentación de Docker Hub](https://hub.docker.com/)
