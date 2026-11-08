# 🛠️ Laboratorio de Troubleshooting y Hacking (VMware & Kali Linux)

Este repositorio contiene documentación técnica sobre problemas reales resueltos durante la configuración de entornos virtualizados (VMware) y notas de intrusión en máquinas de entrenamiento (Hack The Box / DockerLabs). Todo el contenido está enfocado en la resolución de incidencias (HelpDesk / SysAdmin) y en prácticas controladas de ciberseguridad.

---

## 🛑 Troubleshooting en VMware Workstation Player

### Incidencia 1: Error al intentar ejecutar una máquina virtual de Kali Linux descargada
**Síntoma:** Al intentar abrir el archivo descargado (`kali-linux-2025.4-vmware-amd64.7z`) directamente en VMware, o al extraerlo e intentar ejecutar un archivo `.vmdk`, VMware muestra una pantalla en negro o lanza un error de incompatibilidad.
**Causa:**
1. Intentar cargar el archivo directamente desde un formato comprimido (`.7z`).
2. Confundir los discos virtuales duros (`.vmdk`) con el archivo de configuración de la máquina (`.vmx`).

**Solución aplicada:**
1. Descomprimir por completo el archivo `.7z` usando una herramienta como 7-Zip para extraer la carpeta con todos los componentes de la máquina virtual.
2. Abrir **VMware Workstation Player** desde Windows (el sistema anfitrión).
3. Seleccionar la opción `Open a Virtual Machine`.
4. Navegar hasta la carpeta descomprimida y seleccionar **exclusivamente el archivo con extensión `.vmx`** (el archivo de configuración).
5. Iniciar la máquina desde la lista principal del programa.

### Incidencia 2: La máquina virtual se queda "congelada" o no inicia (Archivos `.lck`)
**Síntoma:** Una máquina virtual (nodo) no responde, no arranca, o muestra un error indicando que está en uso, generalmente después de un cierre inesperado.
**Causa:** VMware crea archivos/carpetas de bloqueo temporal (cerrojos) con la extensión `.lck` para evitar que dos procesos modifiquen la misma máquina a la vez. Si el programa se cierra mal, estos archivos no se eliminan automáticamente.

**Solución aplicada:**
1. Forzar el cierre de cualquier proceso de VMware residual desde el Administrador de Tareas de Windows (buscar `vmware.exe`).
2. Acceder a la ruta física donde está guardada la máquina virtual.
3. Localizar y eliminar manualmente los archivos o carpetas que terminen en `.lck`.
4. Reiniciar VMware y encender la máquina virtual con normalidad.

### Incidencia 3: El cursor del ratón se queda atrapado y no funciona el portapapeles (Copy/Paste)
**Síntoma:** Al hacer clic dentro de la máquina virtual de Kali Linux, el ratón no puede salir de la ventana hacia Windows, y la opción de copiar y pegar texto entre el anfitrión y el invitado está desactivada.
**Causa:** Faltan las herramientas de integración del sistema invitado (VMware Tools) o el servicio que las gestiona no está activo.

**Solución aplicada:**
1. Para liberar el ratón temporalmente, utilizar el atajo de teclado: `Ctrl + Alt`.
2. Desde una terminal dentro de Kali Linux, instalar el paquete de integración gráfico:
   ```bash
   sudo apt update && sudo apt install -y open-vm-tools-desktop
