# 📚 Base de Conocimiento: Sistemas, Redes y Ciberseguridad

Este repositorio contiene mis apuntes personales, hojas de trucos (*cheat sheets*) y resoluciones de máquinas (*Writeups*) en plataformas como Hack The Box y DockerLabs. Documento mi aprendizaje continuo para agilizar futuras auditorías y tareas de administración de sistemas.

---

## 🛠️ 1. Troubleshooting y Administración Linux

### Solución de Integración en VMware (Portapapeles y Ratón)
Para habilitar copiar/pegar entre Windows y Kali Linux, y evitar que el cursor se quede atrapado, se requiere instalar los controladores gráficos de VMware y configurar el aislamiento.

# 1. Instalar el paquete de integración
sudo apt update && sudo apt install -y open-vm-tools-desktop

# 2. Reiniciar el servicio de integración
sudo systemctl restart open-vm-tools
Nota: Si las opciones "Guest Isolation" están en gris en VMware, apaga la máquina, entra en Settings > Options > Guest Isolation y marca Enable copy and paste.

Configuración del Idioma del Sistema (Locales)
Para corregir problemas de codificación de caracteres en la terminal:

Bash
sudo dpkg-reconfigure locales
Seleccionar con [Espacio] la opción: es_ES.UTF-8 UTF-8 y pulsar Enter.

Gestión del Archivo Hosts
Para resolver dominios locales en laboratorios CTF sin servidor DNS:

Bash
echo "10.129.244.96  facts.htb" | sudo tee -a /etc/hosts
🐧 2. Cheat Sheet: Comandos Esenciales de Linux
Navegación y Gestión de Archivos
pwd : Muestra la ruta del directorio actual.

ls -la : Lista todos los archivos (incluyendo ocultos) con detalles de permisos.

tree : Muestra la estructura de directorios en forma de árbol.

cp [archivo] / mv [archivo] [nuevo_nombre] : Copiar / Mover o renombrar archivos.

rm -r [directorio] : Eliminar un directorio y su contenido.

cat [archivo] : Muestra el contenido de un archivo de texto.

Búsqueda y Filtrado (find y grep)
which [comando] : Muestra la ubicación del binario de una aplicación.

find /etc -type f -name apache2 : Busca un archivo específico en una ruta.

ls -l | grep .png : Filtra la salida para mostrar solo imágenes PNG.

grep -r "password" /etc/ : Busca recursivamente la palabra "password" dentro de todos los archivos en /etc/.

Gestión de Procesos y Usuarios
whoami : Muestra el usuario actual.

w : Muestra los usuarios conectados y qué están haciendo.

ps -aux : Lista absolutamente todos los procesos del sistema con detalles de usuario.

ps -u [usuario] : Lista los procesos de un usuario específico.

kill -9 [PID] / pkill [nombre_proceso] : Fuerza el cierre de un proceso por su ID o por su nombre.

Redes (Networking Básica)
ifconfig : Información de las interfaces de red.

ping [IP/Dominio] : Comprueba la conectividad con un host.

netstat -a : Muestra los servicios, puertos abiertos y conexiones activas.

nslookup [IP/Dominio] : Consultas al servidor DNS para resolver IPs o dominios.

🕵️‍♂️ 3. Hacking y Reconocimiento (Herramientas)
Nmap: Manual de Supervivencia
Bash
nmap [IP]             # Escaneo rápido de los 1000 puertos comunes.
nmap -Pn [IP]         # Salta el bloqueo de Ping (Host Discovery).
nmap -sV [IP]         # Detección exacta de versiones de servicios.
sudo nmap -sS [IP]    # Escaneo sigiloso (Stealth SYN Scan).
sudo nmap -A [IP]     # Escaneo agresivo (SO, versiones, traceroute, scripts).
Kit de Herramientas de Análisis (Estilo Bandit / Reversing)
file [archivo] : Identifica el tipo real de un archivo (binario, texto, compresión).

strings [archivo] : Extrae el texto legible oculto dentro de un archivo binario.

gunzip [archivo.gz] / tar -xf [archivo.tar] : Descompresión de archivos.

base64 -d : Decodifica cadenas en Base64.

xxd -r : Revierte texto hexadecimal a un archivo binario.

🎯 4. Writeups e Informes de Intrusión (CTFs)
🏴‍☠️ Máquina: Psycho (DockerLabs) | Dificultad: Media
Reconocimiento y LFI: Explotación de Local File Inclusion en index.php?secret= para leer /etc/passwd y extraer la clave id_rsa del usuario vaxei.

Acceso Inicial: Conexión vía SSH tras aplicar chmod 600 id_rsa_vaxei.

Movimiento Lateral (Sudo Abuse): El usuario vaxei tenía permisos sudo sobre Perl. Ejecución de sudo -u luisillo /usr/bin/perl -e 'exec "/bin/bash"' para pivotar a luisillo.

Escalada a Root (Library Hijacking): Un script de root (paw.py) importaba la librería os. Al tener permisos de escritura en /opt, creé un script malicioso subprocess.py que fue importado por root al ejecutar el script, logrando una shell con privilegios máximos.

🏴‍☠️ Máquina: Cap (Hack The Box)
Explotación Web (IDOR): Acceso a capturas de red privadas cambiando la URL de /data/1 a /data/0.

Análisis PCAP: Extracción de credenciales FTP en texto plano usando Wireshark (nathan : Buck3tH4TF0RM3!).

Escalada (Linux Capabilities): Escaneo de capacidades con getcap -r / 2>/dev/null. El binario python3.8 tenía cap_setuid+ep, permitiendo ejecutar os.setuid(0) para obtener root instantáneo.

🧩 Resolución de Protocolos Básicos (Starting Point)
Meow (Telnet - Puerto 23): Acceso directo con el usuario root sin contraseña.

Fawn (FTP - Puerto 21): Conexión exitosa utilizando el usuario anonymous.

Redeemer (Redis - Puerto 6379): Conexión a la BBDD con redis-cli, enumeración con keys * y lectura con get flag.

Dancing (SMB - Puerto 445): Enumeración de recursos con smbclient -L y acceso a la carpeta WorkShares para descargar la flag.

💉 Cheat Sheet: SQL Injection Básica
Fase de descubrimiento:

Bash
gobuster dir -u http://[IP] -w [wordlist]
Bypass de Login (Usernames válidos):

admin' or 1=1-- - (Estándar)

admin' # (MySQL)
