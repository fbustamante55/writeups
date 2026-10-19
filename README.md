# TryHackMe — Notas y Writeups

Notas de aprendizaje y writeups de rooms/retos de [TryHackMe](https://tryhackme.com), enfocados en el path **Cyber Security 101** y prácticas relacionadas.

## Estructura

- **`PROGRESO.md`** — índice/bitácora de rooms completados.
- **`CHEATSHEET.md`** — chuleta de comandos para CTFs (recon, cracking, shells, privesc).
- **`rooms/`** — notas de conceptos por room (herramientas, comandos, aprendizajes).
- **`writeups/`** — writeups completos de retos resueltos:
  - `cyber-security-contractor/` — capstone guiado (10 preguntas por contrato).
  - `ctf/` — máquinas/retos tipo CTF (recon → foothold → privesc → flags).

## Contenido destacado

### Cyber Security Contractor (capstone)
Serie de 7 contratos cronometrados, cada uno de un dominio distinto:

| # | Contrato | Dominio |
|---|----------|---------|
| 1 | Arian Retail Group | Auditoría de config Windows |
| 2 | NovaByte Analytics | Auditoría de config Linux |
| 3 | TryMapMe | Escaneo de red con Nmap |
| 4 | AstraVault | Web app pentest (IDOR, XSS, SQLi, JWT) |
| 5 | SkyLedger Cloud | Blue team / logs e IDS (Snort, pcap) |
| 6 | SentinelWorks | Explotación encadenada (web → SSH → root) |
| 7 | Ledger Solutions | Análisis estático de malware |

Ver [`writeups/cyber-security-contractor/`](writeups/cyber-security-contractor/).

### Rooms con notas
John the Ripper · Hydra · Gobuster · Blue (EternalBlue) · Web Application Basics · Moniker Link (CVE-2024-21413).

### Pendientes de documentar
Shells Overview · Bug Bounty Banco Plata · SOC Simulator (sesiones hechas, writeup pendiente).

## Aviso
Material educativo para estudio personal. Las flags, IPs y hashes son específicos de cada despliegue de VM y cambian al re-desplegar; lo valioso es la **metodología y los comandos**. Uso exclusivo en entornos autorizados (labs de TryHackMe).
