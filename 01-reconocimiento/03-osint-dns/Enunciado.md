# Laboratorio 03 · OSINT y reconocimiento DNS

## 🎯 Objetivo

Aprender a recopilar información pública sobre un dominio sin tocar ni escanear directamente ningún sistema: quién lo registró, qué servidores DNS usa, y qué subdominios existen. Esto es lo primero que hace cualquier auditor (o atacante) antes incluso de lanzar un solo paquete contra la infraestructura.

## 🧪 Entorno necesario

- Tu VM de Kali, con conexión a internet (este laboratorio no usa Metasploitable, trabaja contra dominios públicos reales).
- **Importante:** usa solo dominios que sean tuyos (por ejemplo, si tienes uno propio) o dominios pensados explícitamente para practicar. Si no tienes ninguno, usa `example.com` para los comandos básicos — es un dominio reservado para pruebas y documentación.

## 📖 Qué es OSINT y por qué se hace antes de tocar nada

**OSINT** (Open Source Intelligence) es recopilar información que ya es pública: no estás "atacando" nada, solo consultando bases de datos abiertas (registros de dominios, DNS público, buscadores...). Es legal y no deja ningún rastro en el sistema objetivo, porque ni siquiera te conectas a él directamente — consultas a terceros (registradores, servidores DNS) que ya tienen esa información publicada.

### `whois`: quién hay detrás de un dominio

```bash
whois example.com
```
- Consulta el registro público del dominio: quién lo registró (si no está oculto por privacidad), qué registrador lo gestiona, fechas de creación y expiración, y qué servidores de nombres (DNS) usa.
- Muchos dominios ocultan el dato del propietario con un servicio de privacidad, así que no siempre verás un nombre o email real — y eso también es información (te dice que usan protección de privacidad).

### `dig`: preguntar directamente al DNS

El DNS es la "agenda de contactos" de internet: traduce nombres (`example.com`) a direcciones IP, y también guarda otro tipo de información sobre el dominio.

```bash
dig example.com
```
- Por defecto, pregunta por el registro **A** (la IP asociada al dominio) y te devuelve la respuesta completa, incluyendo qué servidor DNS respondió.

```bash
dig example.com MX
```
- `MX`: pregunta específicamente por los servidores de correo del dominio (a qué servidor se entregan los emails enviados a `@example.com`).

```bash
dig example.com NS
```
- `NS`: pregunta qué servidores de nombres son responsables de ese dominio — es decir, quién gestiona su DNS.

```bash
dig example.com TXT
```
- `TXT`: registros de texto libre, usados a menudo para verificación de dominio o configuración de seguridad de correo (SPF, DKIM). A veces revelan qué servicios de terceros usa una organización (Google Workspace, proveedores de email marketing, etc.).

### Enumeración de subdominios

Una organización rara vez tiene un solo dominio expuesto — suele haber subdominios (`correo.ejemplo.com`, `dev.ejemplo.com`, `vpn.ejemplo.com`...) que a veces no están pensados para ser públicos pero lo son.

```bash
subfinder -d example.com
```
- `subfinder` (viene en Kali) busca en múltiples fuentes públicas (certificados SSL, motores de búsqueda, bases de datos de terceros) qué subdominios existen para ese dominio, sin necesidad de "adivinarlos" uno a uno.

## 🪜 Pasos a seguir

1. Elige un dominio para practicar (el tuyo propio, o `example.com` si no tienes ninguno).
2. Consulta `whois` y anota qué información pública obtienes.
3. Usa `dig` para sacar los registros A, MX, NS y TXT. Anota qué te dice cada uno sobre la infraestructura del dominio.
4. Ejecuta `subfinder` y comprueba cuántos subdominios encuentra.
5. En tu `writeup.md`, resume qué información reuniste sin lanzar ni un solo paquete "activo" contra el objetivo, y por qué esta fase se hace siempre antes del escaneo activo (Laboratorio 01).

## 💡 Pistas si te atascas

- Si `whois` no está instalado: `sudo apt install whois`.
- Si `subfinder` no está instalado: `sudo apt install subfinder` (o revisa la documentación oficial, algunas versiones de Kali lo traen ya).
- No todos los dominios tienen registros TXT o MX — si `dig` te devuelve una respuesta vacía (`ANSWER: 0`), es un resultado válido, no un error tuyo.

## 🚀 Reto extra

- Prueba `dig example.com ANY` (si tu resolver lo permite) para ver todos los tipos de registro de golpe, y compáralo con consultar uno a uno.
- Busca el dominio en [crt.sh](https://crt.sh), una base de datos pública de certificados SSL emitidos. Muchas veces revela subdominios que `subfinder` no encontró, porque cualquier certificado emitido para un subdominio queda registrado ahí públicamente.
