 PARA REVISAR, PROBLEMAS EN ENVIO DE CORREOS EXTERNOS, faltan imagenes..
# Instalación de Postfix en Ubuntu 22.04 ó 24.04

Postfix es un servidor de correo electrónico popular, usado ampliamente por su facilidad de configuración y rendimiento. A continuación, se describen los pasos para instalar y configurar Postfix en Ubuntu 22.04 o 24.04

---

## Requisitos previos

Configuraciones previas:

- VirtualBox- RedNat 192.168.20.0/24; dhcp desactivado
- Máquina virtual con Ubuntu server 22.04 ó 24.04, ip estática 192.168.20.5/24
- Instalado y corriendo servidor dns
- dominio sercamp.org
- Máquina virtual con Ubuntu Desktop 22.04, ip 192.168.20.21

---

## Paso 1: Instalación de Postfix

1. Instala Postfix desde los repositorios oficiales de Ubuntu ejecutando:

```bash
sudo apt install postfix -y
```

2. Durante la instalación, aparecerá un menú de configuración. Selecciona las siguientes opciones:
   - **General type of mail configuration:** Elije `Internet Site`.
   - **System mail name:** Introduce tu nombre de dominio principal  `sercamp.org`.

3. Si no aparece el menú de configuración, puedes iniciarlo manualmente con:
```bash
sudo dpkg-reconfigure postfix
```

4. Comprueba el servicio
```bash
sudo service postfix status
```

5. Comprueba puertos 
```bash
sudo ss -plnut
```
debes tener escuchando el puerto 25 del servidor de correo

---

## Paso 2: Configuración básica

1. Edita el archivo principal de configuración de Postfix, (recuerda hacer una copia del mismo antes de modificarlo):

```bash
sudo nano /etc/postfix/main.cf
```

2. Verifica o actualiza las siguientes configuraciones:

- Configuramos el nombre del servidor de correo (es el que hemos configurado en el DNS):
myhostname = mail.sercamp.org

- Configuramos en mydestination los dominos de los que vamos aceptar correos entrantes.
mydestination = $myhostname, sercamp.org, localhost.localdomain, localhost

- En el campo mynetworks se configura la red en la que tenemos clientes que pueden enviar correos desde este servidor postfix, tenemos que añadir la subred 192.168.20.0/24

- El campo myorigin configura lo que añade el servidor de correo para los correos originados en este servidor(lo que va a la derecha de la @), en nuestro caso será sercamp.org

```bash
myhostname = mail.sercamp.org
mydomain = sercamp.org
myorigin = /etc/mailname
inet_interfaces = all
inet_protocols = ipv4
mydestination = $myhostname, sercamp.org, localhost.localdomain, localhost
relayhost =
mynetworks = 192.168.20.0/24 127.0.0.0/8 [::1]/128
mailbox_size_limit = 0
recipient_delimiter = +
```

3. Guarda y cierra el archivo `Ctrl+O`, `Enter` y luego `Ctrl+X`.

4. Reinicia Postfix para aplicar los cambios:

```bash
sudo systemctl restart postfix
```
Haz un cat del archivo /etc/mailname. Debe salir tu dominio

5. Comprueba el servicio

```bash
sudo service postfix status
```
---

## Paso 3: Prueba de funcionamiento -- Sendmail en el servidor -- Envío local
Sendmail es una herramienta que se instala con la instalación de postfix y nos permite enviar
correos desde la línea de comandos.
Averigua con sudo postconf mail_spool_directory la carpeta donde se almacenan los correos.

La salida del comando es mail_spool_directory = /var/mail

Recién instalado postfix se utiliza como sistema de almacenamiento de correos, mailbox.

Con este sistema, todos los correos de un usuario se almacenan en el mismo fichero dentro de
/var/mail. Se crea un fichero por cada usuario.

1. Usa el comando `mail` para enviar un correo de prueba (instala `mailutils` si no está disponible):

```bash
sudo apt install mailutils -y

echo "Este es un correo de prueba" | mail -s "Prueba Postfix" usuario@sercamp.org
```

Sustituye ```usuario``` por uno de los usuarios del sistema que tengas en el servidor

2. Revisa los registros de Postfix para confirmar que el correo fue enviado:

```bash
sudo tail -f /var/log/mail.log
```
3. Revisa el correo del usuario. En la carpeta /var/mail haz un cat usuario 
4. Envia otro correo y comprueba que se alamcena en el mismo archivo

## Paso 4: Prueba de funcionamiento -- Sendmail en el servidor – Cuenta externa

Envío de correo desde el servidor con el usuario a una cuenta externa. Las cuentas externas de gmail y outlook no funcionan por las propias restricciones de seguridad de estas compañias pero si damos de alta una cuenta en otro servidor por ejemplo temp-mail.org podemos comprobar el funcionamiento

sendmail lijomi3842@luxyss.com

Subject: Escribes aquí el asunto

Escribes todo lo que quieras enviar.

Sigues escribiendo.

Para enviarlo pulsas Ctrl+d

## Paso 5: Cambiar mailbox por maildir

MailDir es una forma distinta de almacenar los correos. Al cambiar el sistema de almacenamiento de los correos de mailbox a maildir, se crea una carpeta Maildir dentro del directorio personal de cada usuario. En esa carpeta es donde se van a guardar los correos y cada correo se guarda como un archivo diferente, en vez de todos en el mismo archivo.

Cambiar la configuración de postfix.

- Editar el archivo /etc/postfix/main.cf y añadir la línea: 

```home_mailbox_ = Maildir/```

- Reiniciar servicio

```sudo service postfix restart``` 

```sudo postconf -n```

Envia otro correo y comprueba que ahora lo almacena maildir y está en un archivo diferente


imagen

teclea: ```mail usuario@sercamp.org ``` te aparecerán los campos para que rellenes asunto, cuerpo..

```Ctrl+d para enviar.```

una vez has enviado el mensaje puedes consultarlo con mail. Mira la pantalla de ejemplo verás que hay 4 mensajes enviados si pulsas el número, en mi caso el 4, te aparece el desglose del mensaje.

## Paso 6: Instalar dovecot con protocolo IMAP

POSTFIX tiene la funcionalidad de MTA(agente de transferencia de correo), desde el punto de vista de un usuario de sercamp.org, es el que permite enviar correos y que lleguen a su destino.
DOVECOT Dovecot es un MDA que tiene por función almacenar el correo y servirlo mediante POP3 o IMAP4
al programa cliente de correo.


Instalar el paquete que permite utilizar IMAP4
```bash
sudo sudo apt install dovecot-imapd
```

Comprueba protocolos activos
```bash
sudo cat /usr/share/dovecot/protocols.d/*.protocol
```
Mostrará protocols = $protocols imap

Para la práctica no se van configurar certificados de seguridad.

Se comprueba la configuración en /etc/dovecot/conf.d/10-ssl.conf

- ssl = no

Comenta las lineas:

- ssl_cert

- ssl_key

Modifica el archivo 10-auth.conf
- disable_plaintext_auth = no
- auth_mechanisms = plain login


Configurar para el uso de maildir en el archivo 10-mail.conf

descomenta la linea:
- mail_location=maildir:~/Maildir

comenta la linea
-  mail_location = mbox:~/mail:INBOX=/var/mail/%u



Reiniciar servicio ```sudo service dovecot restart```

## Paso 7: Prueba desde un ordenador cliente con Thunderbird


Configuración de cuenta:

- Nombre del servidor: mail.sercamp.org

- Nombre de usuario: usuario@sercamp.org

siendo usuario uno de los usuarios del sistema que tienes en el servidor, su contraseña será la contraseña del sistema

Servidor saliente:

- Nombre del servidor: mail.sercamp.org

- Nombre de usuario: usuario@sercamp.org

Protocolo: IMAP

Puerto: 143

Nos saldrá una advertencia de que el servidor no usa cifrado, pulsamos en ```Entiendo los riesgos``` y ```Confirmar```


## Comprobación del estado de Postfix

Verifica que Postfix esté activo y funcionando correctamente:

```bash
sudo systemctl status postfix
```

Si todo está configurado correctamente deberías poder enviar y recibir correos desde tu cliente thunderbird entre los usuarios de correo que tienes en el servidor
