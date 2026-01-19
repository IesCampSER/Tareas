# Práctica ASIR  
## Despliegue de un servidor de mensajería XMPP (Openfire) con Docker

## 1. Objetivo
Desplegar y administrar un servidor de mensajería instantánea **Openfire** utilizando **contenedores Docker**, garantizando persistencia de datos y acceso desde clientes XMPP.

---

## 2. Requisitos previos
- Sistema Linux con Docker instalado y operativo  
- Acceso a terminal con permisos de usuario  
- Navegador web  
- Máquina cliente Linux y/o Windows  

---

## 3. Imagen Docker utilizada
Se utiliza la imagen mantenida por el proyecto Openfire:

```
igniterealtime/openfire:latest
```

Se trabajará siempre con la **última versión estable disponible**.

---

## 4. Creación del volumen persistente

```bash
mkdir -p /home/$USER/docker-volumes/openfire
```

Este volumen almacenará:
- Configuración del servidor
- Usuarios
- Historial de mensajes
- Plugins

---

## 5. Despliegue del contenedor

```bash
docker run -d \
  --name openfire \
  --restart unless-stopped \
  -p 9090:9090 \
  -p 5222:5222 \
  -p 5269:5269 \
  -v /home/$USER/docker-volumes/openfire:/var/lib/openfire \
  igniterealtime/openfire:latest
```

### Puertos utilizados
| Puerto | Función |
|------|--------|
| 9090 | Consola web de administración |
| 5222 | Conexión de clientes XMPP |
| 5269 | Comunicación entre servidores XMPP |

---

## 6. Acceso y configuración inicial
Desde un navegador web:

```
http://localhost:9090
```

Seguir el asistente:
1. Selección de idioma  
2. Dominio (localhost en entorno educativo)  
3. Base de datos integrada  
4. Creación del usuario administrador  

---

## 7. Configuración mínima de seguridad
- Activar **TLS** para conexiones cliente  
- Usar contraseñas robustas  
- Desactivar puertos inseguros no utilizados  

No se permiten credenciales por defecto.

---

## 8. Creación de usuarios
Desde la consola web:
- Usuarios / Grupos → Crear usuario  
- Crear al menos dos usuarios para pruebas  

---

## 9. Instalación de clientes XMPP

### Cliente Linux
```bash
sudo apt update
sudo apt install pidgin
```

### Cliente Windows
- Descargar Pidgin desde su web oficial  
- Configurar cuenta XMPP con cifrado activado  

---

## 10. Pruebas de funcionamiento
- Envío de mensajes entre usuarios  
- Comprobación de estados de conexión  
- Revisión de logs:

```bash
docker logs openfire
```

---

## 11. Verificación de persistencia
1. Detener el contenedor  
```bash
docker stop openfire
```

2. Eliminarlo  
```bash
docker rm openfire
```

3. Volver a desplegarlo usando el mismo volumen  
4. Verificar que usuarios y configuración se conservan  

---

## 12. Cuestiones finales
- Ventajas de Docker frente a instalación tradicional  
- Importancia de la persistencia de datos  
- Riesgos de exponer el servicio sin TLS  
- Otros métodos de autenticación soportados por Openfire  

