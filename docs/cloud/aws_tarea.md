SERVICIOS EN RED · 2.º ASIR

# DESPLIEGUE DE UNA INFRAESTRUCTURA CLOUD EN AWS

Diseño, automatización y comprobación de servicios de red

# 1. Situación planteada

Una empresa necesita desplegar en la nube una infraestructura básica para alojar la página web de su intranet, almacenar documentos y gestionar la información mediante una base de datos relacional. Como técnicos de sistemas, deberéis diseñar y desplegar la infraestructura utilizando los servicios de AWS disponibles en AWS Academy Learner Lab.

El objetivo no es únicamente crear los recursos: también debéis comprender para qué sirve cada servicio, cómo se relacionan entre sí y qué consecuencias tienen las decisiones de configuración.

# 2. Objetivos

- Diseñar una red virtual propia mediante Amazon VPC y dividirla en subredes.
- Configurar subredes públicas y privadas, tablas de rutas e Internet Gateway.
- Desplegar una instancia EC2 con Apache instalado automáticamente.
- Controlar el tráfico mediante grupos de seguridad.
- Crear un bucket privado de Amazon S3.
- Desplegar una base de datos Amazon RDS MySQL en subredes privadas.
- Automatizar la creación de recursos mediante un script y verificar el resultado desde la consola.
- Justificar las decisiones de diseño y explicar el funcionamiento de los recursos.

# 3. Arquitectura que debéis desplegar

La infraestructura debe incluir, como mínimo, los siguientes elementos:

- Una VPC con el rango IPv4 10.20.0.0/16.
- Una subred pública 10.20.1.0/24 para el servidor web.
- Dos subredes privadas: 10.20.2.0/24 y 10.20.3.0/24, situadas en zonas de disponibilidad diferentes y destinadas al servicio de base de datos.
- Un Internet Gateway asociado a la VPC.
- Una tabla de rutas pública asociada a la subred pública, con una ruta por defecto hacia el Internet Gateway.
- Una tabla de rutas para las subredes privadas, sin acceso directo desde Internet.
- Una instancia EC2 en la subred pública, con IPv4 pública y Amazon Linux 2023.
- Apache instalado y arrancado automáticamente mediante User Data.
- Un grupo de seguridad web que permita HTTP (TCP 80) desde Internet. No abráis SSH indiscriminadamente a todo Internet.
- Un bucket S3 privado, con el bloqueo de todo el acceso público activado.
- Una instancia Amazon RDS MySQL utilizando un grupo de subredes que incluya las dos subredes privadas.
- Un grupo de seguridad de RDS que permita MySQL (TCP 3306) únicamente desde el grupo de seguridad del servidor web.

No es necesario crear una NAT Gateway. La instancia web se ubicará en la subred pública y la base de datos no debe ser accesible públicamente.

# 4. Trabajo que debéis realizar

### Fase 1. Diseño de la arquitectura

Antes de ejecutar el script, preparad un esquema con la VPC, las tres subredes, las tablas de rutas, el Internet Gateway, EC2, S3 y RDS. Indicad qué recursos están dentro de la VPC y cuáles son servicios gestionados que no se despliegan como máquinas dentro de ella.

**Cuestiones de reflexión que debéis entregar:**

- ¿Por qué se crea una VPC propia en lugar de utilizar la VPC predeterminada?
- ¿Qué diferencia hay entre una región de AWS y una zona de disponibilidad?
- ¿Por qué se utilizan dos subredes privadas en zonas de disponibilidad diferentes para RDS?
- ¿Qué recursos necesitan recibir conexiones desde Internet y cuáles deben permanecer aislados?
---  
### Fase 2. Creación de la VPC y las subredes

Iniciad AWS Academy Learner Lab desde Vocareum y comprobad que el laboratorio está activo. Ejecutad el script **desarrollado con ayuda de una herramienta de IA**. Después, localizad las redes en la consola y comprobad su configuración.

**Cuestiones de reflexión que debéis entregar:**

- ¿Qué representa el rango 10.20.0.0/16 de la VPC?
- ¿Qué rangos tienen las tres subredes? ¿Pertenecen todas a la misma VPC?
- ¿En qué zonas de disponibilidad se han creado las subredes privadas? ¿Por qué interesa que estén en zonas diferentes?
- ¿Se solapan los rangos IP de las subredes? ¿Qué problema produciría un solapamiento?
- ¿Podrían existir dos VPC distintas con el mismo rango de direcciones IP?
---  
### Fase 3. Internet Gateway y tablas de rutas

Comprobad que el Internet Gateway está asociado a la VPC, que la tabla de rutas pública está asociada a la subred pública y que las subredes privadas utilizan su tabla de rutas correspondiente.

**Cuestiones de reflexión que debéis entregar:**

- ¿Qué significa la ruta 0.0.0.0/0? ¿A qué recurso apunta en la tabla pública?
- ¿Qué función realiza el Internet Gateway?
- ¿Qué rutas aparecen en la tabla asociada a las subredes privadas? ¿Tienen una ruta directa hacia el Internet Gateway?
- ¿Qué ocurriría con el acceso a Internet de la instancia web si eliminaseis la ruta por defecto de la tabla pública?
- ¿Una subred es pública únicamente por tener un rango IP determinado? Explicad qué condiciones hacen que una subred sea pública.

---  

### Fase 4. Servidor web EC2 y grupo de seguridad

Comprobad que EC2 se ha creado en la subred pública y tiene una dirección IPv4 pública. El script debe instalar y arrancar Apache mediante User Data. Cuando la instancia esté disponible, acceded desde un navegador a su dirección IP pública.

**Cuestiones de reflexión que debéis entregar:**

- ¿Qué permite la regla de entrada TCP 80 desde 0.0.0.0/0?
- ¿Qué sucedería si eliminaseis esa regla del grupo de seguridad?
- ¿Por qué no es recomendable permitir SSH desde 0.0.0.0/0?
- ¿Qué diferencia hay entre un grupo de seguridad y una tabla de rutas?
- ¿Qué es User Data y cuándo se ejecuta? ¿Qué ventaja tiene frente a instalar Apache manualmente después de crear la instancia?
- Que EC2 aparezca en estado «Running», ¿garantiza que Apache esté funcionando? ¿Cómo lo comprobaríais?
---  
### Fase 5. Almacenamiento con Amazon S3

Localizad el bucket creado, comprobad su configuración y subid un pequeño archivo de prueba. Verificad que el bucket mantiene bloqueado el acceso público.

**Cuestiones de reflexión que debéis entregar:**

- ¿Qué diferencia hay entre almacenar un archivo en el disco de EC2 y almacenarlo en S3?
- ¿El bucket S3 está dentro de la VPC? Explicad dónde encaja este servicio en la arquitectura.
- ¿Por qué se activa el bloqueo de acceso público?
- Si se elimina la instancia EC2, ¿se eliminan también los objetos almacenados en S3?
- Si la aplicación alojada en EC2 necesitase guardar archivos en S3, ¿cómo le daríais permisos de forma segura? Investigad el uso de roles de IAM.

---  

### Fase 6. Base de datos Amazon RDS MySQL

Desplegad una instancia Amazon RDS con motor MySQL. Configurad un grupo de subredes de base de datos que incluya las dos subredes privadas creadas en la práctica. La opción «Public access» o «Publicly accessible» debe estar desactivada. Configurad un grupo de seguridad que permita TCP 3306 únicamente desde el grupo de seguridad de la instancia web.

Anotad el identificador de la instancia, motor y versión, clase de instancia, zona de disponibilidad indicada por AWS, nombre de la base de datos (si se ha definido) y endpoint. No incluyáis contraseñas en el documento ni en capturas.

**Cuestiones de reflexión que debéis entregar:**

- ¿Para qué sirve un grupo de subredes de base de datos (DB subnet group)? ¿Por qué debe incluir subredes de zonas de disponibilidad diferentes?
- ¿Qué implica que RDS no sea públicamente accesible?
- ¿Qué origen habéis indicado en la regla de entrada del grupo de seguridad de RDS? ¿Por qué es preferible indicar el grupo de seguridad de EC2 en lugar de una dirección IP concreta?
- ¿Qué riesgo tendría permitir el puerto 3306 desde 0.0.0.0/0?
- ¿Qué ventajas ofrece Amazon RDS frente a instalar y administrar MySQL directamente en EC2?
- Si elimináis la instancia EC2, ¿se elimina automáticamente la base de datos RDS?
- ¿Tener subredes en dos zonas de disponibilidad dentro del DB subnet group significa necesariamente que la base de datos esté configurada en Multi-AZ? Explicad la diferencia.


# 7. Comprobaciones y evidencias que debéis entregar

No basta con indicar que el script se ha ejecutado correctamente. Debéis demostrar que los recursos existen y que su configuración coincide con los requisitos.

- Esquema de la arquitectura final, con nombres de recursos y rangos IP.
- Captura de la VPC y sus subredes, donde se identifiquen rangos y zonas de disponibilidad.
- Captura de las tablas de rutas y sus asociaciones.
- Captura del Internet Gateway asociado a la VPC.
- Captura de EC2 con estado, subred y dirección IP pública.
- Captura del navegador mostrando la página servida por Apache. Modificad previamente la página a servir con algo que os identifique.
- Evidencia de la regla HTTP del grupo de seguridad de EC2.
- Captura del bucket S3 y de la configuración de bloqueo de acceso público.
- Captura de RDS donde se vea motor, estado, accesibilidad pública desactivada y grupo de subredes.
- Captura de las reglas del grupo de seguridad de RDS, mostrando que TCP 3306 solo admite el grupo de seguridad web como origen.
- Respuestas razonadas a las preguntas de reflexión de cada fase.

Las capturas deben permitir leer la información relevante.


# 8. Actividad final: ¿qué ocurriría si...?. OPCIONAL

Responded justificando qué recurso o configuración revisaríais y por qué.

1. La instancia EC2 aparece como «Running», pero no se puede acceder a la página web desde el navegador.
2. Se elimina el Internet Gateway de la VPC. ¿Qué comunicaciones dejarían de funcionar?
3. La empresa necesita desplegar un segundo servidor web. ¿Es necesario crear otra VPC, otro Internet Gateway o nuevas tablas de rutas? ¿Qué recursos podrían reutilizarse?
4. La empresa quiere almacenar documentos sin depender del disco de EC2. ¿Qué servicio utilizaríais y por qué?
5. Un usuario intenta conectarse a MySQL desde su ordenador personal a través de Internet. ¿Debería poder hacerlo con la configuración solicitada? Explicadlo.
6. Se elimina EC2, pero la empresa quiere conservar los documentos y la base de datos. ¿Qué recursos deberían mantenerse y qué aspectos habría que comprobar antes de borrar nada?

# 9. Ampliación: modificación de la infraestructura. OPCIONAL

Añadid una segunda instancia EC2 que también ejecute Apache. Antes de modificar el script, indicad qué recursos nuevos serían necesarios y cuáles podrían reutilizarse.

- Modificad el script para crear la segunda instancia.
- Decidid en qué subred se desplegará y justificad la decisión.
- Determinad si necesita un nuevo grupo de seguridad o puede utilizar el existente.
- Comprobad que ambas instancias sirven correctamente la página web.
- Explicad qué ocurriría si se elimina una de las dos instancias.

# 10. Entrega

- Entregad el script utilizado y el documento con las respuestas y evidencias solicitadas.
- El script debe estar comentado y utilizar nombres claros para los recursos.
- Debéis poder explicar el propósito de cada recurso y justificar las principales decisiones de configuración.
- Las respuestas deben basarse en la infraestructura desplegada. Siempre que sea posible, acompañadlas de una captura o comprobación realizada en la consola.
- Podéis utilizar herramientas de IA para comprender los servicios, preparar o revisar el script y resolver errores. Debéis revisar el código, comprobar qué recursos crea y ser capaces de explicar su funcionamiento.
