# Configurar dos adaptadores de red en VirtualBox

En esta guía vamos a configurar una máquina virtual para que tenga dos adaptadores de red:

- **Adaptador 1: Red interna**, para comunicarse con otras máquinas virtuales conectadas a la misma red interna.
- **Adaptador 2: NAT**, para acceder a Internet a través de la conexión del equipo anfitrión.

## 1. Apagar la máquina virtual

Antes de cambiar la configuración de red, apaga completamente la máquina virtual. No basta con dejarla en pausa o en estado guardado.

## 2. Configurar el primer adaptador: Red interna

1. Abre VirtualBox.
2. Selecciona la máquina virtual que quieres configurar.
3. Pulsa **Configuración**.
4. En el menú lateral, entra en **Red**.
5. Abre la pestaña **Adaptador 1**.
6. Marca **Habilitar adaptador de red**, si todavía no está marcado.
7. En **Conectado a**, selecciona **Red interna**.
8. En el campo **Nombre**, elige una red interna existente o escribe un nombre nuevo. Las máquinas virtuales que deban comunicarse entre sí tendrán que usar exactamente el mismo nombre de red interna.

Este adaptador se utilizará para la comunicación con otras máquinas de esa red interna. Por sí solo, no proporciona acceso a Internet.

## 3. Configurar el segundo adaptador: NAT

1. Sin salir de la sección **Red**, abre la pestaña **Adaptador 2**.
2. Marca **Habilitar adaptador de red**.
3. En **Conectado a**, selecciona **NAT**.
4. Deja el resto de opciones con sus valores predeterminados, salvo que la práctica indique otra cosa.
5. Pulsa **Aceptar** para guardar los cambios.

El adaptador NAT permite que la máquina virtual acceda al exterior utilizando la conexión de red del equipo anfitrión. VirtualBox suele asignar automáticamente una dirección IP a este adaptador mediante DHCP.

## 4. Comprobar la configuración en Linux

Arranca la máquina virtual e inicia sesión. Abre un terminal y ejecuta:

```bash
ip a
```

El comando muestra las interfaces de red y sus direcciones IP. Deben aparecer dos interfaces Ethernet, además de la interfaz de bucle local `lo`.

Los nombres pueden variar según la distribución y la máquina virtual; por ejemplo, `enp0s3`, `enp0s8`, `eno1` o `eth0`.

Comprueba que:

- La interfaz conectada a **Red interna** tiene la dirección IP que corresponda a la configuración de la práctica. Puede ser una IP estática o configurarse de otra manera según las instrucciones.
- La interfaz conectada a **NAT** está activa y, normalmente, recibe una dirección IP automáticamente mediante DHCP.

Para consultar la ruta por defecto, ejecuta:

```bash
ip route
```

Si quieres que la máquina tenga acceso a Internet mediante NAT, comprueba que la ruta `default` utiliza la interfaz NAT. Si la configuración no es la esperada, revisa las opciones de red y la configuración IP del sistema operativo.

## 5. Probar el acceso a Internet

Prueba a resolver un nombre de dominio y comprobar la conectividad, por ejemplo:

```bash
ping -c 4 8.8.8.8
ping -c 4 example.com
```

Si el primer comando funciona, pero el segundo no, puede haber un problema de resolución DNS. Si ninguno funciona, revisa la configuración del adaptador NAT y la ruta por defecto.

## 6. Resumen

| Adaptador | Modo | Función |
|---|---|---|
| Adaptador 1 | Red interna | Comunicación con las máquinas virtuales conectadas a la misma red interna. |
| Adaptador 2 | NAT | Acceso al exterior a través de la conexión del equipo anfitrión. |

> **Importante:** las direcciones IP y los nombres de las interfaces pueden ser distintos en cada equipo. No copies una dirección IP o un nombre de interfaz de un ejemplo sin comprobar primero los valores de tu propia máquina virtual.

**Licencia:** Lourdes Tamarit. Esta obra está bajo una licencia Creative Commons Atribución-CompartirIgual 4.0 Internacional (CC BY-SA 4.0).
