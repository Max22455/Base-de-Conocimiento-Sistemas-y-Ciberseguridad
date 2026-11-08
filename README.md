# 📚 Base de Conocimiento: Sistemas, Redes y Ciberseguridad
Este repositorio contiene mis apuntes personales, hojas de trucos (*cheat sheets*) y resoluciones de máquinas (*Writeups*) en plataformas como Hack The Box y DockerLabs. Documento mi aprendizaje continuo para agilizar futuras auditorías y tareas de administración de sistemas.
---
## 🛠️ 1. Troubleshooting y Administración Linux
### Solución de Integración en VMware (Portapapeles y Ratón)
Para habilitar copiar/pegar entre Windows y Kali Linux, y evitar que el cursor se quede atrapado, se requiere instalar los controladores gráficos de VMware y configurar el aislamiento.
```bash
# 1. Instalar el paquete de integración
sudo apt update && sudo apt install -y open-vm-tools-desktop
# 2. Reiniciar el servicio de integración
sudo systemctl restart open-vm-tools
```
> **Nota:** Si las opciones "Guest Isolation" están en gris en VMware, apaga la máquina por completo, entra en `Settings > Options > Guest Isolation` y marca **"Enable copy and paste"**.
---
### Configuración del Idioma del Sistema (Locales)
Para corregir problemas de codificación de caracteres en la terminal:
```bash
sudo dpkg-reconfigure locales
```
* **Paso clave:** En la lista larga, busca con las flechas `es_ES.UTF-8 UTF-8` y pulsa la **Barra Espaciadora** para asegurarte de que aparece el asterisco `[*]` a la izquierda. Luego pulsa `Enter`.
---
### Gestión del Archivo Hosts
Para resolver dominios locales en laboratorios CTF sin necesidad de configurar un servidor DNS:
```bash
echo "10.129.244.96  facts.htb" | sudo tee -a /etc/hosts
```
---
## 🐧 2. Cheat Sheet: Comandos Esenciales de Linux
### Navegación y Gestión de Archivos
* `pwd` : Muestra la ruta de la estructura de directorios actual.
* `ls -la` : Lista todos los archivos (incluyendo los ocultos) con detalles de permisos.
* `tree` : Muestra la estructura de directorios en forma de árbol visual.
* `cp [archivo]` / `mv [archivo] [nuevo_nombre]` : Copiar / Mover o renombrar archivos.
* `rm -r [directorio]` : Eliminar de forma recursiva un directorio y todo su contenido.
* `cat [archivo]` : Muestra por pantalla el contenido de un archivo de texto.
---
### Búsqueda y Filtrado (`find` y `grep`)
* `which [comando]` : Muestra la ruta absoluta del binario de una aplicación.
* `find /etc -type f -name apache2` : Busca un archivo específico dentro de una ruta.
* `ls -l | grep .png` : Filtra la salida del comando anterior para mostrar solo imágenes PNG.
* `grep -r "password" /etc/` : Busca de forma recursiva la palabra "password" dentro de todos los archivos del directorio `/etc/`.
---
### Gestión de Procesos y Usuarios
* `whoami` : Muestra el usuario con el que estás conectado actualmente.
* `w` : Muestra los usuarios conectados, el tiempo de conexión y qué comandos están ejecutando.
* `ps -aux` : Lista absolutamente todos los procesos en ejecución del sistema con sus respectivos usuarios.
* `ps -u [usuario]` : Lista los procesos que está ejecutando un usuario específico.
* `kill -9 [PID]` / `pkill [nombre_proceso]` : Fuerza el cierre de un proceso por su número de identificador (PID) o por su nombre.
---
### Redes (Networking Básica)
* `ifconfig` : Muestra la información de las interfaces de red activas.
* `ping [IP/Dominio]` : Comprueba la conectividad de red con un host remoto.
* `netstat -a` : Muestra los servicios, puertos abiertos y conexiones de red activas.
* `nslookup [IP/Dominio]` : Realiza consultas al servidor DNS para resolver nombres de dominio a direcciones IP.
---
## 🕵️‍♂️ 3. Hacking y Reconocimiento (Herramientas)
### Nmap: Manual de Supervivencia
```bash
nmap [IP]             # Escaneo rápido de los 1000 puertos comunes.
nmap -Pn [IP]         # Salta el bloqueo de Ping (Host Discovery).
nmap -sV [IP]         # Detección exacta de versiones de servicios.
sudo nmap -sS [IP]    # Escaneo sigiloso (Stealth SYN Scan).
sudo nmap -A [IP]     # Escaneo agresivo (SO, versiones, traceroute, scripts).
```
---
### Kit de Herramientas de Análisis (Estilo Bandit / Reversing)
* `file [archivo]` : Identifica el tipo real de un archivo (binario, texto plano, comprimido).
* `strings [archivo]` : Extrae todas las cadenas de texto legibles ocultas dentro de un archivo binario.
* `gunzip [archivo.gz]` / `tar -xf [archivo.tar]` : Descompresión rápida de archivos.
* `base64 -d` : Decodifica cadenas de texto cifradas en Base64.
* `xxd -r` : Revierte texto en formato hexadecimal a un archivo binario real.
---
## 🎯 4. Writeups e Informes de Intrusión (CTFs)
### 🏴‍☠️ Máquina: Psycho (DockerLabs) | Dificultad: Media
1. **Reconocimiento y LFI:** Explotación de una vulnerabilidad de *Local File Inclusion* en el parámetro `index.php?secret=` para leer `/etc/passwd` y extraer la clave privada SSH (`id_rsa`) del usuario `vaxei`.
2. **Acceso Inicial:** Conexión vía SSH al sistema tras aplicar los permisos correctos a la clave (`chmod 600 id_rsa_vaxei`).
3. **Movimiento Lateral (Sudo Abuse):** Mediante `sudo -l` descubrimos que el usuario `vaxei` podía ejecutar `/usr/bin/perl` como el usuario `luisillo` sin contraseña. Ejecutamos `sudo -u luisillo /usr/bin/perl -e 'exec "/bin/bash"'` para pivotar de usuario.
4. **Escalada a Root (Library Hijacking):** Identificamos un script ejecutado por root (`paw.py`) en `/opt` (directorio con permisos de escritura nuestros) que importaba la librería `os`. Creamos un falso módulo malicioso `subprocess.py` en `/opt` que fue importado por el script principal, logrando una shell interactiva como `root`.
---
### 🏴‍☠️ Máquina: Cap (Hack The Box)
1. **Explotación Web (IDOR):** Identificación de una vulnerabilidad de *Insecure Direct Object Reference* en el panel de control. Cambiando la URL de `/data/1` a `/data/0`, accedimos a un archivo histórico de captura de tráfico de red (`.pcap`).
2. **Análisis de Tráfico:** Descarga del archivo `0.pcap`, análisis con Wireshark filtrando por el protocolo `ftp`, lo que reveló credenciales en texto plano (`nathan : Buck3tH4TF0RM3!`).
3. **Escalada (Linux Capabilities):** Tras acceder por SSH, realizamos una enumeración con `getcap -r / 2>/dev/null` y descubrimos que el binario `/usr/bin/python3.8` tenía asignada la capacidad `cap_setuid+ep`. Esto permitió ejecutar un comando de una línea para cambiar el ID de usuario a 0 y obtener root de forma inmediata.
---
## 🧩 Resolución de Protocolos Básicos (Starting Point)
* **Meow (Telnet - Puerto 23):** Acceso directo al servicio de red con el usuario `root` sin contraseña.
* **Fawn (FTP - Puerto 21):** Conexión exitosa al servidor de ficheros utilizando el usuario anónimo (`anonymous`).
* **Redeemer (Redis - Puerto 6379):** Conexión a la base de datos con `redis-cli`, listado de claves mediante `keys *` y lectura del valor con `get flag`.
* **Dancing (SMB - Puerto 445):** Enumeración de recursos compartidos de red con `smbclient -L` y acceso a la carpeta `WorkShares` para descargar la bandera.
---
## 💉 Cheat Sheet: SQL Injection Básica
* **Fase de descubrimiento de directorios ocultos:**
  ```bash
  gobuster dir -u http://[IP] -w /usr/share/wordlists/dirb/common.txt
  ```
* **Bypass de Autenticación (Inyección en campos de usuario):**
  * `admin' or 1=1-- -` (Estándar SQL)
  * `admin' #` (Específico para MySQL)
