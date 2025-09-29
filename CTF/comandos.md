# Dentro del sistema

Distribucion, kernel, arquitectura, hora, directorio actual.

```bash
uname -a            -> Info del kernel y arquitectura.
cat /etc/os-release -> Version/distribucion.
date                -> Hora del sistema.
pwd                 -> Directorio actual.
```

Identidad y permisos

```bash
whoami / id -> Usuario y grupos efectivos. 
group       -> Grupos a los que pertenece el usuario.
```

Procesos en ejecucion
```bash
ps aux / ps oef -> Listado de procesos.
top    / htop   -> Uso de CPU/memoria en tiempo real. (htop no siempre estara instalado en los servidores).
```

Red y conectividad

```bash
ip a     / if config    -> Interfaces y direcciones IP.
ss -tuln / netsat -tuln -> Puertos escuchando.
ss -s                   -> Resumen de sockets/
```

Usuarios y sesiones

```bash
cat /etc/passwd (lectura) -> Lista de cuentas locales (sin passwords).
w / who                   -> Usuarios conectados.
```

Servicios y unidades del sistema (Esto nos dira que demonios esta corriendo y como se administran)

```bash
systemctl list-units --type=service --state=running -> Servicios activos en systemd.
/etc/init.d/ o service --status-all                 -> En sistemas legacy.
```

Archivos y permisos en el FileSystem

```bash
ls -al -> Permisos y propietarios en un directorio.
df -h  -> Uso de disco.
mount  -> Puntos de montaje.
```
