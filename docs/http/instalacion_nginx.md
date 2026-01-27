# Manual para Instalar, Comprobar y Configurar Nginx en Ubuntu 22.04

## 1. Actualizar el Sistema
Es recomendable actualizar los paquetes del sistema antes de instalar cualquier software nuevo para evitar problemas de dependencias.

```
sudo apt update && sudo apt upgrade -y
```

## 2. Instalar Nginx
Ubuntu 22.04 incluye Nginx en sus repositorios predeterminados, por lo que la instalación es bastante sencilla.

```
sudo apt install nginx -y
```

## 3. Verificar el Estado del Servicio Nginx
Después de instalar Nginx, el servicio debería iniciarse automáticamente. Verifica su estado:

```
sudo systemctl status nginx
```

Deberías ver algo como:

<pre>
nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since ...
</pre>

## 4. Comprobar Funcionamiento
Para verificar que Nginx está funcionando correctamente, abre un navegador web y dirígete a la IP o dominio de tu servidor. Si estás trabajando en un servidor local, abre:

```
http://localhost
```

Deberías ver la página de bienvenida de Nginx que dice: **"Welcome to nginx!"**.

Otra forma de comprobar que el servidor está escuchando en el puerto correcto es usar \`curl\`:

```
curl http://localhost
```

Deberías obtener el HTML de la página de bienvenida.

## 5. Configuración de Nginx

El archivo de configuración principal de Nginx en Ubuntu se encuentra en:

```
/etc/nginx/nginx.conf
```

Además, cada sitio web puede tener su propia configuración en un archivo separado dentro de los directorios:

- **/etc/nginx/sites-available/**: Aquí es donde se almacenan los archivos de configuración de los sitios.
- **/etc/nginx/sites-enabled/**: Aquí es donde los archivos de sitios activos están habilitados mediante enlaces simbólicos.

### 5.1 Editar el Archivo de Configuración Principal (\`nginx.conf\`)
Para editar el archivo de configuración principal, usa un editor de texto como \`nano\`:

```
sudo nano /etc/nginx/nginx.conf
```

Algunos parámetros importantes que podrías querer ajustar son:

- **worker_processes**: Número de procesos de trabajo. Se puede ajustar al número de núcleos de CPU.

```nginx
worker_processes auto;
```

- **server_names_hash_bucket_size**: Si tienes un nombre de servidor muy largo, a veces es necesario aumentar este valor para evitar errores.

Guarda los cambios y cierra el editor.

### 5.2 Crear un Archivo de Configuración para un Nuevo Sitio

Supongamos que quieres configurar un sitio web llamado \`apellido.com\`. Primero, crea un archivo en \`sites-available\`:

```
sudo nano /etc/nginx/sites-available/apellido.com
```

Ejemplo básico de configuración para un sitio web:

```nginx
server {
    listen 80;
    server_name apellido.com www.apellido.com;

    root /var/www/apellido;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    error_page 404 /404.html;

    location = /404.html {
        internal;
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        internal;
    }
}
```

Este archivo define que Nginx escuchará en el puerto 80 para el dominio \`apellido.com\` y servirá archivos desde el directorio \`/var/www/apellido\`.

### 5.3 Habilitar el Sitio

Ahora, crea un enlace simbólico en el directorio \`sites-enabled\`:

```
sudo ln -s /etc/nginx/sites-available/apellido.com /etc/nginx/sites-enabled/
```

## 6. Probar la Configuración

Antes de reiniciar Nginx, siempre es buena idea probar la configuración para evitar errores:

```
sudo nginx -t
```

Si todo está correcto, deberías ver:

<pre>
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
</pre>

## 7. Reiniciar Nginx

Reinicia Nginx para aplicar los cambios:

```
sudo systemctl reload nginx
```

## 8. Ajustar Permisos de los Archivos del Sitio

Asegúrate de que los archivos del sitio web tengan los permisos correctos. Crea el directorio del sitio y asigna los permisos:

```
sudo mkdir -p /var/www/apellido
sudo chown -R $USER:$USER /var/www/apellido
sudo chmod -R 755 /var/www/apellido
```

Coloca un archivo \`index.html\` para probar:

```
echo "<h1>Hola desde el sitio de apellido</h1>" | sudo tee /var/www/apellido/index.html
```

## 9. Configurar el Firewall (Opcional)

Si tienes \`ufw\` (firewall de Ubuntu) activado, deberías permitir el tráfico HTTP y HTTPS:

```
sudo ufw allow 'Nginx Full'
```
