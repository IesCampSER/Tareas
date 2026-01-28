# Guía de Instalación de VSFTPD en Ubuntu 22.04

Este documento explica cómo instalar y configurar el servidor FTP **vsftpd** en **Ubuntu 22.04**. VSFTPD (Very Secure FTP Daemon) es uno de los servidores FTP más seguros y populares disponibles para sistemas Linux.

## Requisitos previos

- Un servidor con Ubuntu 22.04.
- Acceso a una cuenta de usuario con permisos sudo.

## Paso 1: Actualizar el sistema

Antes de instalar cualquier paquete, es recomendable actualizar los paquetes del sistema:

```bash
sudo apt update
sudo apt upgrade -y
```

## Paso 2: Instalar VSFTPD

Para instalar el paquete **vsftpd**, ejecuta el siguiente comando:

```bash
sudo apt install vsftpd -y
```

## Paso 3: Configurar el Archivo de Configuración de VSFTPD

El archivo de configuración principal de VSFTPD se encuentra en `/etc/vsftpd.conf`. Vamos a realizar algunos cambios básicos para habilitar y asegurar el servicio.

Abre el archivo de configuración:

```bash
sudo nano /etc/vsftpd.conf
```

Realiza las siguientes modificaciones:

1. **Permitir conexiones anónimas** (opcional):
   - Por defecto, las conexiones anónimas están deshabilitadas. Si no necesitas permitir el acceso anónimo, asegúrate de que esta opción esté configurada como `NO`.
   ```conf
   anonymous_enable=NO
   ```

2. **Permitir acceso local**:
   - Habilita el acceso a los usuarios locales estableciendo esta opción en `YES`:
   ```conf
   local_enable=YES
   ```

3. **Permitir subida de archivos**:
   - Para permitir que los usuarios suban archivos, configura `write_enable` como `YES`:
   ```conf
   write_enable=YES
   ```

4. **Enjaular a los usuarios en su directorio de inicio**:
   - Para mayor seguridad, se recomienda enjaular (chroot) a los usuarios en sus directorios personales.
   ```conf
   chroot_local_user=YES
   ```

5. **Habilitar la seguridad TLS** (opcional pero recomendado):
   - Para hacer las transferencias seguras, habilita TLS. Primero, agrega las siguientes configuraciones:
   ```conf
   ssl_enable=YES
   allow_anon_ssl=NO
   force_local_data_ssl=YES
   force_local_logins_ssl=YES
   ssl_tlsv1=YES
   ssl_sslv2=NO
   ssl_sslv3=NO
   rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
   rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
   ```
   - **Nota**: Puedes generar un certificado SSL personalizado o utilizar uno existente en lugar de `ssl-cert-snakeoil.pem`.

Guarda y cierra el archivo (`Ctrl + O`, luego `Ctrl + X` en nano).

## Paso 4: Reiniciar el servicio VSFTPD

Después de realizar los cambios, reinicia el servicio para aplicar la nueva configuración:

```bash
sudo systemctl restart vsftpd
```

Para asegurarte de que VSFTPD esté habilitado y se inicie al arrancar el sistema, usa:

```bash
sudo systemctl enable vsftpd
```

## Paso 5: Configurar el Firewall (Opcional)

Si tienes un firewall configurado, necesitarás permitir el tráfico para FTP. Para hacerlo en **UFW**, ejecuta los siguientes comandos:

```bash
sudo ufw allow 20/tcp
sudo ufw allow 21/tcp
sudo ufw allow 990/tcp
sudo ufw allow 40000:50000/tcp
```

Luego, recarga UFW para aplicar los cambios:

```bash
sudo ufw reload
```

## Paso 6: Verificar la instalación

Para comprobar que el servidor VSFTPD está funcionando correctamente, puedes usar el siguiente comando:

```bash
sudo systemctl status vsftpd
```

La salida debería indicar que el servicio está **activo** y **corriendo**.

## Paso 7: Prueba de conexión FTP

Puedes probar el servidor FTP usando la herramienta `ftp` desde otro sistema o usando un cliente FTP como **FileZilla**.

- **Conéctate al servidor** usando la dirección IP de tu servidor Ubuntu y el puerto 21.
- Usa el nombre de usuario y contraseña de un usuario local para autenticarse.
