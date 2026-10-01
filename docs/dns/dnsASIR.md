# Servidor DNS con BIND9 en Ubuntu Server 24.04 LTS

## Servicios de Red · ASIR

## 1. Introducción

El **Sistema de Nombres de Dominio (DNS, Domain Name System)** es un servicio fundamental de Internet y de las redes corporativas.

Su función principal es traducir nombres de dominio y nombres de host a direcciones IP y, en determinados casos, realizar el proceso inverso.

```text
www.empresa.local  →  192.168.20.10
```

En ASIR es necesario comprender no solo la traducción de nombres, sino también la arquitectura jerárquica, resolución recursiva e iterativa, zonas, delegaciones, caché, transferencias de zona, DNSSEC y seguridad.

---

## 2. Arquitectura jerárquica de DNS

DNS tiene una estructura jerárquica:

```text
                         .
                    ROOT SERVERS
                         |
          +--------------+--------------+
          |              |              |
         .com           .es           .org
          |
       empresa
          |
     www.empresa.com
```

La jerarquía comienza en la raíz (`.`), continúa con los TLD y después con los dominios y subdominios.

Un nombre completo se denomina **FQDN (Fully Qualified Domain Name)**:

```text
www.informatica.empresa.es.
```

El punto final representa la raíz DNS.

---

## 3. Dominios, zonas y delegaciones

Un **dominio** es una parte del espacio de nombres DNS. Una **zona** es una parte de ese espacio que está administrada por uno o varios servidores DNS.

Por ejemplo:

```text
empresa.es
    |
    +--- ventas.empresa.es
    |
    +--- informatica.empresa.es
```

El administrador de `empresa.es` puede delegar `informatica.empresa.es` a otros servidores.

---

## 4. Tipos de servidores DNS

### Servidor autoritativo

Contiene información oficial sobre una zona:

```text
www.empresa.local → 192.168.20.10
```

### Servidor recursivo

Recibe una consulta de un cliente y realiza las consultas necesarias para obtener la respuesta.

```text
Cliente → DNS recursivo → Root → TLD → servidor autoritativo
```

### Servidor caché

Un servidor recursivo almacena respuestas temporalmente para evitar repetir consultas.

El tiempo de permanencia depende principalmente del **TTL (Time To Live)**.

---

## 5. Resolución recursiva e iterativa

### Consulta recursiva

El cliente solicita al servidor una respuesta completa:

```text
Cliente
   |
   | consulta recursiva
   v
DNS
   |
   +--> realiza consultas
   |
   v
respuesta final
```

### Consulta iterativa

El servidor devuelve la mejor información que conoce, normalmente una referencia a otro servidor:

```text
DNS → Root → servidores .com → servidores example.com
```

---

## 6. Resolución directa e inversa

La resolución directa transforma un nombre en una IP:

```text
www.empresa.local → 192.168.20.10
```

La resolución inversa transforma una IP en un nombre:

```text
192.168.20.10 → www.empresa.local
```

Para IPv4 se utilizan zonas bajo:

```text
in-addr.arpa
```

Para IPv6:

```text
ip6.arpa
```

---

## 7. Principales registros DNS

### A

IPv4:

```dns
www    IN    A    192.168.20.10
```

### AAAA

IPv6:

```dns
www    IN    AAAA    2001:db8::10
```

### CNAME

Alias:

```dns
web    IN    CNAME    www.empresa.local.
```

### MX

Servidores de correo:

```dns
@    IN    MX    10    mail.empresa.local.
```

Un número menor representa una prioridad mayor.

### NS

Servidores autoritativos:

```dns
@    IN    NS    ns1.empresa.local.
@    IN    NS    ns2.empresa.local.
```

### SOA

Contiene información administrativa de la zona:

```dns
@ IN SOA ns1.empresa.local. admin.empresa.local. (
        2026100101 ; Serial
        3600       ; Refresh
        900        ; Retry
        604800     ; Expire
        86400      ; Minimum
)
```

El **serial** identifica la versión de la zona.

### PTR

Resolución inversa:

```dns
10    IN    PTR    www.empresa.local.
```

### TXT

Almacena información textual. Se utiliza, entre otros usos, en mecanismos como SPF y verificaciones de dominio:

```dns
@ IN TXT "v=spf1 mx -all"
```

---

## 8. TTL

El TTL indica cuánto tiempo puede mantenerse un registro en caché.

```dns
www    300    IN    A    192.168.20.10
```

`300` son 300 segundos.

Un TTL bajo facilita la propagación de cambios, pero puede aumentar las consultas DNS. Un TTL alto reduce consultas, pero prolonga la permanencia de datos antiguos en caché.

---

# 9. BIND9 en Ubuntu Server 24.04 LTS

En este tema utilizaremos:

```text
Sistema operativo: Ubuntu Server 24.04 LTS
Servidor DNS:      BIND9
```

Instalación:

```bash
sudo apt update
sudo apt install bind9 bind9-utils bind9-doc
```

Comprobar el servicio:

```bash
sudo systemctl status bind9
```

Iniciar:

```bash
sudo systemctl start bind9
```

Reiniciar:

```bash
sudo systemctl restart bind9
```

Activar el inicio automático:

```bash
sudo systemctl enable bind9
```

---

# 10. Estructura de BIND

Los principales archivos de configuración se encuentran en:

```text
/etc/bind/
```

Entre ellos:

```text
/etc/bind/named.conf
/etc/bind/named.conf.options
/etc/bind/named.conf.local
/etc/bind/named.conf.default-zones
```

Las zonas pueden almacenarse, según la configuración, en:

```text
/var/cache/bind/
```

---

# 11. Configuración global

Archivo:

```text
/etc/bind/named.conf.options
```

Ejemplo para una red interna:

```conf
options {
        directory "/var/cache/bind";

        recursion yes;

        allow-recursion {
                192.168.20.0/24;
        };

        allow-query {
                192.168.20.0/24;
        };

        listen-on {
                192.168.20.5;
        };

        listen-on-v6 {
                none;
        };

        forwarders {
                1.1.1.1;
                8.8.8.8;
        };

        dnssec-validation auto;
};
```

La configuración debe adaptarse a la red real.

En un servidor autoritativo público normalmente se evita ofrecer recursión abierta a Internet.

---

# 12. Crear una zona directa

Supongamos:

```text
Red:       192.168.20.0/24
DNS:       192.168.20.5
Dominio:   aula.local
```

Editar:

```bash
sudo nano /etc/bind/named.conf.local
```

Añadir:

```conf
zone "aula.local" {
        type master;
        file "/var/cache/bind/db.aula.local";
};
```

Crear el archivo:

```bash
sudo nano /var/cache/bind/db.aula.local
```

Contenido:

```dns
$TTL 86400

@   IN  SOA ns1.aula.local. admin.aula.local. (
        2026100101
        3600
        900
        604800
        86400
)

@       IN  NS      ns1.aula.local.

ns1     IN  A       192.168.20.5
router  IN  A       192.168.20.1
web     IN  A       192.168.20.10
srv     IN  A       192.168.20.20
mail    IN  A       192.168.20.30

www     IN  CNAME   web.aula.local.
```

---

# 13. El serial de la zona

Cada modificación de una zona debe incrementar el serial.

Por ejemplo:

```text
2026100101
```

puede interpretarse como:

```text
2026 10 01 01
```

Después de otro cambio:

```text
2026100102
```

Los servidores secundarios utilizan el serial para saber si necesitan actualizarse.

---

# 14. Comprobación de la configuración

Antes de reiniciar BIND:

```bash
sudo named-checkconf
```

Para comprobar una zona:

```bash
sudo named-checkzone aula.local /var/cache/bind/db.aula.local
```

Un resultado correcto será similar a:

```text
zone aula.local/IN: loaded serial 2026100101
OK
```

---

# 15. Recarga de BIND

Cuando se modifica una zona no siempre es necesario reiniciar el servicio.

Puede utilizarse:

```bash
sudo rndc reload
```

o:

```bash
sudo systemctl reload bind9
```

---

# 16. Zona inversa

Para:

```text
192.168.20.0/24
```

la zona inversa es:

```text
20.168.192.in-addr.arpa
```

En `named.conf.local`:

```conf
zone "20.168.192.in-addr.arpa" {
        type master;
        file "/var/cache/bind/db.192.168.20";
};
```

Crear:

```bash
sudo nano /var/cache/bind/db.192.168.20
```

Ejemplo:

```dns
$TTL 86400

@   IN  SOA ns1.aula.local. admin.aula.local. (
        2026100101
        3600
        900
        604800
        86400
)

@       IN  NS      ns1.aula.local.

5       IN  PTR     ns1.aula.local.
10      IN  PTR     web.aula.local.
20      IN  PTR     srv.aula.local.
30      IN  PTR     mail.aula.local.
```

---

# 17. Herramienta `dig`

`dig` es una herramienta fundamental para administrar y diagnosticar DNS.

Consulta básica:

```bash
dig www.aula.local
```

Consultar directamente a nuestro servidor:

```bash
dig @192.168.20.5 www.aula.local
```

Registro A:

```bash
dig @192.168.20.5 www.aula.local A
```

MX:

```bash
dig @192.168.20.5 aula.local MX
```

NS:

```bash
dig @192.168.20.5 aula.local NS
```

SOA:

```bash
dig @192.168.20.5 aula.local SOA
```

Resolución inversa:

```bash
dig @192.168.20.5 -x 192.168.20.10
```

---

# 18. Interpretar `dig`

Las respuestas pueden incluir:

```text
QUESTION SECTION
ANSWER SECTION
AUTHORITY SECTION
ADDITIONAL SECTION
```

Las banderas son especialmente útiles:

```text
qr
aa
rd
ra
```

- `qr`: la respuesta es una respuesta.
- `aa`: respuesta autoritativa.
- `rd`: se solicitó recursión.
- `ra`: el servidor admite recursión.

También conviene comprobar:

```text
status: NOERROR
```

---

# 19. `nslookup`

Otra herramienta habitual:

```bash
nslookup www.aula.local 192.168.20.5
```

Consulta inversa:

```bash
nslookup 192.168.20.10 192.168.20.5
```

`dig` suele ser preferible para diagnóstico porque proporciona información más detallada.

---

# 20. DNS y puertos

DNS utiliza principalmente:

```text
UDP 53
TCP 53
```

UDP se utiliza habitualmente para las consultas normales.

TCP se utiliza, entre otros casos, para transferencias de zona y determinadas respuestas que requieren TCP.

Por tanto, no debe asumirse que DNS utiliza exclusivamente UDP.

---

# 21. Transferencias de zona

Cuando existen varios servidores autoritativos:

```text
DNS primario
     |
     | AXFR / IXFR
     v
DNS secundario
```

**AXFR** realiza una transferencia completa.

**IXFR** permite una transferencia incremental cuando es posible.

---

# 22. Servidor secundario

Ejemplo:

```conf
zone "aula.local" {
        type slave;
        masters {
                192.168.20.5;
        };
        file "/var/cache/bind/db.aula.local";
};
```

Las transferencias deben limitarse a servidores autorizados.

En documentación actual también puede aparecer la terminología **primary/secondary**, pero en configuraciones de BIND es frecuente encontrar `master/slave`.

---

# 23. Control de transferencias

En el primario:

```conf
zone "aula.local" {
        type master;
        file "/var/cache/bind/db.aula.local";

        allow-transfer {
                192.168.20.6;
        };
};
```

Esto evita que cualquier equipo pueda solicitar una copia completa de la zona.

Para aumentar la seguridad pueden utilizarse mecanismos como **TSIG**.

---

# 24. Delegación

Un dominio puede delegar parte de su espacio de nombres.

Ejemplo:

```text
empresa.local
      |
      +--- informatica.empresa.local
```

La administración de `informatica.empresa.local` puede pasar a otros servidores DNS.

La delegación permite distribuir la administración de grandes espacios de nombres.

---

# 25. Forwarders

Un DNS interno puede reenviar consultas externas a otros servidores:

```conf
forwarders {
        1.1.1.1;
        8.8.8.8;
};
```

Flujo:

```text
Cliente
   |
   v
DNS interno
   |
   v
Forwarder
   |
   v
Internet
```

---

# 26. DNS y DHCP

DHCP puede proporcionar al cliente:

```text
Dirección IP
Máscara
Gateway
Servidor DNS
Dominio de búsqueda
```

Por ejemplo:

```text
IP:       192.168.20.50
Gateway:  192.168.20.1
DNS:      192.168.20.5
Dominio:  aula.local
```

DNS y DHCP suelen trabajar conjuntamente en redes corporativas.

---

# 27. DNS dinámico

En redes grandes puede ser necesario actualizar automáticamente DNS cuando DHCP asigna direcciones.

Esto puede realizarse mediante **DDNS**:

```text
DHCP
  |
  | asigna IP
  v
actualización DNS
  |
  v
registro A / PTR
```

Las actualizaciones pueden protegerse mediante mecanismos como TSIG.

---

# 28. DNSSEC

DNSSEC proporciona mecanismos para verificar la autenticidad e integridad de los datos DNS.

Conceptualmente:

```text
Consulta DNS
     |
     v
Respuesta + información criptográfica
     |
     v
Validación
```

Registros relacionados con DNSSEC:

```text
DNSKEY
DS
RRSIG
NSEC
NSEC3
```

**DNSSEC no cifra las consultas DNS.**

Su objetivo principal es permitir validar que determinados datos DNS proceden de una cadena de confianza y no han sido manipulados.

---

# 29. Seguridad de BIND

Medidas habituales:

- Limitar la recursión.
- Deshabilitar la recursión donde no sea necesaria.
- Limitar las transferencias de zona.
- Mantener BIND actualizado.
- Utilizar DNSSEC cuando corresponda.
- Revisar los logs.
- Configurar correctamente el firewall.
- Separar funciones autoritativas y recursivas cuando el diseño lo requiera.
- Evitar servidores DNS abiertos a Internet.

Ejemplo:

```conf
allow-recursion {
        192.168.20.0/24;
};
```

---

# 30. Firewall

Si `ufw` está activo:

```bash
sudo ufw allow 53/udp
sudo ufw allow 53/tcp
```

Comprobar:

```bash
sudo ufw status
```

Solo deben abrirse los servicios necesarios.

---

# 31. Logs

Consultar los mensajes de BIND:

```bash
sudo journalctl -u bind9
```

Últimos mensajes:

```bash
sudo journalctl -u bind9 -n 50
```

Seguir en tiempo real:

```bash
sudo journalctl -u bind9 -f
```

También:

```bash
sudo systemctl status bind9
```

---

# 32. Metodología de diagnóstico

Ante un problema DNS:

### 1. Comprobar conectividad

```bash
ping 192.168.20.5
```

### 2. Comprobar BIND

```bash
systemctl status bind9
```

### 3. Comprobar configuración

```bash
sudo named-checkconf
```

### 4. Comprobar la zona

```bash
sudo named-checkzone aula.local /var/cache/bind/db.aula.local
```

### 5. Consultar directamente

```bash
dig @192.168.20.5 www.aula.local
```

### 6. Comprobar resolución inversa

```bash
dig @192.168.20.5 -x 192.168.20.10
```

### 7. Revisar logs

```bash
sudo journalctl -u bind9
```

---

# 33. Problemas habituales

### BIND no arranca

Comprobar:

```bash
sudo named-checkconf
sudo journalctl -u bind9
```

### La zona no carga

```bash
sudo named-checkzone aula.local /var/cache/bind/db.aula.local
```

### El cliente no resuelve

Comprobar:

```bash
resolvectl status
```

y verificar que utiliza el DNS correcto.

También:

```bash
dig @192.168.20.5 www.aula.local
```

Si la consulta directa funciona pero:

```bash
dig www.aula.local
```

no funciona, el problema puede estar en la configuración DNS del cliente.

### Aparece una respuesta antigua

Puede deberse a la caché. Comprobar el TTL.

---

# 34. Ejemplo de infraestructura ASIR

```text
Red:             192.168.20.0/24

Router:          192.168.20.1

DNS primario:    192.168.20.5
DNS secundario:  192.168.20.6

Web:             192.168.20.10
Servidor:        192.168.20.20
Mail:            192.168.20.30

Dominio:
aula.local
```

Arquitectura:

```text
                         INTERNET
                             |
                         ROUTER
                      192.168.20.1
                             |
              +--------------+--------------+
              |                             |
        DNS PRIMARIO                  DNS SECUNDARIO
        192.168.20.5                 192.168.20.6
              |                             ^
              |                             |
              +-------- AXFR / IXFR --------+
              |
      +-------+--------+--------+
      |                |        |
     WEB              SRV      MAIL
 .20.10             .20.20    .20.30
```

---

# 35. Práctica de ASIR

## Objetivo

Configurar un servidor DNS autoritativo y recursivo con BIND9 en Ubuntu Server 24.04 LTS.

### Parte 1. Instalación

Instalar BIND9 y comprobar el servicio.

### Parte 2. Zona directa

Crear:

```text
asir.local
```

con:

```text
ns1.asir.local
router.asir.local
www.asir.local
srv.asir.local
mail.asir.local
```

### Parte 3. Zona inversa

Configurar:

```text
20.168.192.in-addr.arpa
```

y crear los PTR.

### Parte 4. Recursión

Configurar el servidor para:

- Ser autoritativo para `asir.local`.
- Permitir recursión únicamente a la red interna.
- Utilizar forwarders para consultas externas.

### Parte 5. Diagnóstico

Ejecutar:

```bash
dig @192.168.20.5 www.asir.local
dig @192.168.20.5 asir.local NS
dig @192.168.20.5 asir.local SOA
dig @192.168.20.5 -x 192.168.20.10
```

Explicar las secciones y banderas de las respuestas.

### Parte 6. Servidor secundario

Añadir:

```text
192.168.20.6
```

como servidor secundario y comprobar AXFR/IXFR.

### Parte 7. Seguridad

Configurar:

- Restricción de consultas recursivas.
- Restricción de transferencias.
- Firewall.
- Logs.

Explicar qué riesgo se reduce con cada medida.

---

# 36. Actividades de ampliación

### CNAME

Crear:

```text
intranet.asir.local
```

como alias de:

```text
www.asir.local
```

### MX

Configurar:

```dns
asir.local. IN MX 10 mail.asir.local.
```

y comprobar:

```bash
dig @192.168.20.5 asir.local MX
```

### Varios MX

Configurar:

```dns
MX 10 mail1.asir.local.
MX 20 mail2.asir.local.
```

Explicar la prioridad.

### TTL

Modificar el TTL de un registro y estudiar el comportamiento de la caché.

### Error de configuración

Introducir deliberadamente un error de sintaxis y localizarlo mediante:

```bash
named-checkconf
named-checkzone
journalctl -u bind9
```

---

# 37. Comandos fundamentales

| Objetivo | Comando |
|---|---|
| Estado | `systemctl status bind9` |
| Iniciar | `systemctl start bind9` |
| Reiniciar | `systemctl restart bind9` |
| Recargar | `systemctl reload bind9` |
| Activar inicio | `systemctl enable bind9` |
| Configuración | `named-checkconf` |
| Zona | `named-checkzone` |
| Consulta DNS | `dig` |
| Consulta inversa | `dig -x` |
| Consultar con nslookup | `nslookup` |
| Logs | `journalctl -u bind9` |
| Recargar con RNDC | `rndc reload` |
| DNS del sistema | `resolvectl status` |

---

# 38. Conceptos que debe dominar un alumno de ASIR

1. Qué problema resuelve DNS.
2. Qué es un FQDN.
3. Cómo funciona la jerarquía DNS.
4. Diferencia entre dominio y zona.
5. Diferencia entre servidor autoritativo y recursivo.
6. Diferencia entre resolución recursiva e iterativa.
7. Funcionamiento de la caché.
8. Función del TTL.
9. Registros A, AAAA, CNAME, MX, NS, SOA, PTR y TXT.
10. Zonas directas e inversas.
11. Delegaciones.
12. AXFR e IXFR.
13. Configuración de zonas en BIND9.
14. Diagnóstico con `dig`.
15. DNSSEC.
16. Restricción de la recursión.
17. Restricción de transferencias.
18. Uso de UDP y TCP en DNS.
19. Relación entre DNS y DHCP.
20. Principios básicos de seguridad de un servidor DNS.

---

En ASIR, el objetivo no es únicamente saber instalar BIND9, sino comprender cómo participa DNS en una infraestructura de red y ser capaz de administrar, verificar y solucionar problemas del servicio.
