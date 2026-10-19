# <Nombre del CTF / máquina>

- **Plataforma / room:** TryHackMe —
- **Link:**
- **Dificultad:**
- **Fecha:**
- **IP objetivo:** `10.x.x.x`  (cambia en cada despliegue)

## Resumen (TL;DR)
> 2–3 líneas: qué era, por dónde entró, cómo escaló. Lo reutilizable es la **metodología**, no las flags.

## 1. Recon
```bash
# host vivo + puertos
nmap -sC -sV -p- -T4 <IP> -oN nmap.txt
```
- Puertos abiertos:
- Servicios / versiones:

## 2. Enumeración
```bash
# web
gobuster dir -u http://<IP>/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,txt -t 50
# vhost / dns si aplica
```
- Hallazgos:

## 3. Foothold (acceso inicial)
- Vulnerabilidad:
- Explotación / comandos:
- Shell obtenida como:

## 4. Escalada de privilegios
```bash
# enum local
sudo -l
find / -perm -4000 -type f 2>/dev/null   # SUID
```
- Vector:
- Root conseguido con:

## 5. Flags
| Flag | Ubicación | Valor |
|------|-----------|-------|
| user | `/home/.../user.txt` | `THM{...}` |
| root | `/root/root.txt` | `THM{...}` |

## 6. Aprendizajes / IOCs / trucos
- Qué técnica nueva practiqué:
- Qué me atascó y cómo salí:
- Comando que me quiero quedar:
