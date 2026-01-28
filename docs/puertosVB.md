
# Reenvío de Puertos en VirtualBox

El reenvío de puertos en VirtualBox permite acceder a servicios dentro de una máquina virtual desde el host (o incluso desde otros equipos en la red). Es especialmente útil cuando necesitas acceder a un servidor web o SSH en la máquina virtual.

## Pasos para configurar el reenvío de puertos:

1a. **Si tienes un adaptador de red Nat (si tienes la configuración de clase este NO es nuestro caso):**
   - Inicia VirtualBox y selecciona la máquina virtual en la que deseas configurar el reenvío de puertos.
   - Haz clic derecho sobre la máquina virtual y selecciona **Configuración**.
   - En el panel de configuración, selecciona la opción **Red**.
   - En la sección del adaptador de red, haz clic en el botón de **Avanzadas** para desplegar más opciones.

1b. **Si tienes un adaptador de red RedNat (nuestro caso).**
   - Inicia VirtualBox y selecciona la opción del menú Archivo-Preferencias **Red**.
   - Edita la RedNat seleccionada (icono de la derecha)
2. **Agregar reglas de reenvío de puertos:**
   - Haz clic en **Reenvío de puertos** para abrir el menú de configuración.
   - En esta ventana, puedes agregar nuevas reglas de reenvío de puertos.

3. **Configurar una regla de reenvío de puertos:**
   - En el campo de **Nombre**, puedes escribir un nombre descriptivo para la regla.
   - En **Protocolo**, selecciona TCP o UDP dependiendo de tu necesidad.
   - En el campo **IP del anfitrión**, puedes dejarlo en blanco o usar `127.0.0.1` si solo deseas acceder desde el host.
   - En **Puerto del anfitrión**, introduce el puerto del host que se usará para redirigir el tráfico.
   - En **IP del invitado**, si tienes la configuración de clase puedes usar `192.168.20.5`.
   - En **Puerto del invitado**, introduce el puerto en la máquina virtual al que deseas redirigir el tráfico (por ejemplo, el puerto 80 para un servidor web o el 22 para SSH).

4. **Guardar y reiniciar la máquina virtual:**
   - Una vez configurado el reenvío de puertos, guarda los cambios y reinicia la máquina virtual para que la nueva regla surta efecto.

## Ejemplo de configuración

Supongamos que quieres acceder a un servidor web que corre en el puerto 80 de tu máquina virtual:

- **Protocolo:** TCP
- **IP del anfitrión:** 127.0.0.1
- **Puerto del anfitrión:** 8080 (esto permitirá acceder al servidor web desde `http://localhost:8080`)
- **IP del invitado:** 192.168.20.5
- **Puerto del invitado:** 80

Con esta configuración, cualquier tráfico dirigido a `localhost:8080` en el sistema host será reenviado al puerto 80 de la máquina virtual, donde el servidor web está corriendo.

## Comprobación

¿Como puedo saber si tengo los puertos abiertos? habréis dado esto en redes de primero pero os dejo aquí un pequeño recordatorio

[Comandos para obtener información de puertos](puertos-comandos.md)

Si ejecutáis esto en la máquina virtual donde acabáis de instalar el servicio FTP debería daros como resultado que el puerto 21 está abierto

Si ejecutáis esto en la máquina física después de configurar el reenvío debería daros también que el puerto 21 está abierto
