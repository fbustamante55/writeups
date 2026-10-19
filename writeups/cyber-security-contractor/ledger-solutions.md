# Contrato 7 — Ledger Solutions (Análisis estático de malware)

- **Serie:** Cyber Security Contractor (Cyber Security 101)
- **Fecha:** 2026-10-07
- **Dominio:** Análisis estático de un binario malicioso (DFIR)
- **Acceso:** VM Windows. Archivo: `C:\Users\DFIRUser\Desktop\updater.exe`
- **Herramientas:** PowerShell, strings.exe (Sysinternals), DFIR Tools en el escritorio.

## Enfoque
Análisis estático = NO ejecutar. Solo inspeccionar: tipo/arch, strings, decodificar IOCs, clasificar.

## Preguntas y comandos

### Q1 — SO destino
Es un PE (empieza con `MZ`) → **[REDACTADO]**

### Q2 — Arquitectura
```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\Users\DFIRUser\Desktop\updater.exe")
$peOffset = [BitConverter]::ToInt32($bytes, 0x3C)
$machine = [BitConverter]::ToUInt16($bytes, $peOffset + 4)
"{0:X4}" -f $machine
```
`8664` = AMD64 → **[REDACTADO]** (`014C` sería x86)

### Q3 — URL en strings
```powershell
strings.exe C:\Users\DFIRUser\Desktop\updater.exe | Select-String -Pattern "http"
```
→ **[REDACTADO]**
(typosquat: tryhatme ≠ tryhackme)

### Q4 — Táctica ATT&CK (File and Directory Discovery)
Strings con `GetFileAttributesA` + `GetCurrentDirectoryA` → **[REDACTADO]** (T1083)

### Q5 — String codificado
```powershell
strings.exe C:\Users\DFIRUser\Desktop\updater.exe | Select-String -Pattern "^[A-Za-z0-9+/]{16,}={0,2}$"
```
→ **[REDACTADO]**

### Q6 — URL decodificada
```powershell
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("dHJ5aGF0bWUuY29tL2NzMTAxLzIvdGVzdDIuZXhl"))
```
→ **[REDACTADO]**

### Q7 — SHA-256 del archivo
```powershell
(Get-FileHash C:\Users\DFIRUser\Desktop\updater.exe -Algorithm SHA256).Hash.ToLower()
```
→ **[REDACTADO]**

### Q8 — Dominio malicioso
→ **[REDACTADO]**

### Q9 — Primeros 2 bytes
```powershell
Format-Hex C:\Users\DFIRUser\Desktop\updater.exe | Select-Object -First 1
```
→ **[REDACTADO]** (= MZ, magic number de todo PE)

### Q10 — Capacidad de crear procesos
```powershell
strings.exe C:\Users\DFIRUser\Desktop\updater.exe | Select-String -Pattern "CreateProcess|WinExec|ShellExecute"
```
→ **[REDACTADO]** (también ShellExecuteExA)

## Respuestas
_Retiradas a propósito: no se publican las respuestas del capstone. La metodología está arriba._

## Clasificación del malware
**Downloader/Trojan**: usa `URLDownloadToFileA` para bajar `test2.exe` desde el dominio typosquat,
y `CreateProcessA` para ejecutarlo. Encaja con el escenario del software pirata que trajo el componente.

## Aprendizajes / IOCs clave
- **Magic numbers**: `MZ` (4D 5A) = PE Windows. ELF = `7F 45 4C 46`.
- Arquitectura por campo Machine del PE header: `014C`=x86, `8664`=x64.
- `strings` + grep de http/base64/APIs revela IOCs.
- `aHR0`/`dHR0` = inicio de "http" en base64.
- APIs reveladoras: `URLDownloadToFileA` (descarga), `CreateProcessA` (ejecuta), `GetFileAttributesA`/`GetCurrentDirectoryA` (discovery).
- SHA-256 del binario = IOC principal para VirusTotal.
