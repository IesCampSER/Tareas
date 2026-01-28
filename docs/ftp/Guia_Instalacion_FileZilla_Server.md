# Guía de Instalación de FileZilla Server

Esta guía te explicará paso a paso cómo instalar el servidor de FileZilla, un software gratuito y de código abierto para la transferencia de archivos a través del protocolo FTP.

---

## Requisitos Previos

- **Sistema Operativo:** Windows (FileZilla Server está diseñado principalmente para Windows).
- **Permisos de Administrador:** Necesarios para instalar software en el sistema.

---

## Pasos para Instalar FileZilla Server

1. **Descargar el Instalador**

   - Ve al sitio web oficial de FileZilla: [https://filezilla-project.org](https://filezilla-project.org).
   - Haz clic en **Download FileZilla Server** para obtener la última versión del instalador.

2. **Ejecutar el Instalador**

   - Abre el archivo descargado (normalmente llamado `FileZilla_Server_x.x.x_win64-setup.exe`).
   - Si aparece una ventana de control de usuario, haz clic en **Sí** para permitir que el instalador se ejecute.

3. **Configuración del Instalador**

   - Selecciona el idioma y haz clic en **OK**.
   - Acepta los términos de licencia y haz clic en **Next**.
   - Elige los componentes que deseas instalar y haz clic en **Next**.

4. **Configurar el Servidor de FileZilla**

   - Selecciona las opciones de inicio para el servicio de FileZilla Server:
     - **Automático** (recomendado para uso constante) o **Manual**.
   - Configura el puerto para la administración (por defecto es el `14147`).

5. **Completar la Instalación**

   - Haz clic en **Install** para iniciar la instalación.
   - Una vez completado, selecciona la opción para iniciar FileZilla Server Interface y haz clic en **Finish**.

6. **Configuración Inicial del Servidor**

   - Al iniciar por primera vez, se abrirá la interfaz de FileZilla Server.
   - Ingresa `localhost` como servidor y el puerto de administración configurado (por defecto `14147`).
   - Puedes configurar usuarios, permisos y carpetas compartidas desde la pestaña de **Users**.

---

## Conexión al Servidor de FileZilla

Para conectar al servidor desde un cliente FTP, utiliza la dirección IP del servidor, el puerto (por defecto es el `21` para FTP) y las credenciales de usuario configuradas.

---

## Recursos Adicionales

- [Documentación de FileZilla Server](https://wiki.filezilla-project.org/Documentation)
