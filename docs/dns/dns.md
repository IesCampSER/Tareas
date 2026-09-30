# Servidor DNS
## Servicios en Red · 2.º SMR

> **Objetivo:** entender qué es DNS, cómo funciona una resolución de nombres y aprender a instalar, configurar y comprobar un servidor DNS con **BIND9** en Linux.

---

## 1. ¿Qué es DNS?

**DNS (Domain Name System)** es el servicio que traduce nombres fáciles de recordar en direcciones IP.

```text
www.google.com
       │
       │ DNS
       ▼
142.250.184.36
```

Las personas usamos nombres:

```text
www.google.com
```

Los equipos necesitan direcciones IP:

```text
142.250.184.36
```

DNS funciona como una **agenda de teléfonos de Internet**.

### Ejemplo

Cuando escribimos:

```text
https://www.iesejemplo.es
```

el ordenador necesita conocer la IP asociada.

```text
┌──────────────┐
│ PC alumno    │
└──────┬───────┘
       │ ¿Qué IP tiene www.iesejemplo.es?
       ▼
┌──────────────┐
│ Servidor DNS │
└──────┬───────┘
       │ 93.184.216.34
       ▼
┌──────────────┐
│ Servidor web │
└──────────────┘
```

---

# 2. ¿Por qué necesitamos DNS?

Sin DNS tendríamos que recordar las IP de los servidores.

| Servicio | Con DNS | Sin DNS |
|---|---|---|
| Web | `www.empresa.es` | `203.0.113.20` |
| Correo | `mail.empresa.es` | `203.0.113.30` |
| Servidor | `servidor.empresa.local` | `192.168.10.10` |

DNS permite utilizar nombres en lugar de memorizar direcciones.

---

# 3. DNS en una red local

En una empresa podemos tener un servidor DNS interno.

```text
                 RED LOCAL
              192.168.10.0/24

        ┌─────────────────────┐
        │   SERVIDOR DNS      │
        │   192.168.10.10     │
        │      BIND9          │
        └──────────┬──────────┘
                   │
          ┌────────┴────────┐
          │                 │
     ┌────▼────┐       ┌────▼────┐
     │   PC1   │       │   PC2   │
     │ .20     │       │ .30     │
     └─────────┘       └─────────┘
```

Podríamos configurar:

```text
servidor.empresa.local  → 192.168.10.10
web.empresa.local       → 192.168.10.20
nas.empresa.local       → 192.168.10.30
```

Así, un usuario podría acceder a:

```text
http://web.empresa.local
```

en lugar de:

```text
http://192.168.10.20
```

---

# 4. Conceptos básicos

## Dominio

Ejemplo:

```text
empresa.es
```

## Subdominio

```text
empresa.es
│
├── aula.empresa.es
├── tienda.empresa.es
└── soporte.empresa.es
```

## Host

Es el nombre de un equipo o servicio dentro de un dominio.

```text
web.empresa.es
```

- `web` → host
- `empresa.es` → dominio

---

# 5. FQDN

**FQDN (Fully Qualified Domain Name)** es el nombre completo de un equipo.

```text
www . empresa . es
 │       │      │
 │       │      └── TLD
 │       └───────── dominio
 └───────────────── host
```

Ejemplo:

```text
servidor.aula.empresa.es
```

En los archivos de BIND es frecuente encontrar:

```text
servidor.empresa.es.
```

El último `.` representa la **raíz de DNS**.

---

# 6. La jerarquía DNS

DNS tiene una estructura jerárquica.

```text
                         .
                    ┌────┴────┐
                    │  RAÍZ   │
                    └────┬────┘
                         │
              ┌──────────┼──────────┐
              │          │          │
             .es        .com       .org
              │          │
          empresa.es  google.com
              │
       ┌──────┼──────┐
       │      │      │
      www    mail   ftp
```

La raíz es:

```text
.
```

Después aparecen los dominios de nivel superior (**TLD**):

```text
.es
.com
.org
.net
```

Y después los dominios:

```text
empresa.es
google.com
```

---

# 7. ¿Cómo funciona una consulta DNS?

Supongamos que escribimos:

```text
www.empresa.es
```

El ordenador necesita conocer su IP.

```text
        CLIENTE
           │
           │ 1. ¿Qué IP tiene
           │    www.empresa.es?
           ▼
      SERVIDOR DNS
           │
           │ 2. Busca la respuesta
           ▼
        INTERNET
           │
           │ 3. Respuesta
           ▼
      SERVIDOR DNS
           │
           │ 4. 203.0.113.20
           ▼
        CLIENTE
```

Después el cliente puede conectarse:

```text
www.empresa.es
       │
       ▼
203.0.113.20
       │
       ▼
Servidor web
```

---

# 8. Consulta recursiva e iterativa

## Consulta recursiva

El cliente pide al servidor DNS:

> "Dame la respuesta completa."

```text
CLIENTE
   │
   │ www.empresa.es
   ▼
DNS LOCAL
   │
   │ busca por el cliente
   ▼
OTROS DNS
   │
   ▼
RESPUESTA
   │
   ▼
CLIENTE
```

## Consulta iterativa

Un servidor puede preguntar a otros servidores y recibir referencias.

```text
DNS LOCAL
   │
   │ ¿Quién conoce .es?
   ▼
SERVIDOR RAÍZ
   │
   │ Pregunta a este servidor
   ▼
DNS .ES
   │
   │ Pregunta a este servidor
   ▼
DNS empresa.es
   │
   │ www → 203.0.113.20
   ▼
DNS LOCAL
```

---

# 9. Tipos de servidores DNS

## Servidor autoritativo

Tiene la información oficial de una zona.

```text
empresa.es
    │
    ├── www → 203.0.113.20
    ├── mail → 203.0.113.30
    └── ftp → 203.0.113.40
```

## Servidor recursivo

Recibe consultas de clientes y busca la respuesta cuando no la conoce.

## Servidor caché

Guarda temporalmente las respuestas.

```text
PRIMERA CONSULTA

PC ───► DNS ───► Internet
         │
         ◄────── IP
         │
         ▼
       CACHÉ


SEGUNDA CONSULTA

PC ───► DNS
         │
         │ Ya conoce la respuesta
         ▼
       RESPUESTA
```

El tiempo de permanencia en caché se controla mediante **TTL (Time To Live)**.

---

# 10. Registros DNS

DNS almacena información mediante registros.

| Registro | Función | Ejemplo |
|---|---|---|
| **A** | Nombre → IPv4 | `web → 192.168.10.20` |
| **AAAA** | Nombre → IPv6 | `web → 2001:db8::20` |
| **CNAME** | Alias | `www → web` |
| **MX** | Servidor de correo | `mail.empresa.es` |
| **NS** | Servidor DNS | `ns1.empresa.es` |
| **PTR** | IP → nombre | `192.168.10.20 → web` |
| **SOA** | Información de la zona | Datos de autoridad |
| **TXT** | Texto asociado | Verificaciones, SPF, etc. |

---

# 11. Registro A

Relaciona un nombre con una dirección **IPv4**.

```text
web.empresa.local
        │
        │ A
        ▼
  192.168.10.20
```

En BIND:

```dns
web      IN      A       192.168.10.20
```

---

# 12. Registro AAAA

Hace lo mismo que A, pero con IPv6.

```dns
web      IN      AAAA    2001:db8::20
```

---

# 13. Registro CNAME

Permite crear un alias.

```text
www.empresa.local
        │
        │ CNAME
        ▼
web.empresa.local
        │
        │ A
        ▼
192.168.10.20
```

Configuración:

```dns
web      IN A       192.168.10.20
www      IN CNAME   web
```

---

# 14. Registro MX

Indica qué servidor recibe el correo.

```text
empresa.es
     │
     │ MX
     ▼
mail.empresa.es
     │
     ▼
192.168.10.30
```

Ejemplo:

```dns
empresa.es. IN MX 10 mail.empresa.es.
```

El número `10` es la prioridad.

> Cuanto menor sea el número, mayor es la prioridad.

---

# 15. Registro PTR

Realiza la resolución inversa:

```text
IP → nombre
```

Mientras que:

```text
A:
nombre → IP
```

PTR:

```text
IP → nombre
```

Ejemplo:

```text
192.168.10.20
      │
      │ PTR
      ▼
web.empresa.local
```

---

# 16. DNS directo e inverso

### Zona directa

Pregunta:

> ¿Qué IP tiene este nombre?

```text
web.empresa.local
        │
        ▼
192.168.10.20
```

### Zona inversa

Pregunta:

> ¿Qué nombre corresponde a esta IP?

```text
192.168.10.20
        │
        ▼
web.empresa.local
```

```text
             DNS
              │
       ┌──────┴──────┐
       │             │
    DIRECTA       INVERSA
       │             │
 nombre → IP      IP → nombre
```

---

# 17. BIND9

Uno de los servidores DNS más utilizados en Linux es **BIND9**.

En Ubuntu/Debian:

```bash
sudo apt update
sudo apt install bind9 bind9-utils dnsutils
```

Comprobar el servicio:

```bash
sudo systemctl status bind9
```

Debería aparecer:

```text
Active: active (running)
```

---

# 18. Archivos importantes de BIND9

En Ubuntu/Debian encontramos la configuración en:

```text
/etc/bind/
```

```text
/etc/bind/
│
├── named.conf
├── named.conf.options
├── named.conf.local
├── named.conf.default-zones
│
└── db.empresa.local
```

### Los más importantes

| Archivo | Función |
|---|---|
| `named.conf` | Configuración principal |
| `named.conf.options` | Opciones generales |
| `named.conf.local` | Nuestras zonas |
| `named.conf.default-zones` | Zonas predeterminadas |

---

# 19. Configuración de opciones

Archivo:

```text
/etc/bind/named.conf.options
```

Ejemplo:

```conf
options {
        directory "/var/cache/bind";

        recursion yes;

        allow-query {
                192.168.10.0/24;
                localhost;
        };

        forwarders {
                1.1.1.1;
                8.8.8.8;
        };
};
```

### Significado

```text
recursion yes;
```

Permite consultas recursivas.

```text
allow-query
```

Indica qué equipos pueden consultar el servidor.

```text
forwarders
```

Indica DNS externos a los que se pueden reenviar consultas.

---

# 20. Crear nuestra primera zona

Supongamos:

```text
Dominio: empresa.local
Servidor DNS: 192.168.10.10
Servidor web: 192.168.10.20
Servidor correo: 192.168.10.30
```

En:

```text
/etc/bind/named.conf.local
```

añadimos:

```conf
zone "empresa.local" {
        type master;
        file "/etc/bind/db.empresa.local";
};
```

Esto indica:

```text
Zona:
empresa.local

Archivo:
db.empresa.local
```

---

# 21. Crear el archivo de zona

```bash
sudo nano /etc/bind/db.empresa.local
```

Contenido:

```dns
$TTL 86400

@       IN      SOA     ns1.empresa.local. admin.empresa.local. (
                        2026092901
                        3600
                        1800
                        604800
                        86400 )

        IN      NS      ns1.empresa.local.

ns1     IN      A       192.168.10.10
web     IN      A       192.168.10.20
mail    IN      A       192.168.10.30
www     IN      CNAME   web
```

---

# 22. Entender el archivo de zona

## `$TTL`

```text
$TTL 86400
```

Indica el tiempo de vida predeterminado.

```text
86400 segundos = 24 horas
```

## SOA

```dns
@ IN SOA ns1.empresa.local. admin.empresa.local.
```

**SOA = Start Of Authority**

Contiene información principal de la zona.

## NS

```dns
IN NS ns1.empresa.local.
```

Indica el servidor DNS autoritativo.

## A

```dns
web IN A 192.168.10.20
```

Relaciona:

```text
web → 192.168.10.20
```

## CNAME

```dns
www IN CNAME web
```

Relaciona:

```text
www → web → 192.168.10.20
```

---

# 23. Comprobar la configuración

Antes de reiniciar BIND:

```bash
sudo named-checkconf
```

Si no aparece ningún mensaje, la sintaxis general es correcta.

Comprobar una zona:

```bash
sudo named-checkzone empresa.local /etc/bind/db.empresa.local
```

Resultado esperado:

```text
zone empresa.local/IN: loaded serial 2026092901
OK
```

---

# 24. Reiniciar BIND

Después de modificar la configuración:

```bash
sudo systemctl restart bind9
```

Comprobar:

```bash
sudo systemctl status bind9
```

Consultar mensajes:

```bash
sudo journalctl -u bind9
```

---

# 25. Probar DNS con `dig`

```bash
dig @192.168.10.10 web.empresa.local
```

Estamos preguntando:

> Al DNS `192.168.10.10`, ¿qué IP tiene `web.empresa.local`?

La respuesta debería contener:

```text
web.empresa.local.    IN    A    192.168.10.20
```

---

# 26. Probar DNS con `nslookup`

```bash
nslookup web.empresa.local 192.168.10.10
```

Podemos obtener:

```text
Server:         192.168.10.10
Address:        192.168.10.10#53

Name:           web.empresa.local
Address:        192.168.10.20
```

---

# 27. ¿Qué significa el puerto 53?

DNS utiliza normalmente:

```text
Puerto 53
```

Principalmente:

```text
UDP 53
```

También:

```text
TCP 53
```

en determinadas situaciones, como respuestas grandes o transferencias de zona.

```text
              DNS
               │
       ┌───────┴───────┐
       │               │
    UDP 53          TCP 53
  consultas       casos especiales
```

---

# 28. DNS y DHCP

Son servicios diferentes, pero trabajan juntos con frecuencia.

### DHCP

Asigna:

```text
IP
Máscara
Puerta de enlace
DNS
```

### DNS

Resuelve:

```text
web.empresa.local
          ↓
192.168.10.20
```

Ejemplo:

```text
                SERVIDOR DHCP
                     │
                     │ entrega
                     ▼
       ┌──────────────────────────┐
       │ IP:       192.168.10.50  │
       │ Máscara:  255.255.255.0  │
       │ Gateway:  192.168.10.1  │
       │ DNS:      192.168.10.10  │
       └──────────────────────────┘
                     │
                     ▼
                    PC
                     │
                     │ consulta
                     ▼
              DNS 192.168.10.10
```

---

# 29. Zona inversa

Para la red:

```text
192.168.10.0/24
```

la zona inversa será:

```text
10.168.192.in-addr.arpa
```

En:

```text
/etc/bind/named.conf.local
```

añadimos:

```conf
zone "10.168.192.in-addr.arpa" {
        type master;
        file "/etc/bind/db.192.168.10";
};
```

---

# 30. Archivo de zona inversa

Creamos:

```bash
sudo nano /etc/bind/db.192.168.10
```

Ejemplo:

```dns
$TTL 86400

@       IN      SOA     ns1.empresa.local. admin.empresa.local. (
                        2026092901
                        3600
                        1800
                        604800
                        86400 )

        IN      NS      ns1.empresa.local.

10      IN      PTR     ns1.empresa.local.
20      IN      PTR     web.empresa.local.
30      IN      PTR     mail.empresa.local.
50      IN      PTR     pc1.empresa.local.
```

---

# 31. Probar resolución inversa

```bash
dig @192.168.10.10 -x 192.168.10.20
```

Deberíamos obtener:

```text
20.10.168.192.in-addr.arpa.
        PTR
web.empresa.local.
```

Por tanto:

```text
192.168.10.20
      ↓
web.empresa.local
```

---

# 32. Ejemplo completo de una empresa

Tenemos:

```text
RED: 192.168.10.0/24
```

| Equipo | IP | Nombre |
|---|---:|---|
| DNS | `192.168.10.10` | `ns1.empresa.local` |
| Web | `192.168.10.20` | `web.empresa.local` |
| Correo | `192.168.10.30` | `mail.empresa.local` |
| PC1 | `192.168.10.50` | `pc1.empresa.local` |

Configuración:

```text
ns1  → 192.168.10.10
web  → 192.168.10.20
mail → 192.168.10.30
pc1  → 192.168.10.50
```

Y:

```text
www → web
```

Por tanto:

```text
www.empresa.local
        ↓
web.empresa.local
        ↓
192.168.10.20
```

---

# 33. Flujo completo de una consulta

Si desde PC1 ejecutamos:

```bash
ping web.empresa.local
```

ocurre algo parecido a esto:

```text
┌───────────────┐
│      PC1      │
│192.168.10.50  │
└───────┬───────┘
        │
        │ ¿IP de web.empresa.local?
        ▼
┌───────────────┐
│   DNS BIND9   │
│192.168.10.10  │
└───────┬───────┘
        │
        │ Busca en su zona
        ▼
┌─────────────────────┐
│ web → 192.168.10.20 │
└─────────┬───────────┘
          │
          │ respuesta
          ▼
       ┌──────┐
       │ PC1  │
       └──┬───┘
          │
          │ ping 192.168.10.20
          ▼
       ┌──────┐
       │ WEB  │
       └──────┘
```

---

# 34. Diagnóstico de problemas

Cuando DNS no funciona, seguir este orden.

### 1. ¿Está BIND funcionando?

```bash
systemctl status bind9
```

### 2. ¿Hay errores de configuración?

```bash
named-checkconf
```

### 3. ¿La zona es correcta?

```bash
named-checkzone empresa.local /etc/bind/db.empresa.local
```

### 4. ¿El servidor responde?

```bash
dig @192.168.10.10 web.empresa.local
```

### 5. ¿El cliente utiliza el DNS correcto?

En sistemas actuales con `systemd-resolved`:

```bash
resolvectl status
```

---

# 35. Errores típicos

## Error 1: olvidar el punto final

En nombres FQDN dentro de determinados registros:

```dns
ns1.empresa.local.
```

es un nombre absoluto.

Sin el punto:

```dns
ns1.empresa.local
```

BIND puede interpretarlo como un nombre relativo y añadir el dominio de la zona.

---

## Error 2: error de sintaxis

Utiliza siempre:

```bash
named-checkconf
```

y:

```bash
named-checkzone
```

antes de reiniciar BIND.

---

## Error 3: olvidar aumentar el serial

Si modificamos una zona:

```text
2026092901
```

podemos pasar a:

```text
2026092902
```

El **serial** identifica la versión de la zona.

---

# 36. Práctica de aula

## Objetivo

Crear un servidor DNS para una red de aula.

### Red

```text
192.168.20.0/24
```

### Servidores

| Equipo | IP |
|---|---:|
| DNS | `192.168.20.5` |
| Web | `192.168.20.10` |
| FTP | `192.168.20.11` |
| PC alumno | `192.168.20.50` |

### Dominio

```text
aula.local
```

---

## Tareas

### 1. Instalar BIND9

```bash
sudo apt update
sudo apt install bind9 bind9-utils dnsutils
```

### 2. Crear la zona

```text
aula.local
```

### 3. Crear registros A

```text
ns1.aula.local  → 192.168.20.5
web.aula.local  → 192.168.20.10
ftp.aula.local  → 192.168.20.11
```

### 4. Crear un alias

```text
www.aula.local → web.aula.local
```

### 5. Crear la zona inversa

Para:

```text
192.168.20.0/24
```

### 6. Crear registros PTR

```text
192.168.20.5  → ns1.aula.local
192.168.20.10 → web.aula.local
192.168.20.11 → ftp.aula.local
```

### 7. Comprobar

```bash
dig @192.168.20.5 web.aula.local
```

Y:

```bash
dig @192.168.20.5 -x 192.168.20.10
```

---

# 37. Reto

Añade:

```text
mail.aula.local → 192.168.20.12
```

Y configura:

```text
aula.local → MX → mail.aula.local
```

Después comprueba:

```bash
dig @192.168.20.5 aula.local MX
```

---

# 38. Comandos imprescindibles

| Comando | Utilidad |
|---|---|
| `systemctl status bind9` | Ver estado de BIND |
| `systemctl restart bind9` | Reiniciar BIND |
| `named-checkconf` | Comprobar configuración |
| `named-checkzone` | Comprobar una zona |
| `dig dominio` | Consultar DNS |
| `dig -x IP` | Consulta inversa |
| `nslookup dominio` | Consulta DNS sencilla |
| `journalctl -u bind9` | Consultar registros de BIND |

---

# 39. Ideas clave para el examen

> **DNS traduce nombres de dominio a direcciones IP y también puede realizar resolución inversa.**

- DNS utiliza normalmente el **puerto 53**.
- **A** → nombre a IPv4.
- **AAAA** → nombre a IPv6.
- **CNAME** → alias.
- **MX** → servidor de correo.
- **NS** → servidor DNS autoritativo.
- **PTR** → IP a nombre.
- **SOA** → información principal de una zona.
- **TTL** → tiempo de vida.
- **BIND9** → servidor DNS utilizado habitualmente en Linux.
- Zona **directa** → nombre → IP.
- Zona **inversa** → IP → nombre.
- `dig` y `nslookup` permiten probar consultas.
- `named-checkconf` comprueba la configuración.
- `named-checkzone` comprueba una zona.

---

# 40. Mini test

### 1. ¿Qué función realiza DNS?

A. Asignar automáticamente direcciones IP  
B. Traducir nombres a direcciones IP  
C. Cifrar conexiones web  
D. Compartir archivos  

**Respuesta: B**

### 2. ¿Qué registro relaciona un nombre con una IPv4?

A. MX  
B. PTR  
C. A  
D. CNAME  

**Respuesta: C**

### 3. ¿Qué registro se utiliza para una resolución inversa?

A. A  
B. PTR  
C. MX  
D. NS  

**Respuesta: B**

### 4. ¿Qué software podemos utilizar como servidor DNS en Ubuntu?

A. Apache  
B. Samba  
C. BIND9  
D. Docker  

**Respuesta: C**

### 5. ¿Qué herramienta permite comprobar una zona BIND?

A. `named-checkzone`  
B. `ipconfig`  
C. `chmod`  
D. `ssh`  

**Respuesta: A**

### 6. ¿Qué registro permite crear un alias?

A. PTR  
B. MX  
C. CNAME  
D. SOA  

**Respuesta: C**

---

# 41. Mapa mental final

```text
                         ┌─────────────┐
                         │     DNS     │
                         └──────┬──────┘
                                │
             ┌──────────────────┼──────────────────┐
             │                  │                  │
          ¿PARA QUÉ?        ¿CÓMO?          ¿CON QUÉ?
             │                  │                  │
        nombre ↔ IP          consultas          BIND9
             │                  │                  │
       ┌─────┴─────┐      ┌─────┴─────┐     ┌────┴────┐
       │           │      │           │     │         │
    DIRECTA     INVERSA  recursiva  iterativa  zonas   registros
       │           │                           │
    A/AAAA        PTR                    A CNAME MX NS
```

> **Idea fundamental:** cuando un usuario escribe `www.aula.local`, DNS se encarga de averiguar qué dirección IP corresponde a ese nombre para que el equipo pueda comunicarse con el servidor correcto.
