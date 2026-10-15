# Laboratorio 14 · Auditoría de permisos y usuarios

## 🎯 Objetivo

Revisar una máquina desde el punto de vista de "qué podría usar un atacante si consiguiera entrar como un usuario normal" — exactamente lo que tú mismo buscaste en el Laboratorio 06 (escalada de privilegios), pero ahora desde el lado defensivo: encontrarlo y corregirlo antes de que alguien más lo haga.

## 🧪 Entorno necesario

- Cualquiera de tus VMs de laboratorio (Metasploitable2 es ideal porque, al ser deliberadamente insegura, encontrarás bastantes cosas que auditar).

## 📖 Qué revisar y por qué

### 1. Cuentas de usuario existentes

```bash
cat /etc/passwd
```
- Lista todas las cuentas del sistema. Cada línea tiene el formato `usuario:x:UID:GID:comentario:home:shell`. Fíjate especialmente en:
  - Cuentas con **UID 0** (además de `root`) — UID 0 significa privilegios de root, así que cualquier otra cuenta con ese UID es tan poderosa como root y a menudo pasa desapercibida.
  - Cuentas de servicio (como `ftp`, `www-data`) que tengan una shell real asignada (`/bin/bash`) en vez de `/usr/sbin/nologin` — una cuenta de servicio no debería poder iniciar sesión interactiva.

```bash
awk -F: '$3 == 0 {print $1}' /etc/passwd
```
- Filtra directamente las cuentas con UID 0 (columna 3 del fichero, separado por `:`), para no tener que revisarlas todas a mano.

### 2. Permisos de `sudo`

```bash
sudo cat /etc/sudoers
cat /etc/sudoers.d/*
```
- Revisa quién tiene acceso a `sudo` y con qué alcance. Presta atención especial a líneas con `NOPASSWD`, que permiten ejecutar comandos como root sin pedir contraseña — como viste en el Laboratorio 06, son una vía directa de escalada si el comando permitido no está bien acotado.

### 3. Ficheros con permisos de escritura demasiado abiertos

```bash
find / -xdev -writable -type f -not -path "/proc/*" 2>/dev/null
```
- Busca ficheros que cualquier usuario pueda modificar. Un fichero de configuración importante (por ejemplo, uno que se ejecute automáticamente, o un script que corra como root vía cron) con permisos de escritura para todos es una vía de ataque: si puedes modificarlo, puedes hacer que ejecute lo que tú quieras.
- `-xdev`: no cruza a otros sistemas de ficheros montados (evita resultados ruidosos de particiones externas).

### 4. Tareas programadas (cron)

```bash
cat /etc/crontab
ls /etc/cron.d/
crontab -l -u msfadmin
```
- Revisa qué tareas se ejecutan automáticamente y con qué usuario. Si una tarea cron corre como root y apunta a un script que un usuario normal puede modificar (combínalo con el punto anterior), tienes una vía de escalada: editas el script, esperas a que cron lo ejecute, y ese código corre como root.

## 🪜 Pasos a seguir

1. Revisa `/etc/passwd` y busca cuentas con UID 0 aparte de `root`, y cuentas de servicio con shell interactiva.
2. Revisa `/etc/sudoers` y los ficheros en `/etc/sudoers.d/` buscando entradas `NOPASSWD` o permisos demasiado amplios.
3. Busca ficheros de escritura global con el comando de arriba (puede tardar un poco).
4. Revisa las tareas cron del sistema y de los usuarios.
5. En tu `writeup.md`, haz una lista de "hallazgos" como la haría un auditor real: qué encontraste, por qué es un riesgo, y qué cambio concreto propondrías para corregirlo (no hace falta que lo apliques todo, con proponerlo justificado basta para este laboratorio).

## 💡 Pistas si te atascas

- Metasploitable2 tiene configuraciones deliberadamente débiles — si no encuentras nada "raro" a la primera, revisa con calma cada sección, suele haber más de un hallazgo.
- Compara lo que encuentres aquí con lo que ya sabías del Laboratorio 06: ¿la vía de escalada que usaste entonces aparece también en esta auditoría?

## 🚀 Reto extra

- Investiga **Lynis**, una herramienta de auditoría de seguridad automatizada para Linux (`sudo apt install lynis`, luego `sudo lynis audit system`), y compara su informe con los hallazgos que identificaste tú a mano.
