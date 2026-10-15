# Servidor proxy en redes locales y entornos de servidores

## 1. ¿Qué es un proxy?

Un **proxy** es un servicio intermediario que recibe peticiones de un cliente y las reenvía a otro servidor. Cuando recibe la respuesta del servidor de destino, la devuelve al cliente.

El proxy puede aplicar reglas de acceso, registrar determinadas peticiones, almacenar algunas respuestas en caché o modificar ciertos datos de la comunicación. Sus funciones dependen del tipo de proxy, del software utilizado y de su configuración.

En una conexión directa, el cliente se comunica con el servidor de destino. Cuando se utiliza un proxy, la comunicación pasa por ese intermediario.

### Ejemplo

En una red de un centro educativo, los ordenadores pueden utilizar un servidor proxy para acceder a páginas web. El administrador puede configurar reglas para permitir o denegar determinados destinos y consultar los registros de actividad.

> **Importante:** un proxy no es necesariamente un cortafuegos completo ni garantiza el anonimato de los usuarios. Esas capacidades dependen de su configuración y de las funciones del software.

## 2. Tipos de proxy según su función

### 2.1. Forward proxy o proxy directo

Un **forward proxy** se sitúa entre los clientes de una red y los servidores externos a los que quieren acceder.

Por ejemplo, los equipos de una red local envían sus peticiones web al proxy, y este las reenvía a los servidores de Internet. Puede utilizarse para aplicar políticas de acceso, registrar peticiones y, cuando es posible, almacenar respuestas en caché.

En este escenario, el servidor de destino normalmente ve la dirección IP del proxy como origen de la conexión. Sin embargo, el proxy puede transmitir información adicional sobre el cliente y también puede conservar registros, por lo que no garantiza el anonimato.

### 2.2. Reverse proxy o proxy inverso

Un **reverse proxy** se sitúa delante de uno o varios servidores que ofrecen servicios, como páginas web. Los clientes se conectan al proxy inverso y este reenvía las peticiones al servidor interno correspondiente.

Puede utilizarse para:

- Centralizar el acceso a varios servidores web.
- Distribuir peticiones entre varios servidores.
- Gestionar certificados TLS y la terminación de conexiones HTTPS.
- Ocultar parte de la arquitectura interna.
- Añadir controles de acceso y otras medidas de protección.

Un proxy inverso puede funcionar aunque solo haya un servidor de destino.

## 3. Funciones principales de un proxy

### 3.1. Control de acceso y filtrado

El proxy puede aplicar reglas para permitir o denegar peticiones. Según el software, las reglas pueden basarse en criterios como:

- La dirección IP o la subred del cliente.
- El destino solicitado o el dominio.
- El horario de acceso.
- La autenticación del usuario.
- El tipo de petición u otros datos disponibles para el proxy.

Por ejemplo, se puede permitir el uso del proxy únicamente a los equipos de la red del aula o bloquear el acceso a determinados destinos.

Las reglas deben configurarse y comprobarse cuidadosamente. Una regla incorrecta puede bloquear tráfico legítimo o permitir accesos que deberían estar restringidos.

### 3.2. Registro de actividad

Muchos proxies generan **registros o logs** con información sobre las peticiones procesadas, como la fecha, el cliente, el destino y el resultado de la solicitud. El detalle depende del software y de su configuración.

Los registros ayudan a diagnosticar problemas y comprobar si las reglas funcionan. También pueden contener información sobre los hábitos de navegación, por lo que deben gestionarse respetando la privacidad y las normas aplicables.

### 3.3. Ocultación de la dirección IP del cliente

En un forward proxy convencional, el servidor de destino suele recibir la conexión desde la dirección IP del proxy, no desde la dirección IP pública del cliente. No obstante, esto no equivale a navegar de forma anónima.

El proxy puede registrar la dirección del cliente o transmitir información adicional en cabeceras HTTP. Además, la autenticación, las cookies y otros mecanismos pueden permitir identificar al usuario.

Por tanto, un proxy puede ocultar la dirección IP del cliente al destino en determinadas condiciones, pero **no garantiza el anonimato ni la privacidad**.

### 3.4. Inspección y seguridad

Un proxy puede inspeccionar y controlar determinados tipos de tráfico de aplicación. Por ejemplo, un proxy HTTP puede aplicar reglas a las peticiones web que gestiona.

Sin embargo, un proxy HTTP no controla automáticamente todos los protocolos ni sustituye por sí solo a un cortafuegos de red. La inspección del tráfico HTTPS también está limitada por el cifrado, salvo que se configure un mecanismo específico de inspección TLS, con sus implicaciones técnicas, de seguridad y de privacidad.

### 3.5. Caché web

Una **caché** almacena temporalmente determinadas respuestas para poder reutilizarlas cuando se solicita el mismo recurso y las condiciones permiten servir la copia almacenada.

La caché puede reducir el consumo de ancho de banda y acelerar algunas peticiones. Su utilidad depende del tipo de contenido, de las cabeceras HTTP y de las políticas de caché del servidor de origen.

HTTPS no elimina la caché web: los navegadores siguen almacenando recursos y también pueden existir cachés compartidas para respuestas que lo permitan. Sin embargo, el cifrado limita lo que un intermediario puede inspeccionar o almacenar sin una configuración específica.

## 4. Ventajas y desventajas

### 4.1. Ventajas

- **Control centralizado:** permite aplicar políticas de acceso desde un punto común.
- **Registro y diagnóstico:** facilita revisar las peticiones y detectar determinados problemas.
- **Ahorro de ancho de banda:** la caché puede evitar descargar repetidamente algunos recursos.
- **Funciones de protección:** puede añadir controles sobre las peticiones que gestiona.
- **Gestión de servidores web:** un proxy inverso puede centralizar el acceso y distribuir peticiones entre varios servidores.

### 4.2. Desventajas y limitaciones

- **Privacidad:** los registros pueden revelar información sobre la navegación de los usuarios.
- **Punto de fallo:** si el proxy es obligatorio y deja de funcionar, los clientes pueden perder el acceso al servicio que depende de él.
- **Administración:** las reglas y la autenticación requieren configuración y mantenimiento.
- **Compatibilidad:** algunos protocolos o aplicaciones pueden necesitar una configuración especial o no funcionar a través de un proxy determinado.
- **Rendimiento:** un servidor insuficiente o mal configurado puede convertirse en un cuello de botella.
- **Límites de seguridad:** instalar un proxy no garantiza por sí mismo una red segura.

## 5. Configuraciones y características del proxy

Las siguientes características describen aspectos diferentes del funcionamiento del proxy; no son todas categorías excluyentes.

### 5.1. Proxy explícito

En un **proxy explícito**, el cliente sabe que debe utilizar un proxy y se configura para enviarle las peticiones. La configuración puede hacerse manualmente, mediante políticas del sistema o con mecanismos de configuración automática.

Es una forma habitual de introducir un proxy en un entorno de prácticas, ya que permite comprobar fácilmente qué cliente está utilizando el servicio.

### 5.2. Proxy transparente

En un **proxy transparente**, el cliente no necesita configurar explícitamente el proxy. La red dirige determinadas conexiones hacia él mediante mecanismos de red.

El término *transparente* se refiere principalmente a cómo se encamina el tráfico y a que el cliente no tiene que configurar el proxy manualmente. No significa que el proxy nunca modifique la comunicación ni que sea invisible en todos los sentidos.

### 5.3. Proxy anónimo

Un **proxy anónimo** intenta evitar que el servidor de destino conozca directamente la dirección IP del cliente, por ejemplo, al no transmitir esa dirección en determinadas cabeceras HTTP.

El nivel de anonimato depende de la configuración y de la información que se transmita. El propio proxy puede registrar la dirección del cliente, y otros mecanismos pueden permitir identificarlo. Por ello, no debe afirmarse que un proxy anónimo garantiza que la actividad no quede registrada.

## 6. Arquitectura básica de una red con proxy

En una instalación sencilla de forward proxy, la comunicación sigue este recorrido:

1. El cliente genera una petición web.
2. El cliente envía la petición al proxy configurado.
3. El proxy comprueba las reglas de acceso aplicables.
4. Si la petición está permitida, el proxy se comunica con el servidor de destino.
5. El servidor devuelve una respuesta al proxy.
6. El proxy entrega la respuesta al cliente y, si corresponde, registra la operación o almacena la respuesta en caché.

Si la petición está denegada, el proxy no debe reenviarla al destino y devuelve el resultado que corresponda según su configuración.

## 7. Instalación y configuración de un servidor proxy

Una forma de practicar este servicio en Linux es utilizar **Squid**, un software de proxy compatible con el filtrado de acceso y la caché web.

En una práctica de aula se pueden trabajar, al menos, estas tareas:

1. Instalar Squid desde los repositorios de la distribución.
2. Revisar el archivo de configuración del servicio.
3. Identificar el puerto de escucha configurado.
4. Comprobar qué interfaces y clientes pueden utilizar el servicio.
5. Configurar un cliente para enviar sus peticiones web al proxy.
6. Reiniciar o recargar el servicio cuando sea necesario y comprobar su estado.
7. Consultar los registros para verificar las peticiones realizadas.

Los nombres de los paquetes, las rutas de configuración y los comandos concretos pueden variar según la distribución y su versión. Deben seguirse las indicaciones correspondientes al sistema utilizado en el aula.

### 7.1. Listas de control de acceso (ACL)

En Squid, las **ACL** (*Access Control Lists*, listas de control de acceso) permiten definir criterios que después se utilizan en las reglas de acceso.

Pueden servir, por ejemplo, para identificar una subred de clientes, un destino o una franja horaria. Las reglas de autorización y denegación se evalúan según el orden y la lógica definidos por la configuración de Squid.

Al crear las reglas, hay que comprobar que:

- Solo los clientes autorizados pueden utilizar el proxy.
- Las peticiones permitidas funcionan.
- Las peticiones que deben bloquearse son denegadas.
- El proxy no queda abierto para clientes no autorizados de otras redes.

Una configuración incorrecta puede convertir el servicio en un **proxy abierto**, que terceros podrían utilizar sin permiso.

## 8. Pruebas y resolución de problemas

Después de configurar el servicio, conviene realizar pruebas desde el cliente y desde el servidor.

- Comprobar que el servicio está iniciado y escucha en el puerto previsto.
- Verificar que el cliente puede alcanzar la dirección y el puerto del proxy.
- Probar una petición que deba permitirse y otra que deba denegarse.
- Consultar los registros para confirmar qué petición se ha recibido y qué resultado ha obtenido.
- Si una prueba falla, revisar la configuración, las ACL, el cortafuegos, la conectividad y los mensajes de error del servicio.

Las pruebas deben demostrar el comportamiento real del servicio, no limitarse a mostrar que el paquete está instalado.

## 9. Introducción al proxy inverso con Nginx

Como ampliación, se puede estudiar **Nginx** como proxy inverso. Nginx puede recibir peticiones HTTP o HTTPS y reenviarlas a un servidor web interno.

Por ejemplo, el cliente se conecta a Nginx y Nginx reenvía la petición a un servidor web situado en una red interna. El cliente utiliza la dirección pública del proxy inverso y no necesita conocer directamente la dirección del servidor interno.

En una práctica introductoria se puede configurar Nginx para reenviar peticiones a un servidor web y comprobar que la página se muestra a través del proxy. El equilibrio de carga, la terminación TLS y otras opciones pueden dejarse para una ampliación posterior.

## 10. Resumen

- Un proxy actúa como intermediario entre un cliente y otro servidor.
- Un **forward proxy** representa a los clientes al acceder a destinos externos.
- Un **reverse proxy** recibe peticiones destinadas a uno o varios servidores.
- El proxy puede aplicar reglas, registrar peticiones y, en determinadas condiciones, utilizar caché.
- Ocultar la dirección IP del cliente al destino no equivale a garantizar el anonimato.
- Un proxy no sustituye automáticamente a un cortafuegos ni garantiza por sí solo la seguridad.
- Para administrar un proxy es necesario instalarlo, configurarlo, probar las reglas y revisar los registros.

## 11. Actividad práctica propuesta

Configura un forward proxy con Squid en una máquina Linux y utiliza un cliente de la red para comprobar su funcionamiento.

1. Instala y verifica el servicio.
2. Configura el cliente para utilizar el proxy.
3. Crea una regla que permita el acceso desde la subred de prácticas.
4. Añade una restricción de acceso y comprueba que se aplica.
5. Consulta los registros para identificar las peticiones permitidas y denegadas.
6. Comprueba que un cliente no autorizado no puede utilizar el servicio.
7. Documenta la configuración realizada, las pruebas y sus resultados.

**Entrega:** archivo de configuración o fragmentos relevantes, capturas de las pruebas, explicación de las reglas aplicadas y breve conclusión sobre el funcionamiento y las limitaciones del proxy.
