# Práctica: acceso y control remoto con SSH

**Ciclo:** CFGM Sistemas Microinformáticos y Redes (SMR)  
**Módulo:** Servicios en Red  
**Curso:** 2.º  
**Actividad principal:** instalación y configuración básica de OpenSSH

## Antes de comenzar

Para probar el funcionamiento de las actividades, crea en el servidor Ubuntu (`SerLApellido`) los siguientes usuarios:

| Usuario | Contraseña |
|---|---|
| `alumno` | `alumno` |
| `alumnomalo` | `alumno` |

> **Importante:** estas credenciales son únicamente para un entorno de prácticas aislado. No las utilices en equipos reales ni en sistemas expuestos a Internet.

## Actividad 1. Instalación y configuración de OpenSSH

### Objetivo

Instalar el servidor OpenSSH en el servidor Linux y comprobar el acceso remoto desde clientes Linux y Windows. Después, restringiremos el acceso SSH a uno de los usuarios.

### 1. Instalar OpenSSH en el servidor

En el servidor Ubuntu (`SerLApellido`), ejecuta:

```bash
sudo apt update
sudo apt install openssh-server
```

Comprueba que el servicio está activo:

```bash
sudo systemctl status ssh
```

Si fuera necesario, puedes iniciarlo y habilitarlo para que arranque automáticamente:

```bash
sudo systemctl enable --now ssh
```

![Esquema de conexión SSH entre cliente y servidor](/../img/ssh3.jpg)


### 2. Conectarse desde el cliente Linux

Desde el cliente Linux, conéctate al servidor usando el usuario creado anteriormente:

```bash
ssh alumno@sercamp.org
```

En este ejemplo, `alumno` es un usuario existente en el servidor y `sercamp.org` es el nombre DNS del servidor.

- Comprueba que el usuario `alumno` está creado en el servidor.
- Comprueba que `sercamp.org` corresponde al servidor y que el servicio DNS funciona.
- Si no tienes el nombre DNS disponible, puedes conectarte mediante la dirección IP del servidor. En el ejemplo de la práctica sería:

```bash
ssh alumno@192.168.20.5
```

La primera vez que te conectes, SSH puede preguntar si confías en la clave de host del servidor. Comprueba que estás conectándote al equipo correcto antes de aceptarla. Introduce después la contraseña del usuario.

Cuando te autentiques, se abrirá una shell del servidor. Podrás ejecutar comandos como si estuvieras trabajando directamente en él.

En un cliente Linux normalmente no necesitas instalar nada, porque el cliente OpenSSH suele estar disponible o puede instalarse con el paquete `openssh-client`.

Prueba a conectarte desde el cliente Linux con el usuario `alumno`.

### 3. Restringir el acceso de un usuario

El servidor SSH se puede configurar modificando el archivo:

```text
/etc/ssh/sshd_config
```

Edita el archivo con permisos de administrador. Añade o modifica la directiva siguiente para denegar el acceso SSH al usuario `alumnomalo`:

```text
DenyUsers alumnomalo
```

Antes de reiniciar el servicio, comprueba la sintaxis de la configuración:

```bash
sudo sshd -t
```

Si no muestra errores, reinicia el servicio:

```bash
sudo systemctl restart ssh
```

Prueba de nuevo desde el cliente Linux:

- Conéctate con `alumnomalo` y comprueba que se deniega el acceso.
- Conéctate con `alumno` y comprueba que sigue pudiendo acceder.

### 4. Instalar y probar PuTTY

Vamos a probar otro cliente SSH.

1. Instala PuTTY en el cliente Linux.
2. Conéctate desde el cliente Linux al servidor Linux mediante PuTTY.
3. En el cliente Windows, utiliza un cliente SSH, por ejemplo OpenSSH o PuTTY.
4. Conéctate desde el cliente Windows al servidor Linux con el usuario `alumno`.

En PuTTY, indica el nombre DNS o la IP del servidor y el puerto **22**, salvo que el servidor se haya configurado para utilizar otro puerto.

### 5. Comprobar en qué equipo estás trabajando

Una vez conectado por SSH, ejecuta:

```bash
ip a
```

La dirección IP que muestra el comando será la del servidor remoto, porque los comandos se están ejecutando en el servidor, aunque estés usando el teclado y la pantalla del cliente.

Para cerrar la sesión SSH, ejecuta:

```bash
exit
```

#### Ejemplos de referencia

Los ejemplos siguientes utilizan nombres e IP diferentes a los de tu práctica:

- Conexión por nombre: `ssh alumnolinux@dns.iesc.org`
- El cliente tiene la IP `192.168.10.20`.
- El servidor tiene la IP `192.168.10.5`.
- Tras conectarte, el prompt cambia para reflejar el usuario y el equipo remoto.
- Al ejecutar `ip a`, aparecen las direcciones de red del servidor.
- Para desconectarte, ejecuta `exit`.


![Ejemplo de una sesión SSH en el terminal](/../img/ssh4.jpg)

#### Acceso mediante PuTTY

El ejemplo de la imagen muestra una conexión desde el cliente a un servidor mediante PuTTY, usando el puerto 22.

![Ejemplo de conexión con PuTTY](/../img/ssh5.jpg)


### Entrega de la actividad 1

Entrega capturas de pantalla donde se vea:

1. Acceso desde el terminal del cliente Linux al servidor Linux con el usuario `alumno`.
2. Denegación de acceso por SSH al usuario `alumnomalo`.
3. Acceso desde el cliente Windows al servidor Linux con el usuario `alumno`.
4. Acceso desde PuTTY en el cliente Windows al servidor Linux con el usuario `alumno`.

En una de las sesiones remotas, ejecuta `ip a` y muestra que las direcciones corresponden al servidor remoto. Puedes elegir la sesión que mejor permita demostrarlo.

## Actividad 2. Opcional: acceso y asistencia remota con TeamViewer

### Objetivo

Probar una herramienta de acceso y asistencia remota con interfaz gráfica y comparar su funcionamiento con SSH.

1. Consulta la página oficial de TeamViewer y comprueba las condiciones de licencia aplicables a tu uso educativo y personal.
2. Instala la aplicación en los equipos que vayas a utilizar, siguiendo las instrucciones oficiales actuales.
3. Configura el equipo remoto y realiza una conexión desde el cliente.
4. Comprueba el funcionamiento y cierra la sesión al terminar.

Realiza esta actividad únicamente en equipos propios o autorizados, y no compartas códigos de acceso ni contraseñas.

### Entrega

Incluye una captura de pantalla que muestre la conexión de escritorio remoto con TeamViewer, sin exponer identificadores, códigos de acceso ni información personal.

### Preguntas de reflexión

1. ¿Puedes establecer una conexión SSH desde tu casa a un ordenador situado en otra red, por ejemplo, un ordenador del centro? ¿Qué condiciones de red y seguridad serían necesarias?
2. ¿Puedes utilizar TeamViewer para conectarte a un equipo situado en otra red?
3. Explica las diferencias entre SSH y TeamViewer: tipo de acceso, interfaz, usos habituales y requisitos de conexión.
4. ¿Qué configuración o herramientas adicionales permitirían acceder por SSH a un equipo de otra red de forma segura?

## Actividad 3. Opcional: escritorio remoto en Ubuntu

### Objetivo

Activar y probar el escritorio remoto de Ubuntu.

1. Consulta la documentación oficial correspondiente a la versión de Ubuntu instalada y comprueba qué función de escritorio remoto ofrece.
2. Activa y configura el acceso remoto siguiendo esa documentación.
3. Desde otra máquina Linux, intenta conectarte mediante un cliente compatible, como Remmina. También puedes utilizar un cliente Windows compatible con el protocolo configurado.
4. Comprueba el funcionamiento y desactiva el acceso remoto al terminar, si no lo necesitas.


### Entrega

Incluye una captura de pantalla de la conexión al escritorio remoto de Ubuntu. Evita mostrar contraseñas, códigos de acceso o datos personales.
