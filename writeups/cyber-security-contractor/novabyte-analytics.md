# Contrato 2 — NovaByte Analytics (Config Linux)

- **Serie:** Cyber Security Contractor (Cyber Security 101)
- **Fecha:** 2026-10-06
- **Dominio:** Auditoría de configuración de servidor Linux (dev server)
- **Acceso:** Remote Access a la VM. Usuario inicial: `consultant`.

## Enfoque
"Sigue el rastro": cada pregunta da pistas/credenciales para la siguiente.
El **handover** es el mapa: `cat ~/handover_notes.txt`.

## Preguntas y comandos

### Q1 — Hostname
```bash
hostname
```
→ **[REDACTADO]**

### Q2 — Proyecto crítico (del handover)
```bash
cat ~/handover_notes.txt
```
→ **[REDACTADO]**

### Q3 — Ruta de project.conf
```bash
find /opt/novabyte -name project.conf
```
→ **[REDACTADO]**

### Q4 — Password cuenta sistema (archivo oculto en el mismo dir)
```bash
ls -la /opt/novabyte/projects/catalyst/
cat /opt/novabyte/projects/catalyst/.credentials
```
El archivo oculto `.credentials` tenía db_pass, api_key y la *System account password* de `dev.admin`.
→ **[REDACTADO]** (NO confundir con db_pass ni api_key)

### Q5 — API endpoint (cambiando de usuario)
```bash
su - dev.admin      # password: [REDACTADO]
cat /home/dev.admin/project_docs/api_config.txt
```
→ **[REDACTADO]**
(⚠️ el enunciado tenía un "eg. https://tryhackme.com/..." que es solo ejemplo, no la respuesta)

### Q6 — Permisos octal de deployment_key.pem
```bash
stat -c '%a' /home/dev.admin/deployment_key.pem
```
→ **[REDACTADO]** (correcto para clave privada)

### Q7 — Comando completo del crontab de dev.admin
```bash
crontab -l -u dev.admin
```
→ **[REDACTADO]**
(los `30 2 * * *` son el horario, no parte del "comando")

### Q8 — Estado del servicio novabyte-sync
```bash
systemctl status novabyte-sync
```
Línea `Active: inactive (dead)` → **[REDACTADO]**

### Q9 — Usuario SSH fallido el 2025-06-15
```bash
grep "Jun 15" /var/log/auth.log* | grep -i failed
# estaba en el log rotado:
grep "Failed password" /var/log/auth.log.1
```
→ **[REDACTADO]** (ojo: si dijera "invalid user X", la respuesta sería X)

### Q10 — Archivo transferido el 2025-06-14
```bash
grep "2025-06-14" /var/log/novabyte/sync.log
```
Línea con "completed" → **[REDACTADO]**

## Respuestas
_Retiradas a propósito: no se publican las respuestas del capstone. La metodología está arriba._

## Aprendizajes
- Archivos ocultos (`.`) guardan credenciales: `ls -la` siempre.
- Logs de auth suelen estar **rotados** (`.1`, `.2.gz`) — revisar todos.
- `stat -c '%a'` da el octal directo.
- Cuidado con los "eg." del enunciado: son ejemplos de formato, no respuestas.
