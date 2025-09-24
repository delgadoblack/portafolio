# Metodologías de ataque

## Escaneo previo
**Full TCP (nmap):**
`nmap -sS -p- -T4 --min-rate 1000 -n -Pn $IP -oA scans/all-ports-$IP`

## Escaneo de servicios
**Servicios + scripts:**
`nmap -sC -sV -p <puertos> -T4 -n $IP -oA scans/targeted-$IP`

## Fuzzing de directorios (web)
`ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://$IP/FUZZ -o ffuf/dirs-$IP.json -of json -t 40 -mc 200,301,302,403`

## Fuzzing de subdominios / vhosts
`ffuf -w /usr/share/wordlists/dns/subdomains-top1million-20000.txt -u http://$IP/ -H "Host: FUZZ.$DOMAIN" -o ffuf/vhosts-$DOMAIN.json -of json -t 40 -mc 200,301,302`

> **Recordatorio:** documentar comandos, salidas y autorización es obligatorio. Uso exclusivo en entornos autorizados.
