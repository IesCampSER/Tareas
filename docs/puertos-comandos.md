# Comandos para verificar los puertos abiertos en Ubuntu 22.04

A continuación se presentan algunos comandos útiles para revisar los puertos abiertos en Ubuntu 22.04.

## 1. Usando ss

El comando ss es una herramienta poderosa para mostrar sockets y conexiones de red.

``` sudo ss -tuln ```

-t: Muestra conexiones TCP.

-u: Muestra conexiones UDP.

-l: Muestra solo puertos en escucha (listening).

-n: No resuelve los nombres de los servicios o direcciones.

## 2. Usando netstat (si está disponible)

El comando netstat puede no estar instalado por defecto, pero puedes instalarlo con net-tools si lo necesitas.
Instalar netstat:

```sudo apt install net-tools```

Comando para verificar puertos abiertos:

```sudo netstat -tuln```

-t: Muestra conexiones TCP.

-u: Muestra conexiones UDP.

-l: Muestra puertos en escucha (listening).

-n: No resuelve nombres.

## 3. Usando nmap

El comando nmap es una herramienta de escaneo de red que permite verificar qué puertos están abiertos.
Instalar nmap:

```sudo apt install nmap```

Escanear puertos locales:

```sudo nmap -sT -O localhost```

-sT: Realiza un escaneo completo de puertos TCP.

-O: Intenta detectar el sistema operativo.

## 4. Usando lsof

El comando lsof lista los archivos abiertos, incluidos los puertos.

```sudo lsof -i -P -n | grep LISTEN```

-i: Muestra conexiones de red.

-P: Muestra números de puerto en lugar de nombres.

-n: No resuelve nombres de host.

Utiliza cualquiera de estos comandos dependiendo de tus necesidades para identificar puertos abiertos en tu sistema Ubuntu 22.04.
