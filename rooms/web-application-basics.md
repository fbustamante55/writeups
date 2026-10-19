# Web Application Basics

- **Link:** THM — Web Application Basics — módulo Web Hacking
- **Fecha:** 2026-10-06
- **Dificultad:** Info / Easy

## Conceptos clave

### Componentes de una web app
- **Front End** (lo que ves): HTML (estructura), CSS (estilo), JavaScript (lógica/decisiones).
- **Back End** (lo que no ves): Web Server (aloja y entrega el contenido), Database, Infraestructura, **WAF** (Web Application Firewall, filtra tráfico malicioso).
- Herramienta para acceder: **web browser**.

### Anatomía de una URL
```
https://user:pass@host.com:443/path?query=val#fragment
scheme   user     host   port path  query       fragment
```
- **Scheme:** http/https (HTTPS = cifrado).
- **Host/Domain:** qué sitio. Cuidado con **typosquatting** (dominios mal escritos para phishing).
- **Port:** 80 http, 443 https.
- **Path:** recurso/endpoint después del dominio.
- **Query string:** empieza con `?`, pasa datos (búsquedas, forms). Riesgo de injection.
- **Fragment:** empieza con `#`, salta a una sección.

### Mensajes HTTP (request y response)
Estructura: **Start Line → Headers → línea vacía → Body**.
- Request: la manda el cliente. Response: la manda el servidor.

### HTTP Request
- Request line: `METHOD /path HTTP/version`.
- **Métodos:** GET (leer), POST (crear/actualizar), PUT (reemplazar), DELETE (borrar), PATCH (actualizar parte), HEAD (solo headers), OPTIONS (qué métodos soporta), TRACE (debug), CONNECT (túnel seguro).
- Versiones: 0.9, 1.0, **1.1** (persistent connections + chunked encoding, la más usada), 2 (multiplexing), 3 (QUIC).
- Request headers: **Host** (dominio destino), User-Agent (navegador), Referer (de dónde vienes), Cookie, Content-Type.
- Body formats: URL Encoded (`application/x-www-form-urlencoded`, `k=v&k2=v2`), Form Data (`multipart/form-data`, archivos), JSON, XML.

### HTTP Response
- Status line: `HTTP/version código reason-phrase` (ej. `HTTP/1.1 404 Not Found`).
- Categorías: 1xx info, 2xx success, 3xx redirect, 4xx client error, 5xx server error.
- Comunes: 200 OK, 301 Moved Permanently, 404 Not Found, 500 Internal Server Error.
- Response headers: Date, Content-Type, **Server** (revela software → se suele ocultar), Set-Cookie, Cache-Control, Location (redirect).
- Flags de cookies: **Secure** (solo HTTPS), **HttpOnly** (no accesible desde JS, protege de XSS).

### Security Headers
- **Content-Security-Policy (CSP):** mitiga XSS. Props: `default-src`, `script-src` (de dónde cargar scripts), `style-src`. `'self'` = mismo dominio.
- **Strict-Transport-Security (HSTS):** fuerza HTTPS. Directivas: `max-age`, **`includeSubDomains`**, `preload`.
- **X-Content-Type-Options: nosniff** — evita MIME sniffing.
- **Referrer-Policy:** controla qué referrer se envía (no-referrer, same-origin, strict-origin...).

## Task 10 — Práctica (emulador de requests)
- Hacer GET / POST / DELETE en el emulador. Respuestas (flags) salen en la respuesta del server.
- GET `/api/users`, POST `/api/user/2` (cambiar country Bob UK→US, body `country=US`), DELETE `/api/user/1`.
- Nota: el emulador es front-end (sitio estático), se opera con sus botones, no necesariamente con curl externo.

## Resumen para documentar
Fundamentos web: componentes (front/back + WAF), anatomía de URL, estructura de mensajes HTTP (start line, headers, body), métodos, status codes por categoría, y security headers (CSP, HSTS, nosniff, cookie flags Secure/HttpOnly). Base para todo pentesting web.
