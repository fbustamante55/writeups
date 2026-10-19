# Progreso TryHackMe

| Fecha | Room | Estado | Notas |
|-------|------|--------|-------|
| 2026-10-05 | John the Ripper — The Basics | ✅ Completado | Single crack, custom rules, zip2john / rar2john / ssh2john (patrón *2john) |
| 2026-10-05 | Moniker Link (CVE-2024-21413) | En progreso (Tasks 1–3 ✅) | Outlook `file://`+`!` bypass → Responder captura netNTLMv2. Hecho desde Kali/WSL con VPN en tun0 |
| 2026-10-05 | Blue (EternalBlue MS17-010) | ✅ Completado | eternalblue → meterpreter → SYSTEM → migrate → hashdump → John (contraseñas débiles crackeadas) → 3 flags |
| 2026-10-05 | Hydra | ✅ Completado | hydra http-post-form + ssh (molly), 2 flags |
| 2026-10-06 | Web Application Basics | ✅ Completado | HTTP, URL, métodos, status codes, security headers, práctica GET/POST/DELETE |
| 2026-10-06 | Gobuster: The Basics | ✅ Completado | dir / dns / vhost modes; `/etc/hosts` para hostnames .thm |
| 2026-10-06 | Shells Overview | ✅ Completado (📝 writeup pendiente) | reverse vs bind shell, listeners, payloads, web shell. Práctica: cmd injection + unrestricted upload → 2 flags |
| 2026-10-06 | Bug Bounty — Banco Plata (HackerOne) | ✅ Sesión completa (📝 writeup pendiente) | Recon real: subfinder, httpx, whatweb, nmap. Burp con header X-HackerOne-Research. Endpoints OTP analizados: /otp/send sin validación de número, /otp/verify sin rate limit, error enumeration (OTP_NOT_MATCH vs OTP_NOT_FOUND), Sentry DSN expuesto en empresa.bancoplata.mx, client_id=dev en Keycloak (whiteboard.platacard.mx). Reporte cerrado como Informative — requieren ATO completo como PoC. Aprendizaje: metodología BBH completa, lectura de APIs reales en Burp, respeto de scope y rate limits. |
| 2026-10-06/07 | Cyber Security Contractor (capstone) | ✅ 7/7 contratos | Serie de 7 retos cronometrados, un dominio cada uno. Writeups completos en `writeups/cyber-security-contractor/`. |
| 2026-10-07/15 | SOC Simulator — The Try Daily | ✅ Sesión de triaje (📝 writeup pendiente) | Triaje de alertas en SIEM (Splunk) + TryDetectThis. Phishing con dominios lookalike (amazon.biz, m1crosoftsupport.co, hrconnex.thm). TP vs FP, lógica de escalado (allowed→impacto posible / blocked→contenido), "assume breach". Fuentes: email, firewall, webserver (ModSecurity). |

### Cyber Security Contractor — detalle
| # | Contrato | Dominio |
|---|----------|---------|
| 1 | Arian Retail Group | Config Windows (SMB, pagefile, dump, UAC, ADS, tareas, servicios) |
| 2 | NovaByte Analytics | Config Linux (handover, .credentials, su, cron, logs auth) |
| 3 | TryMapMe | Nmap (host discovery, TCP/UDP, -sV, -O, SNMP, DHCP) |
| 4 | AstraVault | Web pentest: IDOR, XSS, MFA bypass, cmd injection, SQLi, JWT alg:none |
| 5 | SkyLedger Cloud | Blue team: UFW logs, Snort rules, pcap, vuln scan, IR playbook |
| 6 | SentinelWorks | Cadena: hydra→zip2john→unshadow→SSH→MD5→KeyWatch→ssh2john→root→César |
| 7 | Ledger Solutions | Malware estático: PE header, strings, base64 IOCs, SHA-256, ATT&CK |

## Estructura
- `rooms/<nombre-room>.md` — notas de cada room (conceptos, comandos, lo que aprendí)
- `writeups/<serie>/<reto>.md` — writeups completos de retos resueltos (metodología + respuestas)
- `PROGRESO.md` — este índice
