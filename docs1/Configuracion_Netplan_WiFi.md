
# Configuración de una tarjeta de red inalámbrica con Netplan en Ubuntu 22

En Ubuntu 22.04 (y otras versiones recientes), la configuración de la red se gestiona mediante **Netplan**, que utiliza archivos YAML para definir las interfaces de red. 

## Pasos para configurar la red inalámbrica

### 1. Ubica el archivo de configuración de Netplan
Los archivos de configuración de Netplan se encuentran generalmente en el directorio `/etc/netplan/`. Por lo general, habrá un archivo con una extensión `.yaml` (por ejemplo, `01-netcfg.yaml` o `50-cloud-init.yaml`).

Para ver el archivo existente:
```bash
ls /etc/netplan/
```

---

### 2. Edita o crea el archivo de configuración
Abre el archivo de configuración con un editor de texto (por ejemplo, `nano`):
```bash
sudo nano /etc/netplan/01-netcfg.yaml
```

---

### 3. Define la configuración de la red inalámbrica
Agrega o edita el contenido del archivo para incluir la configuración de tu red inalámbrica. Asegúrate de ajustar los valores como el nombre del adaptador, el SSID y la contraseña.

#### Ejemplo básico (DHCP activado):
```yaml
network:
  version: 2
  renderer: networkd  # O usa NetworkManager dependiendo del entorno
  wifis:
    wlan0:  # Nombre de tu tarjeta de red inalámbrica (puedes verificarlo con `ip link`)
      dhcp4: true  # Usar DHCP para obtener una dirección IP automáticamente
      access-points:
        "NombreDelSSID":  # Reemplaza con el nombre de tu red Wi-Fi
          password: "TuContraseña"  # Reemplaza con la contraseña de tu red Wi-Fi
```

#### Ejemplo con IP estática:
```yaml
network:
  version: 2
  renderer: networkd
  wifis:
    wlan0:
      dhcp4: false
      addresses:
        - 192.168.1.100/24  # IP estática
      gateway4: 192.168.1.1  # Dirección de la puerta de enlace
      nameservers:
        addresses:
          - 8.8.8.8  # DNS primario
          - 8.8.4.4  # DNS secundario
      access-points:
        "NombreDelSSID":
          password: "TuContraseña"
```

---

### 4. Aplica la configuración
Después de guardar los cambios, aplica la nueva configuración de red con el siguiente comando:
```bash
sudo netplan apply
```

---

### 5. Verifica la conexión
Para confirmar que la conexión inalámbrica está activa:
```bash
ip a
```
Busca que el adaptador inalámbrico (por ejemplo, `wlan0`) tenga una dirección IP asignada.

---

## Notas adicionales:
- **Nombre de la interfaz inalámbrica:** Usa `ip link` o `iwconfig` para identificar el nombre exacto de la tarjeta inalámbrica, como `wlan0` o `wlp2s0`.
- **Errores de YAML:** Asegúrate de que el archivo YAML sea válido, respetando la indentación (usa solo espacios, no tabulaciones). Puedes verificarlo con:
  ```bash
  sudo netplan try
  ```

¡Listo! Tu tarjeta de red inalámbrica debería estar configurada correctamente.
