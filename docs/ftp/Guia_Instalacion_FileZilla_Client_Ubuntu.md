# Guía de Instalación de FileZilla Client en Ubuntu 22.04

FileZilla Client es una aplicación de código abierto y gratuita que permite la transferencia de archivos a través de los protocolos FTP, SFTP y FTPS. Esta guía te ayudará a instalar el cliente de FileZilla en una máquina con Ubuntu 22.04.

---

## Requisitos Previos

- **Sistema Operativo:** Ubuntu 22.04 o una versión compatible.
- **Permisos de Usuario:** Asegúrate de tener acceso sudo para realizar la instalación.

---

## Pasos para Instalar FileZilla Client

1. **Actualizar los Repositorios del Sistema**

   Es una buena práctica actualizar los repositorios de paquetes antes de realizar cualquier instalación. Abre una terminal y ejecuta:

   ```bash
   sudo apt update
   ```

2. **Instalar FileZilla Client**

   FileZilla Client está disponible en los repositorios oficiales de Ubuntu, por lo que puedes instalarlo directamente con el siguiente comando:

   ```bash
   sudo apt install -y filezilla
   ```

3. **Verificar la Instalación**

   Una vez que el proceso de instalación se complete, puedes verificar que FileZilla se haya instalado correctamente ejecutando:

   ```bash
   filezilla --version
   ```

   Esto debería mostrar la versión de FileZilla instalada en tu sistema.

4. **Iniciar FileZilla Client**

   - Para abrir FileZilla, ve al menú de aplicaciones y busca **FileZilla**, o abre una terminal y escribe:

     ```bash
     filezilla
     ```

---

## Configuración Básica de FileZilla Client

Al iniciar FileZilla Client, puedes conectar fácilmente a un servidor FTP, SFTP o FTPS. Los pasos básicos son:

1. En la parte superior de la ventana, ingresa la **Dirección del servidor**, **Nombre de usuario**, **Contraseña** y **Puerto**.
2. Haz clic en **Conexión rápida** para conectarte directamente, o utiliza el **Gestor de Sitios** para guardar detalles de conexión.

---

## Recursos Adicionales

- [Documentación de FileZilla](https://wiki.filezilla-project.org/Documentation)
- [Soporte de Ubuntu](https://help.ubuntu.com/)
