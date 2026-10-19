# Contrato 5 — SkyLedger Cloud (Blue Team / Logs e IDS)

- **Serie:** Cyber Security Contractor (Cyber Security 101)
- **Fecha:** 2026-10-06
- **Dominio:** Review de perímetro — firewall, Snort, pcap, vuln scan, IR playbook
- **Acceso:** VM con artefactos en `~/Desktop/SkyLedger_Artefacts/`

## Artefactos
- `SkyLedger_firewall.log` — logs UFW BLOCK (Q3, Q4, Q5)
- `SkyLedger_perimeter_traffic_clean.pcap` — captura (Q7)
- `SkyLedger_VulnScan.txt` — reporte vuln VPN (Q8, Q9)
- `SkyLedger_IR_Playbook.txt` — playbook de respuesta (Q10)
- Snort en `/etc/snort/rules/local.rules` (Q6)
- UFW de la VM = config espejada (Q1, Q2)

## Preguntas y comandos

### Q1 — Puerto BD expuesto desde Anywhere
```bash
sudo ufw status numbered
```
Esperados: 22, 80, 1194/udp. El que sobra y es BD → **[REDACTADO]** (MySQL)

### Q2 — Estado correcto de ese puerto
Una BD no debe estar ALLOW desde Anywhere → **[REDACTADO]**

### Q3 — IP del atacante
Una IP destaca por escaneo sistemático (IDs secuenciales, barriendo 3306/22/21/8080/8443):
→ **[REDACTADO]**

### Q4 — Intentos bloqueados a FTP (21) del atacante
```bash
grep "39.49.232.142" SkyLedger_firewall.log | grep "DPT=21 " | wc -l
```
→ **[REDACTADO]**

### Q5 — dport más frecuente
```bash
grep -oE "DPT=[0-9]+" SkyLedger_firewall.log | sort | uniq -c | sort -rn | head
```
22 (SSH) = 8 veces → **[REDACTADO]**

### Q6 — msg de la regla Snort HTTP dir scan
```bash
cat /etc/snort/rules/local.rules
```
```
alert tcp any any -> any 80 (msg:"SkyLedger HTTP directory scan detected"; content:"/admin"; http_uri; sid:1000023; rev:1;)
```
→ **[REDACTADO]**

### Q7 — Probes FTP detectados (regla custom + pcap)
No había regla FTP; se crea:
```bash
echo 'alert tcp any any -> any 21 (msg:"SkyLedger FTP probe detected"; flags:S; sid:1000030; rev:1;)' | sudo tee -a /etc/snort/rules/local.rules
cd ~/Desktop/SkyLedger_Artefacts
sudo snort -c /etc/snort/snort.conf -r SkyLedger_perimeter_traffic_clean.pcap -A console -q | grep "FTP probe" | grep "39.49.232.142" | wc -l
```
→ **[REDACTADO]**

### Q8 — Año de la vuln VPN más severa
```bash
grep -iE "critical|high|cvss|cve" SkyLedger_VulnScan.txt
```
Más severa: `[1] High - OpenVPN Deferred Authentication Bypass / CVE-2020-15078 / CVSS 7.5`
→ **[REDACTADO]** (del CVE-**[REDACTADO]**-15078)

### Q9 — Producto VPN afectado
→ **[REDACTADO]** (encaja con 1194/udp abierto)

### Q10 — Primera acción IR para "VPN brute-force from internet"
```bash
grep -iA10 "VPN brute-force" SkyLedger_IR_Playbook.txt
```
Paso 1 → **[REDACTADO]**

## Respuestas
_Retiradas a propósito: no se publican las respuestas del capstone. La metodología está arriba._

## Aprendizajes
- Leer logs UFW: `SRC=`, `DST=`, `DPT=` (dest port), `PROTO=`. Identificar atacante por patrón (IDs secuenciales, barrido de puertos) vs ruido de internet.
- `grep -oE "DPT=[0-9]+" | sort | uniq -c | sort -rn` para frecuencias.
- Reglas Snort: `alert <proto> <src> <sport> -> <dst> <dport> (msg:"..."; flags:S; sid:...;)`. `flags:S` = SYN.
- Correr Snort sobre pcap: `snort -c snort.conf -r file.pcap -A console -q`.
- En vuln scans, la severidad la da el CVSS; el año sale del ID del CVE.
- IR playbook: primera acción para brute-force = contener (lock de cuenta).
