
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
### Localizar el archivo de configuración de red:
Los archivos de configuración suelen estar en:
```bash
/etc/netplan/
```

### Crear un archivo YAML si no existe:
Si no existe, crea uno usando `nano` o tu editor preferido:
```bash
sudo nano /etc/netplan/01-netcfg.yaml
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
