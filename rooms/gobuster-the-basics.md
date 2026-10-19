# Gobuster: The Basics

- **Link:** THM — Gobuster: The Basics — módulo Offensive Security Tooling
- **Fecha:** 2026-10-06
- **Dificultad:** Easy (premium)

## Conceptos clave
- **Gobuster:** herramienta de enumeración por fuerza bruta (directorios, archivos, subdominios, vhosts) usando wordlists.
- 3 modos: `dir`, `dns`, `vhost`.
- NO es recursivo: si encuentra un directorio interesante, hay que relanzar apuntando a él.

### Flags útiles (modo dir)
- `-u` URL, `-w` wordlist (ambos obligatorios).
- `-x` extensiones (`.php,.js`), `-k` skip TLS (`--no-tls-validation`), `-r` seguir redirects, `-s`/`-b` filtrar status codes, `-U`/`-P` auth.

## Setup importante — /etc/hosts
Los rooms usan hostnames ficticios (ej. `www.offensivetools.thm`) que no existen en DNS real. Hay que mapearlos a la IP del lab:
```
echo "10.66.156.170 www.offensivetools.thm offensivetools.thm" >> /etc/hosts
```
- ⚠️ Escribir el hostname LIMPIO. Al copiar desde THM se pega en Markdown `[texto](url)` y rompe la resolución (`no such host`). Editar a mano o con `sed -i '/offensivetools/d' /etc/hosts` para borrar la línea mala.
- `dns` mode NO depende de /etc/hosts (consulta DNS directo); `dir` y `vhost` por IP tampoco, pero `dir` por hostname SÍ.

## Modo dir — directorios y archivos
```
gobuster dir -u "http://www.offensivetools.thm" -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```
- Task 4: directorio interesante = **[REDACTADO]**. Dentro (relanzando con `-x .js`) había un `.js` con la flag.
- Enumerar dentro: `gobuster dir -u ".../<dir>" -w <wl> -x .js`.
- Respuesta del directorio: SIN la barra (`dir`, no `/dir`).

## Modo dns — subdominios
```
gobuster dns -d offensivetools.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
```
- `-d` dominio, `-w` wordlist (obligatorios). Flag requerido aparte de `-w`: **`-d`**.
- Contar las líneas `Found:`.

## Modo vhost — virtual hosts
- Vhosts = varios sitios en la MISMA IP (basado en el header `Host:`). Subdominios = en DNS. Diferencia: vhost cambia el header Host; dns hace lookup DNS.
```
gobuster vhost -u "http://10.66.156.170" --domain offensivetools.thm -w <wordlist> --append-domain --exclude-length 250-320
```
- `--append-domain` evita falsos positivos (pega el dominio a cada palabra).
- `--exclude-length` filtra respuestas basura por tamaño.
- Contar solo los `Found:` con `Status: 200` (ignorar 404).

## Lo que me costó / errores
- `no such host`: el hostname quedó mal en /etc/hosts por pegar el link con formato Markdown (corchetes/paréntesis). Reescribir a mano.
- Recordar que la IP del lab cambia al re-desplegar → actualizar /etc/hosts.

## Resumen para documentar
Gobuster enumera por fuerza bruta en 3 modos: dir (directorios/archivos, `-x` para extensiones, no recursivo), dns (subdominios vía lookup), vhost (virtual hosts por header Host, con --append-domain y --exclude-length). Para hostnames .thm hay que editar /etc/hosts primero.
