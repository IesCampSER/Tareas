# Práctica: Servidor DNS con Pi-hole y Unbound

## Objetivo

Configurar un servicio DNS local utilizando **Pi-hole** como servidor DNS para los equipos de la red y **Unbound** como resolvedor DNS recursivo.

La práctica permitirá comprender el funcionamiento de la resolución DNS y la relación entre un servidor DNS utilizado por los clientes y un resolvedor recursivo.

## Escenario

Disponemos de una máquina Ubuntu Server 24.04 que proporcionará servicios DNS a una red local.

La máquina tendrá la siguiente configuración:

* **Sistema operativo:** Ubuntu Server 24.04 LTS
* **Dirección IP:** `192.168.10.33`
* **Pi-hole:** servidor DNS para los clientes
* **Unbound:** resolvedor DNS recursivo local
* **Puerto de Unbound:** `5335`
* **Puerto DNS de Pi-hole:** `53`

Los equipos de la red deberán utilizar la dirección `192.168.10.33` como servidor DNS.

La arquitectura final será:

```text
             CLIENTES DE LA RED
                     │
                     │ DNS :53
                     ▼
              ┌─────────────┐
              │   Pi-hole   │
              │   DNS :53   │
              └──────┬──────┘
                     │
                     │ DNS :5335
                     ▼
              ┌─────────────┐
              │   Unbound   │
              │ Recursivo   │
              └──────┬──────┘
                     │
                     ▼
              Servidores DNS
                 autoritativos
```

## Tareas

### 1. Instalación

Instala y configura Pi-hole y Unbound en Ubuntu Server 24.04.

Documenta los paquetes, servicios y archivos de configuración utilizados.

### 2. Configuración de Unbound

Configura Unbound como resolvedor DNS recursivo local.

Debe:

* Escuchar en el puerto `5335`.
* Aceptar consultas procedentes de Pi-hole.
* Utilizar caché DNS.
* Realizar consultas recursivas.
* No utilizar Google DNS, Cloudflare ni otros DNS públicos como reenviadores.

Comprueba que Unbound funciona correctamente realizando consultas DNS directamente contra él.

### 3. Configuración de Pi-hole

Configura Pi-hole para:

* Escuchar las peticiones DNS de los equipos de la red.
* Utilizar Unbound como servidor DNS ascendente.
* Utilizar el puerto `5335` de Unbound.
* Activar una lista de bloqueo DNS.

### 4. Configuración de los clientes

Configura al menos un equipo cliente para utilizar:

```text
Servidor DNS: 192.168.10.33
Puerto DNS: 53
```

Comprueba que las consultas DNS del cliente son atendidas por Pi-hole.

### 5. Comprobación de la resolución DNS

Utiliza herramientas como `dig` o `nslookup` para comprobar:

* Resolución de nombres.
* Dirección IP obtenida para diferentes dominios.
* Tiempo de respuesta.
* Funcionamiento de la caché.
* Consultas realizadas desde el cliente.

Realiza, como mínimo, varias consultas a dominios diferentes y documenta los resultados.

### 6. Comprobación del bloqueo

Comprueba el funcionamiento del filtrado DNS de Pi-hole accediendo a algún dominio incluido en las listas de bloqueo.

Consulta posteriormente la interfaz web de Pi-hole para comprobar que la petición ha sido registrada y bloqueada.

### 7. Análisis del funcionamiento

Explica el recorrido que realiza una consulta DNS desde que un usuario introduce un dominio en su navegador hasta que obtiene la dirección IP correspondiente.

En particular, explica qué función desempeña cada uno de estos elementos:

* Cliente DNS.
* Pi-hole.
* Caché de Pi-hole.
* Unbound.
* Servidores DNS raíz.
* Servidores TLD.
* Servidor DNS autoritativo.

### 8. Pruebas y diagnóstico

Realiza pruebas que permitan identificar en qué punto se produce un problema DNS.

Por ejemplo:

* Detener Unbound y comprobar qué ocurre.
* Detener Pi-hole y comprobar qué ocurre.
* Realizar una consulta directamente contra Unbound.
* Realizar una consulta contra Pi-hole.
* Comprobar los puertos que están escuchando.
* Consultar los registros de los servicios.

Documenta las pruebas realizadas y explica los resultados.

## Entrega

La entrega deberá incluir:

1. Configuración de red utilizada.
2. Instalación y configuración de Pi-hole.
3. Instalación y configuración de Unbound.
4. Configuración DNS del cliente.
5. Pruebas realizadas con `dig` o `nslookup`.
6. Capturas de la interfaz de Pi-hole.
7. Comprobación del bloqueo DNS.
8. Explicación del recorrido de una consulta DNS.
9. Pruebas de diagnóstico realizadas.
10. Conclusiones sobre el funcionamiento de la solución.

No se valorará únicamente que el servicio funcione. Se deberá demostrar que se comprende **qué función desempeña Pi-hole, qué función desempeña Unbound y cómo intervienen ambos en la resolución DNS**.
