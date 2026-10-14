# Servicio de Correo Electrónico

## 1. Introducción

El correo electrónico es anterior al nacimiento de Internet. Sus primeros usos se remontan a mediados de los años 60 en sistemas multiusuario, y posteriormente se popularizó con el desarrollo de redes como ARPANET. Más de 50 años después, sigue siendo un servicio fundamental y totalmente vigente.

Hoy en día es difícil encontrar una persona sin una dirección de correo electrónico. De hecho, lo habitual es disponer de varias cuentas para distintos usos: personal, profesional, académico, etc.

El correo electrónico permite enviar información de forma casi inmediata a cualquier parte del mundo: texto, imágenes y todo tipo de archivos adjuntos, lo que lo convierte en una herramienta muy flexible y eficiente.

Aunque han aparecido nuevos sistemas de comunicación como las redes sociales o la mensajería instantánea, lejos de sustituir al correo electrónico, estos servicios lo siguen utilizando para notificaciones, registros y recuperación de cuentas.

Además, todos los usuarios de smartphones tienen asociada al menos una cuenta de correo electrónico.

Desde el punto de vista de la administración de sistemas, el correo electrónico es esencial, ya que muchos avisos y alertas del sistema se envían por este medio.

---

## 2. El servicio de correo electrónico

El correo electrónico (email) es probablemente la aplicación TCP/IP más utilizada en Internet y uno de los servicios más importantes tanto en redes públicas como privadas.

Los servidores de correo están constantemente en funcionamiento, permitiendo a los usuarios enviar y recibir mensajes mediante programas cliente o mediante interfaces web (webmail), como Gmail, Outlook o Yahoo.

En este módulo se estudiará el servicio de correo electrónico en su conjunto, centrándonos especialmente en la **instalación y configuración de servidores de correo en sistemas Linux**.

---

## 3. Características del correo electrónico

- Es rápido, económico y fácil de usar.
- Permite la comunicación asíncrona entre emisor y receptor.
- Un mismo mensaje puede enviarse a múltiples destinatarios.
- Permite intercambiar información textual y multimedia mediante archivos adjuntos.
- Puede existir un límite de tamaño en los mensajes.
- Es más ecológico que el correo postal tradicional.
- Presenta problemas asociados como:
  - SPAM o correo no deseado
  - Virus y malware
  - Falta de confidencialidad
  - Suplantación de identidad

---

## 4. Elementos y conceptos básicos

### 4.1 Dirección de correo

Formato general:  
`usuario@dominio`

Ejemplo: `profesorsri@asir.com`

### 4.2 Componentes del sistema de correo

- **MUA (Mail User Agent)**  
  Cliente de correo utilizado por el usuario (Thunderbird, Outlook, etc.).

- **MTA (Mail Transfer Agent)**  
  Encargado del envío y transporte de mensajes entre servidores.  
  Ejemplos: Postfix, Sendmail, Exim.

- **MDA (Mail Delivery Agent)**  
  Deposita los mensajes en el buzón del usuario.  
  Ejemplos: procmail, maildrop.

- **MAA (Mail Access Agent)**  
  Permite al usuario acceder a su buzón.  
  Protocolos: POP3 e IMAP.

### 4.3 ¿Cómo funciona el correo paso a paso?
Este es el proceso simplificado:

1. Escribes un correo en tu aplicación (Outlook, Gmail…).
2. Tu programa lo envía a un **servidor de envío**.
3. Ese servidor busca a qué servidor pertenece el destinatario.
4. Lo envía al **servidor receptor**.
5. La persona destinataria lo descarga en su móvil, ordenador o webmail.  

### 4.4. ¿Qué aplicaciones puedo usar para leer correos?
Existen dos formas:

#### 🖥️ **Clientes (programas instalados)**
- Outlook
- Thunderbird
- Apple Mail
- EM Client

#### 🌐 **Webmail (en navegador)**
- Gmail Web
- Outlook Web
- Proton Mail Web

Su función es la misma: escribir, leer, ordenar y archivar correos.

---

## 5. Protocolos y puertos

### Protocolos principales
- **SMTP / ESMTP**: envío de correo. Se usa cuando mandas un correo. Equivale al cartero que recoge tu carta.
- **POP3**: descarga de mensajes. Antiguo, aún en uso, pero menos recomendable.
- **IMAP4**: acceso remoto al buzón. Permite ver el correo sin descargarlo definitivamente. Todo queda en el servidor.
Ideal para móviles + ordenador.

### Puertos habituales
- SMTP entre servidores: **25**
- Envío autenticado desde clientes: **587**
- POP3: **110**
- IMAP: **143**
- POPS: **995**
- IMAPS: **993**

---

## 6. POP3 vs IMAP

### POP3
- Descarga los mensajes al equipo local.
- Adecuado para un único dispositivo.
- Menor consumo de recursos del servidor.

### IMAP
- Los mensajes permanecen en el servidor.
- Sincronización entre múltiples dispositivos.
- Gestión remota de carpetas y estados.

➡️ Actualmente, **IMAP es la opción más recomendable**.

---

## 7. Seguridad en el correo electrónico

Principales problemas:
Hoy en día hay muchos riesgos: spam, virus, suplantaciones (phishing)… Para proteger a los usuarios, los servidores usan tecnologías como:

- **SPF** → permite saber si un correo lo ha enviado un servidor autorizado.
- **DKIM** → añade una firma digital al correo para evitar modificaciones.
- **DMARC** → decide qué hacer si un mensaje parece falso.
- **TLS** → cifra la comunicación entre servidores.

Todo esto actúa sin que tú tengas que hacer nada.

---

## 8. Servidores de correo

Un servidor de correo electrónico permite:
- Enviar
- Recibir
- Almacenar
- Reenviar mensajes

Ejemplos de servidores:
- Postfix
- Sendmail
- Exim
- Microsoft Exchange

---  

## 9. Consejos prácticos
- No abras archivos de desconocidos.
- Comprueba siempre el remitente.
- Usa contraseñas seguras y, si es posible, **doble autenticación**.
- Mantén tus dispositivos actualizados.
- No pulses enlaces sospechosos.

---

## 10. Motivos para instalar un servidor propio

- Control total del sistema
- Independencia de proveedores externos
- Mayor privacidad
- Integración con la red corporativa
- Menos limitaciones técnicas


  **Licencia:** Lourdes Tamarit. Esta obra está bajo una licencia Creative Commons Atribución-CompartirIgual 4.0 Internacional (CC BY-SA 4.0).
