# Laboratorio 15 · Política de contraseñas y bloqueo de cuentas

## 🎯 Objetivo

Cerrar el bloque de hardening atacando el problema de raíz del Laboratorio 05: si las contraseñas débiles son el problema, vas a configurar el sistema para que no se puedan crear contraseñas débiles, y para que bloquee una cuenta tras demasiados intentos fallidos (incluso si Fail2ban, del Laboratorio 13, fallara o no estuviera activo).

## 🧪 Entorno necesario

- Cualquiera de tus VMs Linux de laboratorio con acceso de administrador.

## 📖 Dos mecanismos distintos, a dos niveles

### 1. Forzar contraseñas fuertes al crearlas: PAM + `pwquality`

**PAM** (Pluggable Authentication Modules) es el sistema que Linux usa para gestionar la autenticación de forma modular — login, `sudo`, cambio de contraseña, todo pasa por PAM. Uno de sus módulos, `pam_pwquality`, permite exigir requisitos mínimos cuando alguien establece una contraseña nueva.

```bash
sudo apt install libpam-pwquality
```

Edita `/etc/security/pwquality.conf`:

```ini
minlen = 12
dcredit = -1
ucredit = -1
lcredit = -1
ocredit = -1
```
- `minlen = 12`: longitud mínima de 12 caracteres.
- `dcredit = -1`: exige al menos un dígito (el signo negativo significa "obligatorio", no "opcional").
- `ucredit = -1`: exige al menos una mayúscula.
- `lcredit = -1`: exige al menos una minúscula.
- `ocredit = -1`: exige al menos un carácter especial (símbolo).

Una vez configurado, prueba a cambiar una contraseña con `passwd` usando algo débil como `1234` y observa que el sistema la rechaza explicando por qué.

### 2. Bloquear la cuenta tras demasiados intentos fallidos de login local: `pam_tally2` / `faillock`

Esto es distinto a Fail2ban (que bloquea la **IP** a nivel de red): esto bloquea la **cuenta de usuario** a nivel del propio sistema operativo, independientemente de desde dónde se intente entrar.

En sistemas más recientes se usa `pam_faillock`. Revisa `/etc/pam.d/common-auth` (o `/etc/pam.d/sshd` según tu distribución) y añade:

```
auth required pam_faillock.so preauth silent deny=5 unlock_time=600
auth [default=die] pam_faillock.so authfail deny=5 unlock_time=600
```
- `deny=5`: número de intentos fallidos permitidos antes de bloquear la cuenta.
- `unlock_time=600`: segundos que permanece bloqueada antes de desbloquearse sola.

```bash
sudo faillock --user msfadmin
```
- Consulta cuántos intentos fallidos lleva registrados una cuenta concreta y si está bloqueada.

```bash
sudo faillock --user msfadmin --reset
```
- Desbloquea manualmente la cuenta (útil en tus pruebas, para no esperar el `unlock_time`).

## 🪜 Pasos a seguir

1. Instala y configura `pam_pwquality` con los requisitos de arriba.
2. Intenta cambiar una contraseña a algo débil (`passwd` de tu usuario, prueba con `1234` o `password`) y confirma que el sistema la rechaza, leyendo el mensaje de error que te da.
3. Cambia la contraseña a algo que sí cumpla los requisitos y confirma que esta vez te deja.
4. Configura `pam_faillock` (o `pam_tally2` si tu distribución es más antigua) con `deny=5`.
5. Intenta iniciar sesión (por SSH o localmente) con la contraseña equivocada 5 veces seguidas, y comprueba con `faillock --user` que la cuenta ha quedado bloqueada incluso con la contraseña correcta en el sexto intento.
6. En tu `writeup.md`, explica la diferencia entre este bloqueo (a nivel de cuenta, en el propio sistema) y el de Fail2ban del Laboratorio 13 (a nivel de IP, en el firewall) — ¿por qué tiene sentido tener los dos a la vez?

## 💡 Pistas si te atascas

- La sintaxis exacta y el nombre del módulo (`pam_tally2` vs `pam_faillock`) varía según la versión de tu distribución — comprueba con `man pam_faillock` o `man pam_tally2` cuál está disponible en tu sistema.
- Ten cuidado de no bloquearte a ti mismo sin tener otra forma de entrar (la consola de VirtualBox, como en laboratorios anteriores, es tu red de seguridad).

## 🚀 Reto extra

- Esto cierra el Bloque 4. Repasa los cinco laboratorios (SSH, firewall, Fail2ban, auditoría, contraseñas) y, en un documento aparte, resume cómo estos cinco cambios juntos habrían evitado o dificultado cada uno de los ataques que hiciste en el Bloque 2 — es exactamente el ejercicio que vas a formalizar en el Bloque 5 (proyecto final).
