
# Configuración de Red en Ubuntu 22.04 (Sin Entorno Gráfico)

## 1. Obtención de Información sobre Interfaces de Red
### Comando para listar interfaces de red:
```bash
ip a
```
Muestra todas las interfaces y su estado (activas/inactivas).

### Comando para información detallada de una interfaz específica:
```bash
ip addr show <interfaz>
```
Reemplaza `<interfaz>` con el nombre de la interfaz, por ejemplo, `eth0` o `ens33`.

### Ver todas las rutas de red configuradas:
```bash
ip route
```

## 2. Habilitar y Deshabilitar una Interfaz de Red
### Para deshabilitar una interfaz:
```bash
sudo ip link set <interfaz> down
```
### Para habilitar una interfaz:
```bash
sudo ip link set <interfaz> up
```
## 3. Comprobación de la Ruta y Puerta de Enlace Predeterminada
### comprobación de la ruta:
Esta opción nos muestra la ruta que un paquete tomará para llegar al destino. Para verificar la información de enrutamiento de la red, ejecutamos el siguiente comando:
```bash
ip route show
ip route s
```
### Verificar la puerta de enlace predeterminada:
```bash
ip route | grep default
```
Esto muestra la ruta predeterminada configurada.

### Comprobar conectividad con la puerta de enlace:
```bash
ping <IP_de_la_puerta_de_enlace>
```
Por ejemplo:
```bash
ping 192.168.1.1
```

## 4. Edición y Creación de Archivos YAML para Configuración de Red
Netplan es la herramienta que incorpora Ubuntu desde la versión 17.05 para la administración y configuración de redes. Ésta se puede usar para definir un archivo en formato YAML ( Yet Another
Markup Language) desde donde se creará la configuración elegida para las redes del sistema que netplan podrá interpretar para aplicar los cambios.

Esta herramienta reemplaza por completo el archivo de configuración de interfaces estáticas alojado bajo la ruta /etc/network/interfaces que se utilizaba en las versiones anteriores de
ubuntu.

La ruta de configuración de las interfaces se aloja bajo la ruta /etc/netplan/*.yaml. Desde la ruta podremos encontrar dos renderers, networkmanager y networkd.

El primero de los renders NetworkManager se utiliza principalmente en entornos de escritorio mientras que NetWorkd en entornos de servidor. Cuando usemos NetworkManager como procesador, el sistema usará el GUI de NetworkManager para administrar las interfaces.

### Localizar el archivo de configuración de red:
Los archivos de configuración suelen estar en:
```bash
/etc/netplan/
```

### Crear un archivo YAML si no existe:
En caso de que este archivo no existiera lo que tendríamos que hacer sería teclear el siguiente comando para que el sistema nos lo creara.
```bash
sudo netplan generate
```
De esta forma netplan se encarga de crear por nosotros el archivo de configuración de interfaces de red.

A continuación recomendamos duplicar el archivo en forma de backup para asegurar la
recuperación en caso de fallo del propio archivo.
```bash
sudo nano /etc/netplan/01-network-manager-all.yaml
```

### Ejemplo de contenido inicial:
```yaml
network:
  version: 2
  ethernets:
    <interfaz>:
      dhcp4: true
```
Reemplaza `<interfaz>` con el nombre de tu interfaz.

## 5. Modificar el Archivo YAML para Configurar una IP Estática
### Ejemplo de configuración para IP estática:
```yaml
network:
  version: 2
  ethernets:
    <interfaz>:
      addresses:
        - 192.168.1.100/24
      gateway4: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```
- `addresses`: IP estática con su máscara (por ejemplo, `/24`).
- `gateway4`: Dirección de la puerta de enlace.
- `nameservers`: Direcciones de servidores DNS.

Dependiendo de tu versión es posible que obtengas el warning gateway has been deprecated, puedes probar con:
```yaml
network:
  version: 2
  ethernets:
    <interfaz>:
      addresses:
        - 192.168.1.100/24
      routes:
        - to: 0.0.0.0/0
          via: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```
Si deseas configurar varias tarjetas de red añade a continuación tantos apartados interfaz como tarjetas
### Guardar y salir del editor:
Si usas `nano`, presiona `Ctrl+O` para guardar y `Ctrl+X` para salir.

## 6. Aplicación de los Cambios
### Aplicar la configuración del archivo YAML:
```bash
sudo netplan apply
```

### Verificar si los cambios se aplicaron correctamente:
```bash
ip a
ip route
```

### Solución de problemas:
Si algo sale mal, verifica los errores con:
```bash
sudo netplan try
```

---
**Nota:** Asegúrate de que la sintaxis del archivo YAML sea correcta, ya que errores de formato pueden causar problemas al aplicar los cambios.
