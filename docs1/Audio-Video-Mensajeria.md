# Apuntes de Servicios en Red: Mensajería, Streaming y Listas de Distribución

Este documento recoge los conceptos fundamentales sobre servicios de comunicación en tiempo real, difusión de contenidos multimedia y gestión de listas de correo, orientados a la administración de sistemas informáticos en red.

---

## 1. Mensajería Instantánea (MI)

La **mensajería instantánea** es una forma de comunicación en tiempo real basada en texto a través de dispositivos conectados a una red local o Internet [1].

### Funcionamiento del Servicio
El intercambio de mensajes se produce entre usuarios que previamente han aceptado comunicarse [1]. El flujo técnico general es:
1. **Conexión:** El usuario se conecta al servidor y establece su estado de **presencia** (disponible, ocupado, etc.) [1, 2].
2. **Sincronización:** El servidor envía la lista de contactos asociada y el estado de cada uno de ellos [3].
3. **Notificación:** El servidor informa automáticamente de la presencia del usuario a sus contactos conectados [3].
4. **Desconexión:** Al cerrar el cliente, se informa al servidor, que notifica a los contactos la salida del usuario [2].

Para dar de alta a un contacto es necesario conocer su alias y que este **autorice la inclusión** [2, 3].

### Protocolo XMPP/Jabber
Frente a protocolos propietarios (AIM, ICQ, MSN) que suelen ser incompatibles entre sí, surge **XMPP** como un estándar abierto basado en **XML** [4]. Sus características principales son:
* **Arquitectura Descentralizada:** Similar al correo electrónico; cualquiera puede montar su propio servidor sin depender de una autoridad central [5].
* **Seguridad Robusta:** Utiliza sistemas de cifrado como **SASL y TLS** [5].
* **Flexibilidad:** Permite el uso de **pasarelas** para conectar con otros protocolos propietarios [5].
* **Software recomendado:**
    * **Servidores:** `ejabberd`, `OpenFire`, `jabberd2` [6].
    * **Clientes genéricos:** `Pidgin` [6].

---

## 2. Tecnologías de Streaming

El **streaming** permite reproducir audio y vídeo **sin esperar a la descarga completa** del archivo, optimizando la visualización de contenidos en red [7, 8].

### Conceptos Técnicos Fundamentales
* **Buffer (Memoria Intermedia):** Almacenamiento temporal que acumula datos antes de reproducirlos. Sirve para amortiguar los efectos del **jitter** (variaciones en el retardo de red) y evitar cortes [7, 9, 10].
* **Codecs:** Algoritmos encargados de la codificación y el empaquetado del contenido [11].
* **Segmentación:** El flujo de datos se divide en paquetes. El tamaño de estos influye en la eficiencia del ancho de banda y está limitado por la **MTU** de la red [12, 13].

### Protocolos de Red
| Protocolo | Capa | Función y Características |
| :--- | :--- | :--- |
| **TCP** | Transporte | Fiable pero lento por el reconocimiento de paquetes. No permite **multicast** [11, 14]. |
| **UDP** | Transporte | Rápido, sin confirmación de recepción. Ideal para evitar retrasos y para transmisiones **multicast** [11, 15]. |
| **HTTP** | Aplicación | Usado en arquitecturas sin servidor. Fácil de escalar con **CDNs** [16, 17]. |
| **RTMP** | Aplicación | Protocolo de Adobe que ofrece baja latencia sobre TCP [18]. |
| **RTSP** | Aplicación | Controla la sesión (Play, Pause, Setup) [19]. |
| **RTP** | Aplicación | Transporta la carga de datos. Usa marcas de tiempo para sincronizar audio y vídeo por separado [20]. |
| **RTCP** | Aplicación | Monitoriza la calidad de la transmisión [19]. |

### Arquitecturas de Servicio
1. **Típica:** Servidor de streaming dedicado y aplicaciones cliente específicas [16, 21].
2. **Sin Servidor (Server-Less):** Utiliza un servidor web convencional (HTTP sobre TCP). Es económico y compatible, pero difícil de escalar a multicast [16, 17].
3. **Sin Cliente (Client-Less):** El reproductor se integra en el navegador mediante **HTML5** o plugins (Java/Flash) [22].

---

## 3. Listas de Distribución

Son mecanismos de difusión de información basados en el **correo electrónico** que facilitan la distribución de mensajes a múltiples destinatarios mediante una única dirección de envío [23].

### Roles y Gestión
* **Administrador del Servicio:** Persona encargada de instalar y configurar el servidor (soporte técnico) [24].
* **Administrador de la Lista:** Gestiona las altas/bajas de usuarios y modera los contenidos [24, 25].

### Clasificación de las Listas
* **Según Suscripción:**
    * **Públicas:** Suscripción automática mediante comandos de correo [26].
    * **Privadas:** Requieren aprobación previa del propietario [26].
* **Según Participación:**
    * **Abiertas:** Cualquiera puede enviar mensajes, lo que conlleva riesgo de **spam** [27].
    * **Cerradas:** Solo el propietario puede enviar mensajes (ej. fines informativos) [27].
    * **Moderadas:** Los mensajes pasan por un filtro o moderador antes de su distribución [27].

### Modos de Suscripción Especiales
* **Resumen (Digest):** El usuario recibe un solo mensaje diario con todo lo publicado en la lista [28].
* **No correo (Solo Web):** El usuario envía mensajes pero no los recibe en su buzón; los consulta a través de una interfaz web [28].

### Software de Implementación
* **Software Libre:** **Mailman** (funciona sobre GNU/Linux con Postfix o Sendmail) [29].
* **Software Propietario:** **LISTSERV**, Microsoft Exchange o hMailServer [29, 30].
