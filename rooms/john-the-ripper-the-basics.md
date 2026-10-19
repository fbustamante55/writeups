# John the Ripper — The Basics

- **Link:** THM — John the Ripper: The Basics
- **Fecha:** 2026-10-05
- **Dificultad:** Easy

## Conceptos clave
- **Wordlist mode:** prueba palabras de un diccionario externo (ej. rockyou.txt).
- **Single crack mode (`--single`):** John genera su propio diccionario a partir del **username**, aplicando **word mangling** (mutaciones: mayúsculas, números, símbolos, leetspeak).
- **GECOS:** 5º campo de `/etc/passwd` (nombre completo, etc.). John lo usa como fuente extra de palabras en single mode.

## Task 07 — Single Crack Mode
- **Qué pide:** crackear un hash asumiendo que el usuario se llama "Joker".
- **Formato del hash:** 32 caracteres hex → MD5 → `--format=raw-md5`.
  - Nota: `wc -m` daba 33 por el newline (`\n`) final. `cat -A` muestra `...aada$` (el `$` = fin de línea). Hex reales = 32.
- **Clave del single mode:** el archivo debe tener formato `username:hash`, o John no genera mutaciones (daba 0 candidatos con el hash pelado).
  - Lo edité con `nano` para que quedara `joker:7bf6...aada`.
- **Comando que funcionó:**
  ```bash
  john --single --format=raw-md5 hash07.txt
  ```
- **Resultado:** `[REDACTADO] (joker)` — mutación leetspeak de "joker". La wordlist rockyou NO la encontró (0g); single sí (1g). Esa es la moraleja de la task.

## Task 08 — Custom Rules
- **Qué son:** reglas propias en `john.conf` para mutar palabras de un wordlist dinámicamente. Explotan la **password complexity predictability** (los usuarios ponen mayús/número/símbolo siempre en el mismo sitio, ej. `Polopassword1!`).
- **Dónde se definen:** `/opt/john/john.conf` (AttackBox) o `/etc/john/john.conf` (instalado por paquete/make). Jumbo John trae muchas reglas de ejemplo ~línea 678.
- **Sintaxis:** `[List.Rules:NOMBRE]` + línea de regla `modificadores"[sets]"`.
  - Modificadores (FUERA de comillas): `Az`=append al final, `A0`=prepend al inicio, `c`=capitaliza posicional.
  - Sets (DENTRO de comillas): `[0-9]`, `[A-Z]`=solo mayús, `[a-z]`=solo minús, `[A-z]`=ambas, `[!£$%@]`=esos símbolos.
  - Ejemplo del room: `cAz"[0-9] [!£$%@]"` → `Polopassword1!`
- **Llamar la regla:** `--rule=NOMBRE`. Comando completo: `john --wordlist=[wl] --rule=NOMBRE [archivo]`
- **Respuestas task (construidas):**
  - Explotamos: *password complexity predictability*.
  - Regla "todas las mayús al final": `Az"[A-Z]"`.
  - Flag para regla `THMRules`: `--rule=THMRules`.

## Task 09 — Cracking Zip files (zip2john)
- **Patrón `*2john`:** John no lee un `.zip` directo; `zip2john` extrae el hash a un archivo y luego John lo crackea normal. Mismo patrón que `unshadow`.
- **Paso 1 — extraer hash:** `zip2john secure.zip > zip_hash.txt` (`>` redirige la salida al archivo).
- **Paso 2 — crackear:** `john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt`
- **Paso 3 — abrir el zip con la pass y leer la flag:** `unzip secure.zip` (pide la password) → `cat <archivo>`.
- **Resultado:** password = `[REDACTADO]`; flag = `[REDACTADO]`.
- Nota: el patrón `*2john` se repite con RAR, claves SSH, PDFs, etc.

## Task 10 — RAR files (rar2john)
- Mismo patrón que zip. Extraer: `rar2john secure.rar > rar_hash.txt` (o `/opt/john/rar2john`).
- Crackear: `john --wordlist=/usr/share/wordlists/rockyou.txt rar_hash.txt`
- Abrir RAR (no sirve unzip): `unrar x secure.rar` (o `7z x secure.rar`) → `cat <archivo>`.

## Task 11 — SSH private key (ssh2john)
- Crackea la **passphrase** de una clave privada `id_rsa` (común en CTFs: si consigues un id_rsa, crackear la passphrase permite login SSH).
- `ssh2john` suele NO estar en PATH → es script Python:
  - AttackBox: `python3 /opt/john/ssh2john.py id_rsa > ssh.txt`
  - Kali: `python /usr/share/john/ssh2john.py id_rsa > ssh.txt`
- Crackear: `john --wordlist=/usr/share/wordlists/rockyou.txt ssh.txt`
- John detecta el formato solo (`SSH private key [RSA/DSA/EC/OPENSSH]`) → no hace falta `--format`.
- **Resultado:** passphrase = `[REDACTADO]`. Aquí la respuesta ES la passphrase (no hay flag dentro).
- Errores que tuve: `ssh2john: command not found` (usar el .py) y `--wordlists` con "s" (es `--wordlist` singular).

## Herramientas / comandos importantes
- Verificar longitud real del hash: `tr -d '\n ' < archivo | wc -c`
- Ver caracteres ocultos: `cat -A archivo` / `xxd archivo`
- Ver crackeados ya resueltos: `john --show --format=raw-md5 archivo`
- Tabla longitud→hash: 32=MD5, 40=SHA1, 64=SHA256, 128=SHA512.

## Lo que me costó / errores
- Confusión 32 vs 33 chars (era el newline).
- Primero intenté wordlist mode (0g); la task era de single mode.
- Rutas: fallaba con `/John-...` (faltaba `~`) y con `hash07` sin `.txt`.

## Resumen para documentar
Single crack mode crackea contraseñas débiles basadas en el username. Requiere el hash en formato `user:hash`. Útil cuando la contraseña es una variación del nombre y no está en diccionarios comunes.
