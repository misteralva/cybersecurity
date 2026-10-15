# Laboratorio 11 · Hardening de SSH

## 🎯 Objetivo

Coger un servidor SSH configurado "por defecto" (como el de tu Metasploitable, deliberadamente inseguro) y endurecerlo paso a paso, cerrando exactamente las puertas que explotaste en los Laboratorios 05 y 06. Vas a verificar cada cambio volviendo a lanzar el mismo ataque de fuerza bruta.

## 🧪 Entorno necesario

- Para no estropear tu Metasploitable (la necesitas "vulnerable" para otros laboratorios), usa una **VM Linux nueva** (Ubuntu Server o Debian, instalación mínima) donde tengas control total, o si prefieres, haz un **snapshot** de tu Metasploitable en VirtualBox antes de tocar nada, para poder revertir los cambios después.
- Tu Kali, para repetir el ataque de fuerza bruta del Laboratorio 05 después de cada cambio.

## 📖 Qué vas a cambiar y por qué

La configuración de SSH vive en `/etc/ssh/sshd_config`. Cada línea que vas a tocar cierra una vía de ataque concreta:

### 1. Desactivar el login de root directamente

```
PermitRootLogin no
```
- Si alguien compromete una cuenta de root directamente, tiene control total de inmediato. Obligar a entrar primero con un usuario normal y usar `sudo` después añade una capa extra: necesitas comprometer dos cosas, no una.

### 2. Autenticación solo por clave, no por contraseña

```
PasswordAuthentication no
PubkeyAuthentication yes
```
- Esto es lo que **elimina por completo** el ataque de fuerza bruta del Laboratorio 05: si no se puede entrar con contraseña, no hay contraseña que adivinar. En su lugar, necesitas un par de claves criptográficas (pública/privada) — la privada se queda solo en tu máquina, nunca viaja por la red.
- Antes de aplicar esto, genera un par de claves en tu Kali con `ssh-keygen` y copia la pública al servidor con `ssh-copy-id usuario@IP_SERVIDOR`, o no podrás volver a entrar.

### 3. Cambiar el puerto por defecto (opcional, "seguridad por oscuridad")

```
Port 2222
```
- Esto no detiene a un atacante decidido (un `nmap -p-` lo encuentra igual), pero reduce muchísimo el ruido de bots automáticos que escanean el puerto 22 por defecto en todo internet. Es una capa extra, no una defensa real por sí sola — documenta en tu writeup por qué no basta con esto solo.

### 4. Limitar qué usuarios pueden conectarse

```
AllowUsers msfadmin
```
- Restringe el acceso SSH solo a los usuarios que realmente lo necesitan, en vez de cualquier cuenta del sistema.

### Aplicar los cambios

```bash
sudo systemctl restart sshd
```
- Reinicia el servicio SSH para que lea la configuración nueva. **Importante**: antes de cerrar tu sesión actual, abre una segunda conexión SSH para confirmar que todavía puedes entrar — si algo está mal configurado, te quedas fuera y necesitarás acceso a la consola de VirtualBox para arreglarlo.

## 🪜 Pasos a seguir

1. Haz un snapshot de tu VM antes de empezar (en VirtualBox: clic derecho sobre la VM → Tomar instantánea).
2. Genera un par de claves SSH en tu Kali y cópialas al servidor con `ssh-copy-id`.
3. Edita `/etc/ssh/sshd_config` aplicando los cuatro cambios de arriba, uno a uno, reiniciando el servicio y comprobando que sigues teniendo acceso después de cada cambio.
4. Una vez aplicados todos, repite el ataque de Hydra del Laboratorio 05 contra este servidor ya endurecido.
5. En tu `writeup.md`, documenta: qué pasó con el ataque de fuerza bruta después del hardening, y cuál de los cuatro cambios crees que es el que realmente lo detiene (distíngelo de los que solo "ayudan un poco").

## 💡 Pistas si te atascas

- Si te quedas sin acceso SSH tras un cambio, usa la consola de VirtualBox (la ventana de la VM directamente, no SSH) para entrar como si estuvieras físicamente delante de la máquina y revertir el cambio.
- `sudo sshd -t` comprueba la sintaxis de tu `sshd_config` sin reiniciar el servicio — úsalo antes de reiniciar para detectar errores de escritura.

## 🚀 Reto extra

- Investiga `fail2ban` (lo configurarás de verdad en el Laboratorio 13) como complemento a `PasswordAuthentication no`: ¿para qué serviría tenerlo si ya has desactivado la autenticación por contraseña?
