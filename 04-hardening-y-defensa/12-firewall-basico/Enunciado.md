# Laboratorio 12 · Firewall básico con UFW

## 🎯 Objetivo

En el Laboratorio 01 descubriste todos los puertos abiertos de una máquina con Nmap. Ahora vas a aplicar el principio de **mínima exposición**: cerrar todo lo que no sea estrictamente necesario, dejando abierto solo lo que el servidor realmente necesita ofrecer.

## 🧪 Entorno necesario

- La misma VM del Laboratorio 11 (o tu Metasploitable, si prefieres practicar ahí con un snapshot previo).
- Tu Kali, para volver a escanear con Nmap después de aplicar el firewall y confirmar el resultado.

## 📖 Qué es un firewall y cómo piensa UFW

Un **firewall** decide, paquete a paquete, si algo puede entrar o salir de la máquina, según reglas que tú defines. La filosofía más segura es "denegar por defecto, permitir lo necesario" (**default deny**): en vez de intentar bloquear cada cosa mala una a una (imposible de mantener), bloqueas todo y solo abres explícitamente lo que sí quieres permitir.

**UFW** (Uncomplicated Firewall) es una capa simplificada sobre `iptables`/`nftables`, pensada para no tener que escribir reglas de bajo nivel a mano.

### Comandos explicados

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```
- La política por defecto: nada entra a menos que lo permitas explícitamente; las conexiones salientes sí se permiten (normalmente no hace falta restringir lo que el propio servidor inicia hacia fuera).

```bash
sudo ufw allow 2222/tcp
```
- Permite explícitamente el tráfico entrante al puerto SSH que configuraste en el Laboratorio 11 (ajusta el número si usaste otro). **Hazlo antes de activar el firewall**, o te quedarás fuera de tu propia máquina.

```bash
sudo ufw allow 80/tcp
```
- Si tu servidor ofrece un servicio web, permites ese puerto también. La idea es repetir esto solo para los servicios que el servidor realmente necesita exponer — nada más.

```bash
sudo ufw enable
```
- Activa el firewall con las reglas que has definido. A partir de aquí, cualquier puerto que no hayas permitido explícitamente queda bloqueado.

```bash
sudo ufw status verbose
```
- Te muestra las reglas activas, de forma legible, para confirmar que está todo como esperas.

## 🪜 Pasos a seguir

1. Antes de activar nada, lanza un `nmap -p- 192.168.56.X` desde tu Kali y guarda el resultado — este es tu "antes".
2. Configura las políticas por defecto (`deny incoming`, `allow outgoing`).
3. Permite explícitamente solo los puertos que tu servidor necesita (SSH como mínimo; añade HTTP si tienes un servicio web corriendo).
4. Activa UFW y confirma el estado con `ufw status verbose`.
5. Repite el mismo `nmap -p-` desde Kali — este es tu "después".
6. En tu `writeup.md`, compara ambos escaneos: cuántos puertos estaban abiertos antes, cuántos después, y por qué cada puerto que dejaste abierto es necesario (si no puedes justificar por qué un puerto está abierto, probablemente debería estar cerrado).

## 💡 Pistas si te atascas

- Si te quedas sin acceso tras activar UFW, de nuevo recurre a la consola de VirtualBox para entrar y corregir las reglas (`sudo ufw disable` para desactivarlo del todo si necesitas empezar de cero).
- `sudo ufw status numbered` te da cada regla con un número, útil si luego quieres borrar una concreta con `sudo ufw delete <número>`.

## 🚀 Reto extra

- Investiga cómo limitar un puerto por IP de origen en vez de abrirlo a todo el mundo, por ejemplo: `sudo ufw allow from 192.168.56.10 to any port 2222`. ¿En qué escenario real tendría sentido restringir así el acceso SSH?
