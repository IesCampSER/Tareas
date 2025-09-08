## **ACTIVIDAD 1. OBLIGATORIA. Configuración del servicio DHCP en Linux**

**Objetivo**: En esta práctica vamos a instalar y configurar un servidor DHCP en Linux. A continuación se configurará algún cliente para comprobar su funcionamiento. Utilizaremos el servidor con la IP 192.168.20.5

Instalamos el paquete isc-dhcp-server para que se convierta en un servidor DHCP: Descargamos el paquete para instalar el servidor DHCP con el comando:

``$sudo apt install isc-dhcp-server``

Una vez instalado, se debe de configurar para que funcione correctamente, modificando el archivo /etc/dhcp/dhcpd.conf y configurando los parámetros que el servidor DHCP empleará a la hora de distribuir direcciones IP a los clientes que encuentre. Los parámetros típicos a modificar son: nombre de dominio, rango de direcciones IP, dirección de Broadcast, puerta de enlace y los tiempos de liberación de IP.

El archivo de configuración /etc/dhcp/dhcpd.conf consta de una primera parte principal donde se especifican los parámetros generales que definen el 'alquiler' y los parámetros adicionales que se proporcionarán al cliente.

El resto del archivo de configuración consta de una serie de secciones que especifican principalmente rangos de direcciones IPs que serán cedidas a los clientes que lo soliciten (sección subnet) y especificaciones concretas de equipos (sección host). Los parámetros de las secciones deberán ir entre llaves '{' y '}'.

Dicho de otra manera, los valores de los parámetros especificados al principio del archivo se aplican como valores por defecto al resto de secciones aunque si dentro de una sección se redefine alguno de los parámetros, se aplicará éste ignorándose el valor por defecto.

Los rangos de direcciones IP se especifican en secciones que empiezan con la palabra clave 'subnet' seguido de la dirección de red de la subred, continua con la palabra 'netmask' seguido de la máscara de red. A continuación estará la lista de parámetros para dicha sección encerrados entre llaves.

**Atención**: El rango de cesión debe pertenecer a la misma subred a la que pertenece la IP del servidor, es necesario para que los clientes puedan comunicarse con el servidor DHCP para procesar las renovaciones. Ejemplo, si un servidor tiene la IP 192.168.1.1/24, no puede ceder direcciones del rango 10.0.0.0/8 porque dicho rango está fuera del alcance de la subred del servidor.

El  archivo /etc/dhcp/dhcpd.conf  es  bastante  largo. A partir de  la línea  30 aparecen declaraciones de subnet comentadas con #, pues a partir de: log-facility local7; podemos dar de alta la sección subnet donde queramos.

Al indicar una sección Subnet tenemos que indicar la dirección de la red y la mascara de red. A continuación y entre llaves podemos poner los siguientes parámetros:

- *range*: Indicamos el rango de direcciones IP que vamos a asignar. Por ejemplo entre la 192.168.20.100 y la 192.168.20.150 (son ips que no coinciden con ninguna ip fija

de nuestros servidores).

- *routers*: indicar cuál es la ip de tu router (recuerda que es el router virtual del virtualBox, compruebalo, normalmente será 192.168.20.1)
- *subnet-mask*: la máscara de red es 255.255.255.0
- *domain-name-servers*: indicaremos cuales son nuestros servidores dns, en nuestro caso son el nuestro: 192.168.20.5 y separado por una coma podemos poner el

  8\.8.8.8 (servidor dns de google) por si fallara el nuestro.
- *domain-name*: nuestro nombre de dominio será: "sercamp.org"

### EJEMPLO 1

---

\# A slightly different configuration for an internal subnet. subnet 192.168.20.0 netmask 255.255.255.0 { range 192.168.20.100 192.168.20.150;

option domain-name-servers 192.168.20.5;

option domain-name "sercamp.org";

option routers 192.168.20.1;

option broadcast-address 192.168.20.255; default-lease-time 600;

max-léase-time 7200;

}

---

Comentar las opciones default-lease-time y max-lease-time:

Un cliente puede especificar la cantidad de tiempo durante el cual el alquiler de la IP será valido.  Por  ejemplo  un  cliente  linux  dhcp  tiene  su  fichero  de  configuración  en /etc/dhcp/dhclient.conf

Y tiene un apartado:

#send dhcp-lease-time 3600;

En donde se puede especificar el lease-time siempre y cuando no supere el max-lease-time definido por el servidor DHCP. Si el cliente no define el dhcp-lease-time se aplica el default- lease-time del servidor dhcp.

Existe la posibilidad de establecer una configuración concreta a un cliente concreto identificándolo por la dirección MAC de su tarjeta de red. Recordemos que la dirección MAC (MAC address) es un número único, formado por 6 octetos, grabado en la memoria ROM de las tarjetas de red ethernet y viene fijado de fábrica. Se suelen escribir los 6 octetos en hexadecimal separados por dos puntos ':'. Todas las tarjetas de red tienen una dirección MAC única en el mundo. Es como un número de serie. Los tres primeros octetos indican el fabricante y los tres siguientes el número de serie en fabricación. En Linux se puede averiguar la dirección MAC mediante el comando ifconfig. En Windows se puede utilizar el comando ipconfig.

Para establecer una configuración de equipo es necesario crear una sección host. Ejemplo, si deseamos que el cliente cuya dirección MAC sea 00:0c:29:c9:46:80 se configure siempre (reserva de dirección IP) con la dirección IP 192.168.1.50 y puerta de enlace 192.168.1.1, que su nombre de dominio sea "sercamp.org", la sección host que debemos crear será:

### EJEMPLO 2

---

// Crear una reserva de dirección IP

host lourdesP {

hardware ethernet 00:0c:29:c9:46:80;

fixed-address 192.168.20.50;

option subnet-mask 255.255.255.0; # Máscara por defecto para los clientes  option routers 192.168.20.1;

option domain-name "sercamp.org";

}

---

Cuando el PC cuya dirección MAC sea '00:0c:29:c9:46:80' solicite una dirección IP al servidor DHCP, recibirá la 192.168.20.50. Y se configurarán automáticamente el resto de opciones indicadas.

**Es una buena práctica: que antes de modificar un archivo de configuracion hagamos una copia del mismo:**

$sudo cp archivo\_en\_cuestion archivo\_en\_cuestion.bak

Con estos datos conseguimos que se efectue automáticamente la configuración de red y de dominio de nuestros clientes. Para ello en el cliente haz lo necesario para que reciba la IP dinámicamente.

Para que se hagan efectivos los cambios, es necesario reiniciar el servicio DHCP. Para ello, se empleará el comando

```
systemctl restart isc-dhcp-server
```

en distribuciones más antiguas

```
isc-dhcp-server restart
```

```
/etc/init.d/isc-dhcp-server restart
```

para reiniciar el demonio. Una vez reiniciado el servicio, se comprueba que está funcionando el Servidor DHCP en los clientes.

## **PARA ENTREGAR. Capturas de pantalla de:**

**Del cliente:**

- un  terminal  con  el  comando  ipconfig/ifconfig/ip  address  show  ejecutado,  para comprobar la configuración que ha obtenido del servidor.

**Del servidor:**

- Captura de pantalla del contenido de log del sistema filtrando las búsquedas con grep para ver las concesiones del DHCP

```
tail -f /var/log/syslog | grep dhcp
```

- Captura de pantalla con el status del servidor:
  ```
  service isc-dhcp-server status
  ```

## **ACTIVIDAD  2. OPCIONAL.**

**Configuración del servicio DHCP en Windows Server**

En el enlace tenéis la configuración del servidor DHCP para windows server 2012, sólo debéis cambiar las direcciones por las de nuestro escenario de prácticas

SERVIDOR DE WINDOWS: 192.168.20.6

CLIENTE: obtendrá un ip en el rango 192.168.20.100-192.168.20.150 

## **PARA ENTREGAR. Capturas de pantalla de:**

**Del cliente:** un terminal con el comando ipconfig ejecutado, para comprobar la configuración que ha obtenido del servidor. Recuerda que el cliente debe obtener la ip dinámicamente.

**Del servidor:** la ventana de configuración del servidor DHCP, dentro de la carpeta: *Ámbito*, selecciona la carpeta: *Concesiones de direcciones*, donde se vea la concesión que se ha otorgado.

Reserva ahora una dirección concreta al cliente según su MAC y entrega

**Del cliente:** un terminal con el comando ipconfig ejecutado, para comprobar la configuración que ha obtenido del servidor. Recuerda que el cliente debe obtener la **ip** dinámicamente.

**Del servidor:** la ventana de configuración del servidor DHCP, dentro de la carpeta: *Ámbito*, selecciona la carpeta: *Reservas*, donde se vea que la dirección obtenida por el cliente es la que se le ha configurado en este apartado al servidor.
