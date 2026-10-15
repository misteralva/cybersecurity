# Laboratorio 13 · Bloqueo automático de fuerza bruta con Fail2ban

## 🎯 Objetivo

Automatizar completamente lo que hiciste a mano en el Laboratorio 10 (detectar fuerza bruta leyendo `auth.log`): vas a configurar una herramienta que vigile el log en tiempo real y **bloquee automáticamente** la IP atacante tras un número de intentos fallidos.

## 🧪 Entorno necesario

- La misma VM de los Laboratorios 11-12 (con SSH y UFW ya configurados), o Metasploitable con un snapshot previo.
- Tu Kali, para lanzar el ataque de Hydra del Laboratorio 05 y comprobar que esta vez te bloquea.

> ℹ️ Si en el Laboratorio 11 ya desactivaste `PasswordAuthentication`, vuelve a activarla temporalmente solo para este laboratorio (o practica Fail2ban contra otro servicio, como FTP) — necesitas que el ataque de fuerza bruta sea posible para poder ver cómo Fail2ban lo detiene.

## 📖 Qué es Fail2ban y cómo funciona

**Fail2ban** vigila ficheros de log (como `/var/log/auth.log`) buscando patrones que definas — típicamente, intentos de login fallidos. Cuando una misma IP supera un número de fallos en un periodo de tiempo, Fail2ban añade automáticamente una regla de firewall para bloquear esa IP durante un tiempo determinado. Es, literalmente, el mismo análisis que hiciste a mano con `grep`/`awk` en el Laboratorio 10, pero corriendo continuamente y actuando por sí solo.

Dos conceptos clave de su configuración:

- **jail** ("celda"): una configuración concreta para un servicio determinado (hay una jail para SSH, otra para Apache, etc.), con sus propias reglas de cuántos fallos tolerar.
- **filter**: la expresión que usa para reconocer una línea de log como "intento fallido" para ese servicio.

### Instalación y configuración básica

```bash
sudo apt install fail2ban
```

Fail2ban lee su configuración de `/etc/fail2ban/jail.conf`, pero **nunca se edita ese fichero directamente** (se sobrescribe en actualizaciones) — se crea uno nuevo:

```bash
sudo nano /etc/fail2ban/jail.local
```

Con un contenido como este:

```ini
[sshd]
enabled = true
port = 2222
maxretry = 3
findtime = 60
bantime = 600
```
- `[sshd]`: activa la jail predefinida para SSH (Fail2ban ya trae el filtro para reconocer sus logs, no hace falta escribirlo tú).
- `port = 2222`: el puerto real de tu SSH, si lo cambiaste en el Laboratorio 11.
- `maxretry = 3`: número de intentos fallidos permitidos antes de bloquear.
- `findtime = 60`: periodo de tiempo (en segundos) durante el cual se cuentan esos intentos — 3 fallos en más de 60 segundos no cuentan como ataque.
- `bantime = 600`: cuánto tiempo (en segundos) queda bloqueada la IP una vez se dispara el bloqueo.

```bash
sudo systemctl restart fail2ban
```
- Aplica la configuración nueva.

### Comandos para comprobar que funciona

```bash
sudo fail2ban-client status sshd
```
- Te muestra el estado de esa jail: cuántos intentos fallidos ha visto y, si hay alguna, qué IPs tiene bloqueadas ahora mismo.

```bash
sudo fail2ban-client unban 192.168.56.10
```
- Desbloquea una IP manualmente (útil durante las pruebas, para no tener que esperar a que expire el `bantime`).

## 🪜 Pasos a seguir

1. Instala y configura Fail2ban con la jail de SSH, usando `maxretry = 3`.
2. Desde tu Kali, lanza el ataque de Hydra del Laboratorio 05 contra este servidor (con un diccionario de al menos 5-6 contraseñas, para asegurar que supera el límite de 3 intentos).
3. Observa qué pasa a mitad del ataque — Hydra debería empezar a recibir rechazos de conexión en vez de poder seguir probando contraseñas.
4. Comprueba con `fail2ban-client status sshd` que tu IP de Kali aparece como bloqueada.
5. Intenta conectarte tú mismo por SSH desde Kali mientras estás bloqueado, para confirmar que ni siquiera tú puedes entrar durante el `bantime`.
6. En tu `writeup.md`, compara el resultado de Hydra en el Laboratorio 05 (sin Fail2ban) con el de ahora, y anota cuánto tiempo tardó en bloquearte desde que empezaste el ataque.

## 💡 Pistas si te atascas

- Si Fail2ban no detecta nada, revisa que `port` en tu jail coincida exactamente con el puerto real de SSH.
- `sudo fail2ban-client status` (sin especificar jail) te lista todas las jails activas, por si el nombre no es exactamente `sshd` en tu configuración.

## 🚀 Reto extra

- Crea una jail adicional para otro servicio de tu laboratorio (por ejemplo, el login web de DVWA si lo tienes montado) usando o adaptando uno de los filtros que trae Fail2ban por defecto en `/etc/fail2ban/filter.d/`.
