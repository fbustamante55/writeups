# Contrato 1 — Arian Retail Group (Config Windows)

- **Serie:** Cyber Security Contractor (Cyber Security 101)
- **Fecha:** 2026-10-06
- **Dominio:** Auditoría de configuración de un servidor Windows (POS backend)
- **Acceso:** RDP a la VM. Todo se hace dentro. PowerShell como Admin.

## Enfoque
NO es pentest de red: es revisar ajustes como blue-team/sysadmin. 4 bloques:
shares · pagefile+dump · inicio+tareas · seguridad (UAC/firewall/servicios).

## Preguntas y comandos

### Q1 — Carpeta compartida
```powershell
Get-SmbShare
```
Ignorar `ADMIN$`, `C$`, `IPC$` (por defecto). La custom era **POSShare** (`C:\POS\Shared`).

### Q2 — Tamaño inicial del page file (MB)
```powershell
Get-CimInstance Win32_PageFileSetting | Select Name, InitialSize, MaximumSize
```
→ `InitialSize` = **[REDACTADO]**

### Q3 — Tipo de crash dump
```powershell
(Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl').CrashDumpEnabled
```
Valores: 0=None, 1=Complete, 2=Kernel, 3=Small(256KB), 7=Automatic.
→ `3` = **[REDACTADO]**

### Q4 — App de inicio (sin extensión)
```powershell
Get-CimInstance Win32_StartupCommand | Select Name, Command, Location
```
Ignorar `SecurityHealth` (sistema). → **[REDACTADO]**

### Q5 — Creación de regla de firewall POSInboundSync
```powershell
Get-NetFirewallRule -DisplayName POSInboundSync | Select DisplayName, CreationTime
```
→ **[REDACTADO]** (formato YYYY-MM-DD HH:MM:SS)

### Q6 — Alternate Data Stream (ADS) en C:\POS\inventory_log.txt
```powershell
Get-Item C:\POS\inventory_log.txt -Stream *
```
El stream con nombre propio (no `:$DATA`) → **[REDACTADO]**
Leer: `Get-Content C:\POS\inventory_log.txt -Stream auditinfo`

### Q7 — Nivel UAC
```powershell
Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System' | Select EnableLUA, ConsentPromptBehaviorAdmin, PromptOnSecureDesktop
```
Interpretación (EnableLUA=1):
| Nivel | ConsentPromptBehaviorAdmin | PromptOnSecureDesktop |
|---|---|---|
| Always notify | 2 | 1 |
| Notify for apps | 5 | 1 |
| Notify without dimming | 5 | 0 |
| Never notify | 0 | 0 |

→ `2` + `1` = **[REDACTADO]**

### Q8 — Tarea programada POSYearlyMaintenance (MM/DD/YYYY HH:MM)
```powershell
(Get-ScheduledTask -TaskName POSYearlyMaintenance).Triggers
```
`StartBoundary` viene ISO `<fecha ISO>` → reordenar → **[REDACTADO]**

### Q9 — Ruta ejecutable del servicio POSDataCollector
```powershell
Get-CimInstance Win32_Service -Filter "Name='POSDataCollector'" | Select Name, PathName
```
→ **[REDACTADO]**
(Revisar si es *unquoted service path* con espacios → vuln de escalada.)

### Q10 — App personalizada instalada
```powershell
Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*, HKLM:\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* | Select DisplayName, DisplayVersion, Publisher | Where DisplayName -ne $null | Sort DisplayName
```
Ignorar Microsoft/Windows/drivers. → **[REDACTADO]**

## Respuestas
_Retiradas a propósito: no se publican las respuestas del capstone. La metodología está arriba._

## Aprendizajes
- `Get-CimInstance` es el caballo de batalla para config Windows (shares, pagefile, startup, services).
- Los **ADS** (alternate data streams) esconden datos en NTFS: `Get-Item -Stream *`.
- Interpretar UAC por los valores del registro, no por la GUI.
- Crash dump types y su mapeo numérico.
