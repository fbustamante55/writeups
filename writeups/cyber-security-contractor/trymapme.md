# Contrato 3 — TryMapMe (Nmap)

- **Serie:** Cyber Security Contractor (Cyber Security 101)
- **Fecha:** 2026-10-07
- **Dominio:** Escaneo/mapeo de red con Nmap
- **Acceso:** Trabajas DESDE una workstation en la red. Todo local, sin máquina externa.
- **Red objetivo:** 192.168.8.0/24

## Enfoque
Empezar siempre por descubrimiento de hosts; esa salida alimenta varias preguntas.

## Preguntas y comandos

### Q1 — Hosts activos en el /24
```bash
nmap -sn 192.168.8.0/24
```
Línea final: `X hosts up` → **[REDACTADO]**

Mapa que quedó:
| IP | Puerto/servicio |
|---|---|
| .10 | 53 domain (DNS) |
| .20 | 21 ftp |
| .50 | (nada en top-1000; redis/memcache en puertos altos) |
| .60 | (8080 SimpleHTTPServer) |
| .91 | (UDP) |
| .90 | DHCP |

### Q2 — Protocolo en .10
`53/tcp open domain` → **[REDACTADO]**

### Q3 — Servicio+versión en el host con 8080
```bash
nmap -p 8080 -sV 192.168.8.0/24 --open
```
Host .60 → `<versión del servicio>` → **[REDACTADO]**

### Q4 — OS details de .60
```bash
sudo nmap -O -p 8080 192.168.8.60
```
(darle un puerto abierto mejora el fingerprint)
Campo `OS details:` → **[REDACTADO]**

### Q5 — Puertos UDP abiertos en .91
```bash
sudo nmap -sU --min-rate 5000 192.168.8.91
```
Solo contar `open` (NO `open|filtered`). Solo 123/udp ntp → **[REDACTADO]**

### Q6 — Puertos TCP abiertos en .50
```bash
sudo nmap -p- --min-rate 5000 192.168.8.50
```
199 smux, 6379 redis, 11211 memcache → **[REDACTADO]**

### Q7 — MAC de .20
```bash
sudo nmap -sn 192.168.8.20
```
Línea `MAC Address:` → **[REDACTADO]**

### Q8 — TCP well-known (0-1023) abiertos en .60
```bash
sudo nmap -p 0-1023 192.168.8.60
```
Solo 22 ssh (el 8080 NO cuenta, es >1023) → **[REDACTADO]**

### Q9 — IP del DHCP
```bash
sudo nmap -sU -p 67 192.168.8.0/24 --open
```
`67/udp open dhcps` en → **[REDACTADO]**

### Q10 — Servicio en puerto 161 de .50
```bash
sudo nmap -sU -p 161 -sV 192.168.8.50
```
161/udp es UDP (no salía en el -p- TCP) → **[REDACTADO]**

## Respuestas
_Retiradas a propósito: no se publican las respuestas del capstone. La metodología está arriba._

## Aprendizajes
- `-sn` = ping sweep (host discovery). `-p-` = 65535 puertos. `-sU` = UDP.
- **Well-known ports = 0-1023**. Puertos altos (8080) no cuentan.
- UDP: contar solo `open`, no `open|filtered`.
- MAC solo visible con privilegios en red local (capa 2 / ARP).
- DHCP = UDP 67, DNS = 53, SNMP = UDP 161, NTP = UDP 123.
- `--min-rate` acelera escaneos (sobre todo UDP).
