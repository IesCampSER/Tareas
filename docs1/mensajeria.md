# Mensajería instantánea

## Introducción
La mensajería instantánea (MI) es un sistema de comunicación en tiempo real entre dos o más personas, basado principalmente en el intercambio de mensajes de texto a través de redes IP, como Internet o redes locales. En la actualidad, estos sistemas han evolucionado para incluir también intercambio de archivos, mensajes de voz, videollamadas, reacciones, estados y comunicación en grupo.

La MI se diferencia del correo electrónico en que la comunicación es síncrona o cuasi síncrona, permitiendo conversaciones inmediatas.

## Funcionamiento general
Aunque los detalles varían según la plataforma, el funcionamiento básico de la mensajería instantánea sigue este esquema:

1. El usuario se conecta a un servicio o servidor de mensajería e inicia sesión.
2. El sistema gestiona su estado de presencia (disponible, ausente, ocupado, no molestar, etc.).
3. El usuario accede a su lista de contactos o conversaciones.
4. El sistema informa a los contactos del estado de presencia, cuando esta función está habilitada.
5. Para añadir un contacto suele ser necesario conocer su identificador (número, correo o alias) y, en muchos casos, que exista una aceptación mutua.
6. Al cerrar sesión o perder la conexión, el sistema actualiza el estado del usuario.

## Características actuales de la mensajería instantánea
Los servicios modernos de mensajería instantánea suelen ofrecer:

- Comunicación en tiempo real.
- Mensajes individuales y en grupo.
- Envío de archivos, imágenes, audio y vídeo.
- Confirmaciones de entrega y lectura.
- Estados y mensajes efímeros.
- Cifrado de las comunicaciones, en muchos casos de extremo a extremo.
- Uso multiplataforma (móvil, escritorio y web).

## Uso en entornos personales y profesionales
La mensajería instantánea es ampliamente utilizada tanto a nivel personal como profesional.

- En entornos personales, facilita la comunicación cotidiana de forma rápida y directa.
- En entornos profesionales, forma parte de las herramientas de productividad y colaboración, integrándose con calendarios, gestores de tareas y plataformas de trabajo en equipo.

Algunas organizaciones regulan su uso para evitar distracciones o problemas de seguridad, mientras que otras la consideran esencial para la comunicación interna.

## Bots y automatización
Además de la comunicación persona a persona o en grupo, la mensajería instantánea permite el uso de **bots o agentes de software** que interactúan automáticamente con los usuarios. Estos se emplean habitualmente en:

- Servicios de atención al cliente (SAT).
- Soporte técnico.
- Automatización de procesos.
- Sistemas de notificación y alerta.

## Protocolos de mensajería instantánea
Históricamente han existido numerosos protocolos propietarios como AIM, ICQ, MSN Messenger o Yahoo Messenger, hoy en día ya obsoletos o desaparecidos.

Actualmente predominan:
- Protocolos y APIs propietarias de grandes plataformas (WhatsApp, Telegram, Signal, Microsoft Teams, Slack).
- Protocolos abiertos utilizados en contextos específicos.

## Protocolo XMPP (Jabber)

### Descripción
XMPP (Extensible Messaging and Presence Protocol) es un protocolo abierto y extensible basado originalmente en XML, diseñado para la mensajería instantánea y la gestión de presencia.

### Características principales
- **Arquitectura descentralizada**: similar al correo electrónico, permite operar servidores independientes.
- **Estándar abierto**: definido por la IETF mediante RFCs, sin dependencia de un proveedor concreto.
- **Extensibilidad**: admite extensiones (XEP) para añadir nuevas funcionalidades manteniendo la interoperabilidad.
- **Seguridad**: soporte para autenticación y cifrado mediante TLS y SASL.
- **Interoperabilidad**: posibilidad de conexión con otros servicios mediante pasarelas (cada vez menos habitual).
- **Uso actual**: aunque ya no es mayoritario en mensajería comercial, sigue siendo muy utilizado en entornos corporativos, IoT, videojuegos y sistemas de comunicación internos.

### Clientes y servidores XMPP
Existen múltiples implementaciones libres y comerciales:

**Clientes:**
- Pidgin
- Gajim
- Conversations

**Servidores:**
- ejabberd
- OpenFire
- Prosody

Disponibles para sistemas GNU/Linux, Windows y otros entornos.

## Seguridad y protección de datos
El uso de la mensajería instantánea debe cumplir la normativa vigente, especialmente en el ámbito profesional:

- Cumplimiento del RGPD en la Unión Europea.
- Uso responsable de datos personales.
- Preferencia por plataformas con cifrado robusto.
- Control de accesos y gestión de identidades.
- Políticas de uso claras en organizaciones.

## Conclusión
La mensajería instantánea ha evolucionado desde simples sistemas de texto hasta plataformas completas de comunicación y colaboración. Aunque muchos protocolos clásicos han desaparecido, tecnologías abiertas como XMPP siguen siendo relevantes en contextos específicos, y la seguridad y la protección de datos son hoy elementos clave en su implantación.
