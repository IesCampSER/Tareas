### dnsmasq
MUY IMPORTANTE: para realizar esta práctica NO UTILICES la misma máquina que has utilizado para instalar bind9. No puedes tener dos servidores DNS funcionando en la red, sólo obtendrás problemas
#### Guía rápida para instalar dnsmasq
Tarea realizada con un Ubuntu Server 22.04 como servidor dns (IP 192.168.20.7) y Ubuntu Desktop 22.04 como cliente dns (IP 192.168.20.50)

Compruebo que tengo el cliente y el servidor en la misma red RedNat, configurados ambos con una ip estática

##### PASO 1. Instalo

```sudo apt update```
```sudo apt install dnsmasq```

Al hacer esto compruebo que el dnsmasq da un error porque escucha en el puerto 53 y ya tiene un servicio escuchando en ese puerto asi que desactivo el servicio

```sudo systemctl stop systemd-resolved```

```sudo systemctl disable systemd-resolved```

```sudo systemctl mask systemd-resolved```

##### PASO 2. Arranco

una vez desactivado arranco dnsmasq

``` sudo systemctl start dnsmasq```

compruebo status

``` sudo systemctl status dnsmasq```

![Imagen dnsmasq](/img/dnsmasq1.png)

##### PASO 3. Hago maestro al servidor dnsmasq

Edita el archivo /etc/dnsmasq.conf y añade un dominio personalizado que será “asir.org”

domain=asir.org

descomenta

expand-host

Modifico el archivo /etc/hosts del servidor para hacer el dns master

![Imagen dnsmasq](/img/dnsmasq2.png)

reinicio el servidor dnsmasq

``` sudo systemctl restart dnsmasq```

##### PASO 4. Compruebo

Voy al cliente y modifico su servidor DNS para que sea el 192.168.20.7 que acabo de configurar

En el cliente hago un nslookup a alguno de los registros que he dado de alta

![Imagen dnsmasq](/img/dnsmasq3.png)

##### PASO 5. Activo el servidor DHCP del dnsmasq

Si quieres puedes configurar el servidor dhcp del dnsmasq para que dé direcciones en el rango 192.168.20.100-192.168.20.110.

para ello añade la linea:

dhcp-range=192.168.20.100,192.168.20.110,24h

Después de configurar reinicia el servicio

Aquí dejo también varios enlaces de la instalación y configuración de dnsmasq, la versión del S.O en estos enlaces es más antigua ya depende de la distribución que tengáis cada uno, no se trata de que seguir al pie de la letra uno de ellos, sino de mirar en esta tarea lo que pido y buscar en cualquiera de ellos (o en la red) cómo hacerlo.

- http://recursostic.educacion.es/observatorio/web/gl/software/software-general/638-servidor-dns-sencillo-en-linux-con-dnsmasq

- https://www.guia-ubuntu.com/index.php/Dnsmasq,_servidor_DNS_y_DHCP

- https://wiki.archlinux.org/index.php/Dnsmasq_(Espa%C3%B1ol)

#### ACTIVIDAD 1

Queremos instalar un servidor DNS caché en nuestra intranet que nos permita gestionar los nombres de las máquinas y recursos de nuestra red. Las características del servidor DNS que queremos instalar son las siguientes:

El servidor es un servidor Linux con una IP fija, la IP ha de ser acorde con tu red local. En nuestro caso usaremos una máquina que puede ser una copia de SerLApellido (antes de instalar bind9) con la IP: 192.168.20.7

##### PASO 0.

Comprueba que tienes el cliente y el servidor en la misma red RedNat, configurados ambos con una ip estática
- el equipo SerLApellido tendrá como servidor DNS el de tu operador o el de google (puedes configurarlo desde los archivos o desde el NetworkManager, nunca desde los dos)
- el equipo cliente tendrá como servidor DNS el 192.168.20.7

Los dos equipos se ven entre ellos y puedes desde el servidor salir a internet

Haz una consulta desde el servidor a una web externa (por ej www.marca.com) para ver el tiempo que tarda en contestar a una petición (guarda la pantalla). Ejemplo $dig marca.com

##### PASO 1.
Instala Dnsmasq en el servidor

##### PASO 2.
Arranca el servidor Dnsmasq

##### PASO 3.
Solicita desde el cliente una petición por ejemplo nslookup www.iescamp.es

##### PASO 4.
Convierte ahora el servidor dnsmask en maestro:

Convierte ahora el servidor dnsmask en maestro:

- añade un dominio personalizado que será “asir.org”
- incluye las ip de tus pc’s
- reinicia el servidor dns y comprueba que puedes solicitar la ip de un equipo del dominio haz un nslookup:

nslookup pc1.asir.org

nslookup pc1

ambas peticiones deberían devolverte el mismo resultado

##### PASO 5.
Ahora configura el servidor dhcp del dnsmasq para que de direcciones en el rango 192.168.20.100-192.168.20.110. Después de configurar reinicia el servicio

##### PASO 6.
Configura la red del cliente para que obtenga una ip de forma automática, reinicia el cliente y comprueba que obtiene una ip del servidor dhcp que acabas de configurar

##### PASO 7.
Realiza la misma petición que en el paso 0 y comprueba si el tiempo de respuesta es inferior

#### Para entregar:

Desde el servidor:

- Consulta a una web externa para ver el tiempo que tarda en contestar (paso 0)
- archivo de configuración dnsmasq.conf, resaltando los parámetros que has necesitado modificar para cada paso de la práctica

Desde el cliente

- nslookup iescamp.es
- nslookup pc1.asir.org
- nslookup pc1
- dig www.marca.com (la misma que hayas utilizado en el paso 0 para ver que el servidor DNS hace la función de caché)
- configuración de ip automática
- ip obtenida del servidor dhcp de dnsmasq
