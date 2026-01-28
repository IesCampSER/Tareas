## Tarea DNS bind

Queremos instalar un servidor DNS local en nuestra intranet que nos permita gestionar los nombres de las máquinas y recursos de nuestra red. Las características del servidor DNS que queremos instalar son las siguientes:

El servidor es el servidor Linux con una IP fija, la IP ha de ser acorde con tu red local. En nuestro caso usaremos la máquina configurada en la primera quincena SerLApellido con la IP: 192.168.20.5

### PASO 1. Instalar bind en el servidor.
```
apt install bind9
```
Recuerda: en ocasiones aparecen problemas en la instalación de paquetes porque no tenemos actualizada la base de datos:
```
apt update
```
### PASO 2. Configurar archivos de bind9

Los ficheros de configuración de bind están en el directorio /etc/bind.

Ficheros db: contienen 3 zonas inversas: 0.in-addr.arpa, 127.in-addr.arppa y 255.in-addr.arpa. Estas tres zonas están configuradas y hacen referencia al nodo local. Además contiene una zona directa vacía db.empty que usaremos como punto de partida.

Ficheros named: hacen referencia a los ficheros de configuración del servidor. Podría configurarse todo en el fichero named.conf pero no se hará así, el fichero named.conf no se debe tocar, editaremos el named.conf.options para configurar.

Importante señalar que el proceso del servidor DNS se llama named y se ejecuta como el usuario named.

Editar el archivo /etc/bind/named.conf.options y en la sección forwarders añadir las IPs de dos servidores DNS (por ejemplo los proporcionados por tu proveedor de Internet) donde redirigir las peticiones DNS.

Recuerda que para editar existen varios programas: nano, vi, gedit
```
$sudo nano /etc/bind/named.conf.options
```
![Imagen bind](/img/dns1.png)

Con esta opción se especifica que las consultas que no pueda responder nuestro servidor sean reenviadas a la IP 8.8.8.8.

Comenta la línea que pone dnssec-verification auto; o pon no en vez de auto para que funcione la resolución de nombres públicos de Internet,

Edita el archivo /etc/bind/named.conf y comenta la linea que hace referencia a named.conf.default-zones.

El servidor DNS tiene muchas más opciones que permiten ajustar la configuración.

### PASO 3. Crear una zona primaria. Vamos a crear la zona sercamp.org

Edita el archivo /etc/bind/named.conf.local. Ejemplo:

![Imagen bind](/img/dns2.png)

Recuerda: todas las lineas que comienzan por // son comentarios, no se procesan

Con Type especifico que voy a crear una zona primaria

Con file especifico el fichero que contiene los datos de la zona

Ahora tenemos que crear los archivos de la zona:

Para crear los archivos de zona nos copiaremos el archivo de zona db.empty:
```
cp /etc/bind/db.empty /etc/bind/sercamp.org.db
cp /etc/bind/db.empty /etc/bind/db.20.168.192
```
### PASO 4. Creación de las zonas.

Vamos a tener los siguientes FQDN:  
- El servidor DNS con la IP que tiene el servidor 192.168.20.5 se llama dns.sercamp.org
- El servidor web llamado www.sercamp.org con la IP: 192.168.20.10. Es un servidor ficticio que de momento no existe.
- Un servidor ftp que se llame ftp.sercamp.org y que está también en 192.168.20.10 (esto es ficticio)
- Servidor para recibir los correos que se llama mail.sercamp.org y que está en 192.168.20.11 (esto es ficticio)

Además queremos nombrar a tres clientes:  
- aula5pc2.sercamp.org (192.168.20.202) (ficticio)
- aula5pc3.sercamp.org (192.168.20.203) (ficticio)
- aula5pc4.sercamp.org (192.168.20.204) (ficticio)

Recuerda: Con el atributo CNAME especificamos un alias para la dirección 192.168.20.10 (la misma IP que tenga el servidor www), que atenderá a los nombres: www y ftp.

#### Vamos a crear la zona directa

Editamos el archivo /etc/bind/sercamp.org.db para definir todos los registros. Este ejemplo está incompleto, sólo es una referencia, debes completarlo con todos los registros. (Al servidor lo he llamado “dns” y quiero que las notificaciones lleguen al usuario root)

![Imagen bind](/img/dns3.png)

El carácter @ puede sustituir a la zona, es decir es lo mismo poner

sercamp.org. IN SOA dns.sercamp.org root.sercamp.org

@ IN SOA dns.sercamp.org root.sercamp.org

SOA es un registro obligatorio

NS es el servidor de nombres, aunque aparece en el SOA es necesario especificarlo de nuevo. Podría haber varios NS.

A: se han especificado dos direcciones IP, la primera se refiere al servidor de nombres, la segunda es un equipo. Los nombres se pueden especificar aquí con el FQDN o no. En el ejemplo aparecen con el FQDN pero es lo mismo poner cualquiera de estas dos cosas:

dns.sercamp.org. IN A 192.168.20.5
dns IN A 192.168.20.5

Si no lleva el punto final quiere decir que no está completamente cualificado (FQDN). A la hora de procesar el fichero es lo mismo poner

dns
dns.sercamp.org.

Es decir, si no lleva el punto final, no está completamente cualificado, así que se le añadirá el dominio en nuestro caso sercamp.org

El registro «MX» nos indica que el servidor de correo del dominio sercamp.org es mail.sercamp.org podríamos definir varios servidores de correo con la misma o distinta prioridad (10). El registro «A» de esta misma zona DNS nos indica que mail.sercamp.org. se encuentra en la IP 192.168.20.5.

No es posible apuntar los registros MX directamente a una IP. Lo que debe hacerse es definir primero un registro «A» y posteriormente apuntar el registro MX a ese registro «A».

#### Vamos a crear la zona de resolución inversa.

Editamos el archivo: /etc/bind/db.20.168.192. Ejemplo incompleto

![Imagen bind](/img/dns4.png)

### PASO 5. Comprobación de la sintaxis de los archivos mediante los comandos:

```
named-checkzone sercamp.org /etc/bind/sercamp.org.db
named-checkzone 20.168.192.in-addr.arpa /etc/bind/db.20.168.192
```

Una vez comprobado que no existen errores (en el caso de que sí existan se puede mirar el archivo /var/log/syslog), se realiza el reinicio del servicio bind9.

Distribuciones anteriores a Ubuntu 20:

- Arranque: /etc/init.d/bind9 start
- Parada: /etc/init.d/bind9 stop
- Reinicio: /etc/init.d/bind9 restart

Ubuntu 20, 22:

- Arranque: `service named start` o bien `systemctl named start`
- Parada: `service named stop` o bien `systemctl named stop`
- Reinicio: ` service named restart` o bien `systemctl named restart`
 
### PASO 6. Indicamos a nuestro servidor Linux que su servidor DNS es él mismo.

Dependiendo de si tu S.O tiene o no interfaz gráfica y de la distribución que tengas Ubuntu 20, 22, 24 deberás realizar este paso de forma diferente según el gestor de resolución de nombres que tengas: Networkmanager, Netplan...etc. Es importante que tengas claro cual de todos debes tocar, si no lo tienes claro pregunta en el foro cómo se realiza la configuración de red de tu distribución.

Adicionalmente existe un archivo (tanto en windows como en linux) llamado hosts que nos permite hacer una configuración estática nombreordenador-dirección IP. Este fichero tiene prioridad sobre las consultas DNS, si algo se encuentra aquí no se consultará al servidor DNS

### Para entregar:

- Configura el servidor DNS con los registros A, CNAME, MX y NS necesarios.
- Configura un cliente Linux de tal manera que su DNS sea el servidor que acabamos de configurar con la IP: 192.168.20.5. En este punto la misma advertencia anterior: Dependiendo de si tu S.O tiene o no interfaz gráfica y de la distribución que tengas deberás realizar este paso de forma diferente según el gestor de resolución de nombres que tengas.

En principio, como lo tenemos por DHCP, debería bastar con comprobar que la configuración es correcta.

Entrega:

captura de pantalla desde el cliente haciendo:
- $ping dns.sercamp.org
- $nslookup aula5pc2.sercamp.org
- $nslookup ftp.sercamp.org
- $dig sercamp.org
- $nslookup 192.168.20.10
  
captura de pantalla desde el servidor con cada uno de los archivos de configuración modificados para realizar la tarea. 

### IMPORTANTE

Ubuntu por defecto tiene activado un dns caché local, por tanto la resolución de nombres siempre pasa por esta caché. Si probamos con comandos como dig o nslookup veremos como el servidor que responde es el bucle local. Mira los ejemplos (son dos versiones de ubuntu):

![Imagen bind](/img/dns5.png)

![Imagen bind](/img/dns6.png)

En la imagen ves como responde el servidor 127.0.0.53 porque este dominio lo tiene almacenado en caché

Para saltarlo y pedir la petición a un servidor específico debemos pasarle como parámetro el servidor dns al que interrogamos en los comandos

dig @servnombres hostnameaconsultar. Ejemplo

dig @dns.sercamp.org ftp.sercamp.org

o bien nslookup hostnameaconsultar servnombres. Ejemplo

nslookup ftp.sercamp.org 192.168.20.5

![Imagen bind](/img/dns7.png)

Esta caché cuando configuramos nuestro servidor DNS local a veces nos juega malas pasadas, si busco un registro de mi dominio y no lo encuentra (es posible que estemos haciendo pruebas y tengamos algun error) almacena en caché que ese registro no existe, luego podemos tener solucionado el problema y volvernos locos porque sigue diciendo que no encuentra el registro. Podemos solucionar esto limpiando la caché, al igual que se ha comentado con anterioridad el comando dependerá de la distribución, os pongo aquí el ejemplo para ubuntu 20

sudo systemd-resolve --flush-caches

con este comando liberas la cache, fijaros como lo hago en mi equipo

![Imagen bind](/img/dns8.png)

primero miro con systemctl is-active systemd-resolved si mi equipo tiene la caché activada, como veis en la imagen la respuesta es que sí (active), después compruebo con systemd-resolve --statistics que el tamaño de mi caché es 164 y después con sudo systemd-resolve --flush-caches libero la caché. La próxima vez que ejecute las estadisticas en current cache size pondrá 0
