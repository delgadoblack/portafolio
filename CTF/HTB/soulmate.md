# Metodologías de ataque

Ingresamos a la ip que nos entrega HTB, en mi caso es la 10.10.11.86



## Escaneo previo
**Full TCP (nmap):**
`nmap -sS -p- -T4 --min-rate 1000 -n -Pn 10.10.11.86 -oA scans/all-ports-$IP`

## Escaneo de servicios
**Servicios + scripts:**
`nmap -sC -sV -p <puertos> -n 10.10.11.86 -oA scans/targeted-$IP`

## Fuzzing de directorios (web)
`ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://$IP/FUZZ -o ffuf/dirs-$IP.json -of json -t 5`

## Fuzzing de subdominios / vhosts
`ffuf -w /usr/share/wordlists/dns/subdomains-top1million-20000.txt -u http://$IP/ -H "Host: FUZZ.$DOMAIN" -o ffuf/vhosts-$DOMAIN.json -of json -t 5`

> **Recordatorio:** documentar comandos, salidas y autorización es obligatorio. Uso exclusivo en entornos autorizados.
