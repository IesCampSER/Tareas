# Instalación de Postfix en contenedor docker 
Postfix es un servidor de correo electrónico popular, usado ampliamente por su facilidad de configuración y rendimiento. A continuación, se describen los pasos para instalar y configurar Postfix en un contenedor ubuntu 20

---

## Requisitos previos

Configuraciones previas:

- Docker instalado en la máquina
- Instalado y corriendo servidor dns en el mismo contenedor, otro de la red o una máquina en la red
- dominio sercamp.org

---
Vamos a instalar un servidor de correo pero esta vez en lugar de utilizar un máquina virtual lo haremos en un contenedor docker.  
## Paso 0: Creación del contenedor  
Descarga la imagen de Ubuntu:20.04  
$ docker pull ubuntu:20.04  
Crea un contenedor y ejecutalo exponiendo el puerto 25 (SMTP):  

![Imagen bind](/img/correo1.png)

Abre un terminal dentro del contenedor:  
```bash
docker exec -it postfix /bin/bash
```
## Paso 1: Instalación de Postfix

1. Instala Postfix desde los repositorios oficiales de Ubuntu ejecutando:

```bash
apt install postfix -y
```

2. Durante la instalación, aparecerá un menú de configuración. Selecciona las siguientes opciones:
   - **General type of mail configuration:** Elije `Internet Site`.
   - **System mail name:** Introduce tu nombre de dominio principal  `sercamp.org`.  
![Imagen bind](/img/correo2.png)  
![Imagen bind](/img/correo3.png)  
![Imagen bind](/img/correo4.png)
  
mydestination = $myhostname, sercamp.org, localhost.localdomain, localhost  
  
![Imagen bind](/img/correo6.png)
![Imagen bind](/img/correo7.png)
![Imagen bind](/img/correo8.png)
![Imagen bind](/img/correo9.png)
![Imagen bind](/img/correo10.png)
![Imagen bind](/img/correo11.png)
![Imagen bind](/img/correo12.png)
![Imagen bind](/img/correo13.png)

3. Si no aparece el menú de configuración, puedes iniciarlo manualmente con:
```bash
dpkg-reconfigure postfix
```

4. Comprueba el servicio
```bash
service postfix status
```
Si no está arrancado inicialo
```bash
service postfix start
```
5. Puedes comprobar puertos por ejemplo con netstat (no estará instalado deberás hacerlo) 
debes tener escuchando el puerto 25 del servidor de correo
---  
Agrega tu usuario al contenedor  
```bash
useradd -m lourdes && echo "lourdes:qwe_123" | chpasswd;
```

## Paso 2: Configuración básica

1. Edita el archivo principal de configuración de Postfix, (recuerda hacer una copia del mismo antes de modificarlo):

```bash
nano /etc/postfix/main.cf
```

2. Verifica o actualiza las siguientes configuraciones:

- Configuramos el nombre del servidor de correo (es el que hemos configurado en el DNS):
myhostname = mail.sercamp.org

- Configuramos en mydestination los dominos de los que vamos aceptar correos entrantes.
mydestination = $myhostname, sercamp.org, localhost.localdomain, localhost

- En el campo mynetworks se configura la red en la que tenemos clientes que pueden enviar correos desde este servidor postfix, añadimos 127.0.0.0/8

- El campo myorigin configura lo que añade el servidor de correo para los correos originados en este servidor(lo que va a la derecha de la @), en nuestro caso será sercamp.org

```bash
myhostname = mail.sercamp.org
mydomain = sercamp.org
myorigin = /etc/mailname
inet_interfaces = all
inet_protocols = ipv4
mydestination = $myhostname, sercamp.org, localhost.localdomain, localhost
relayhost =
mynetworks = 127.0.0.0/8 [::1]/128
mailbox_size_limit = 0
recipient_delimiter = +
```

3. Guarda y cierra el archivo `Ctrl+O`, `Enter` y luego `Ctrl+X`.

4. Reinicia Postfix si has realizado cambios:

Haz un cat del archivo /etc/mailname. Debe salir tu dominio

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
apt install mailutils -y

echo "Este es un correo de prueba" | mail -s "Prueba Postfix" usuario@sercamp.org
```

Sustituye ```usuario``` por uno de los usuarios del sistema que tengas en el servidor

2. Revisa los registros de Postfix para confirmar que el correo fue enviado:

```bash
tail -f /var/log/mail.log
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

![Imagen bind](/img/correo21.png)  
![Imagen bind](/img/correo22.png)  

## Paso 5: Cambiar mailbox por maildir

MailDir es una forma distinta de almacenar los correos. Al cambiar el sistema de almacenamiento de los correos de mailbox a maildir, se crea una carpeta Maildir dentro del directorio personal de cada usuario. En esa carpeta es donde se van a guardar los correos y cada correo se guarda como un archivo diferente, en vez de todos en el mismo archivo.

Cambiar la configuración de postfix.

- Editar el archivo /etc/postfix/main.cf y añadir la línea: 

```home_mailbox_ = Maildir/```

- Reinicia el servicio

```service postfix restart``` 

```postconf -n```

Envia otro correo y comprueba que ahora lo almacena maildir y está en un archivo diferente

teclea: ```mail usuario@sercamp.org ``` te aparecerán los campos para que rellenes asunto, cuerpo..

```Ctrl+d para enviar.```

una vez has enviado el mensaje puedes consultarlo con mail. Si hay varios mensajes enviados pulsas el número y te aparece el desglose del mensaje.

## Paso 6: Instalar dovecot con protocolo IMAP

POSTFIX tiene la funcionalidad de MTA (agente de transferencia de correo), desde el punto de vista de un usuario de sercamp.org, es el que permite enviar correos y que lleguen a su destino.
DOVECOT Dovecot es un MDA que tiene por función almacenar el correo y servirlo mediante POP3 o IMAP4 al programa cliente de correo.

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

Reinicia servicio  
```sudo service dovecot restart```

## Paso 7: Prueba desde un ordenador cliente con Thunderbird

Para realizar este paso tienes que tener como servidor DNS nuestro servidor de clase ya que thunderbird va a comprobar que existe el dominio sercamp.org y el registro MX correspondiente, así que abre thuderbird en una de las máquinas virtuales que tenga como servidor DNS nuestro servidor de siempre y por supuesto arranca también el propio servidor.  
Además, para que funcione esta configuraciónhe hay que actualizar la IP del servidor donde apunta el dominio mail.sercamp.org en el servidor DNS. La nueva dirección IP es la máquina HOST donde hemos montado el docker con postfix.

Configuración de cuenta:

- Nombre del servidor: mail.sercamp.org

- Nombre de usuario: usuario@sercamp.org

siendo usuario uno de los usuarios del sistema que tienes en el servidor, su contraseña será la contraseña del sistema

Servidor saliente:

- Nombre del servidor: mail.sercamp.org

- Nombre de usuario: usuario@sercamp.org

Protocolo: IMAP

Puerto: 143

Nos saldrá una advertencia de que el servidor no usa cifrado, pulsamos en ```Entiendo los riesgos``` y ``Confirmar```


## Comprobación del estado de Postfix

Verifica que Postfix esté activo y funcionando correctamente:

Si todo está configurado correctamente deberías poder enviar y recibir correos desde tu cliente thunderbird entre los usuarios de correo que tienes en el servidor
