
# Guía de instalación y configuración de Apache en Ubuntu 22.04

## 1. Instalación de Apache

Para instalar Apache en Ubuntu 22.04, sigue estos pasos:

1. Actualiza la lista de paquetes:

   ```bash
   sudo apt update
   ```

2. Instala Apache utilizando el siguiente comando:

   ```bash
   sudo apt install apache2 -y
   ```

3. Verifica que Apache esté instalado correctamente comprobando su estado:

   ```bash
   sudo systemctl status apache2
   ```

   Si está en funcionamiento, deberías ver el estado como **active (running)**.

## 2. Comprobar el funcionamiento de Apache

Una vez instalado Apache, puedes verificar que esté funcionando abriendo tu navegador web y accediendo a `http://localhost` o `http://<tu_dirección_IP>` o `http://nombre-del-servidor` (siempre que tengamos configurado nuestro servidor de nombres DNS).

Deberías ver la página predeterminada de Apache, lo que indica que el servidor está en funcionamiento. 

`
It works!
`

También puedes comprobar que el servicio esté escuchando en el puerto 80 con:

```bash
sudo ss -tuln | grep :80
```
Podemos personalizar nuestra página modificando el archivo index.html que hay dentro de la carpeta /var/www/html/ del servidor web:

```
$sudo nano /var/www//html/index.html
```

Fíjate en la estructura que usa el HTML. Lo que pongas entre las etiquetas 

`<body>` y `</body>`

es lo que aparecerá en el documento.

**Un inciso muy importante:** el servidor web Apache se ejecuta inicialmente como root y puedes modificar el  index.html tal como se acaba de explicar, pero usar el usuario usuario root para poder operar con nuestro servidor no es lo mas adecuado, lo mas adecuado y acertado es usar nuestro usuario normal (por ejemplo: lourdes).

EL servidor apache crea un sitio virtual por defecto que es el que acabamos de ver bajo /var/www/html sigue leyendo y aprenderás a crear sitios virtuales, ahí verás cómo dar los permisos adecuados a tu sitio y debes aplicar esa misma explicación si quieres utilizar el sitio por defecto de apache.


        
## 3. Configuración de Apache

Los archivos de configuración de apache2 se encuentran en la carpeta /etc/apache2.
El archivo principal de configuración es /etc/apache2/apache2.conf. Antes de realizar cualquier cambio en este archivo, es conveniente realizar una copia de seguridad del mismo ya que si Apache encuentra algún error en el archivo de configuración, no arrancará. Se pueden configurar infinidad de parámetros. Los valores que hay por defecto funcionan bien en la mayoría de situaciones.

Las líneas que empiezan por #, se consideran comentarios y no se procesan por Apache.

## 4. Crear sitios virtuales

Vamos a preparar un servidor Apache2 para que funcione con varios dominios diferentes desde la misma IP, es decir, que un único servidor Apache aloje y resuelva 2 hosts del mismo dominio por separado. Es lo que se conoce como configurar VirtualHosts basados en nombres.
Para crear un sitio virtual (Virtual Host), sigue los siguientes pasos:

En este ejemplo crearemos un sitio web llamado bardetapas

1. Crea el directorio para tu sitio web. Por ejemplo:

   ```bash
   sudo mkdir -p /var/www/bardetapas

   ```

2. Asigna los permisos adecuados (voy a darle permisos al usuario lourdes):

   ```bash
   sudo chown -R lourdes:lourdes /var/www/bardetapas

   sudo chmod -R 755 /var/www/bardetapas

   ```

3. Crea un archivo de configuración para tu sitio virtual. Para ello puedes:
     
- Los ficheros de configuración de los sitios webs se encuentran en el directorio /etc/apache2/sites-available, hay un fichero que se llama 000-default.conf que es la configuración del sitio web por defecto. Puedes utilizar este fichero para copiarte la configuración
 ```bash
$ cd /etc/apache2/sites-available
$ sudo cp 000-default.conf bardetapas.conf
  ```
   Ahora abre bardetapas.conf  y modifica para obtener la siguiente configuración:

   ```
   <VirtualHost bardetapas.com:80>
       ServerAdmin admin@bardetapas
       ServerName bardetapas.com
       ServerAlias www.bardetapas.com
       DocumentRoot /var/www/bardetapas
       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```

Una vez instalado Apache, toda la configuración de Apache está en /etc/apache2/.

Mirando el contenido de este directorio, observamos distintos archivos y directorios, entre los cuales están: sites-available y sites-enabled.

En sites-available tendremos los VirtualHosts disponibles (pero no “activados”) para ser usados, y en sites-enabled, tendremos los VirtualHosts que tenemos funcionando.

La forma de trabajar entre estos directorios es a través de un enlace simbólico; así el default de sites-enabled será un enlace simbólico del default de sites-available.

4. Habilita el nuevo sitio con el comando:

   ```bash
   sudo a2ensite bardetapas.conf
   ```

5. Deshabilita el sitio por defecto (opcional):

   ```bash
   sudo a2dissite 000-default.conf
   ```

6. Recarga Apache para aplicar los cambios:

   ```bash
   sudo systemctl reload apache2
   ```
## 5. Arranque y parada del servidor web apache

Según la distribución que tengáis utilizareis diferentes herramientas para manejar los servicios por eso os añado aquí tres formas distintas para arrancar y parar
`sudo service apache2.start` o
`sudo /etc/init.d/apache2 start`o `sudo systemctl start apache2.service`

Idem stop y reload

## 6. Comprobar los logs de Apache

Apache guarda los logs de acceso y error en dos archivos principales:

- **Acceso**: `/var/log/apache2/access.log`
- **Errores**: `/var/log/apache2/error.log`

Puedes ver estos logs utilizando `cat`, `tail` o cualquier otro comando que prefieras. Por ejemplo, para ver los últimos registros de errores:

```bash
sudo tail -f /var/log/apache2/error.log
```

## 7. Deshabilitar un sitio virtual

Si en algún momento deseas deshabilitar un sitio virtual, puedes hacerlo con el siguiente comando:

```bash
sudo a2dissite clientes.conf
```

Luego, recarga Apache nuevamente:

```bash
sudo systemctl reload apache2
```

## 8. Habilitar módulos adicionales en Apache

Puedes habilitar módulos adicionales en Apache con el comando `a2enmod`. Por ejemplo, para habilitar el módulo de reescritura (rewrite), ejecuta:

```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```

## 9. Desinstalar Apache (opcional)

Si deseas desinstalar Apache de tu sistema, puedes hacerlo con:

```bash
sudo apt remove apache2 -y
```

Para eliminar completamente cualquier archivo de configuración residual, usa:

```bash
sudo apt purge apache2 -y
```
