# Laboratorio 05 · Fuerza bruta controlada con Hydra

## 🎯 Objetivo

Entender cómo funciona un ataque de fuerza bruta contra credenciales, y por qué es tan importante defenderse de él (lo harás en el Laboratorio 13, Fail2ban). Vas a atacar un servicio SSH o FTP de tu propio laboratorio con contraseñas de un diccionario.

## 🧪 Entorno necesario

- Kali (atacante) y Metasploitable2 (objetivo), misma red interna.
- Metasploitable2 trae una cuenta de usuario conocida y documentada (`msfadmin` / `msfadmin`) — la usarás para verificar que el ataque funciona, pero el ejercicio es simular que *no* conoces la contraseña de antemano.

## 📖 Qué es un ataque de fuerza bruta y qué es Hydra

Un ataque de **fuerza bruta** prueba muchas combinaciones de usuario/contraseña contra un servicio, una tras otra, hasta encontrar una que funcione. Hay dos variantes:

- **Fuerza bruta "pura"**: prueba todas las combinaciones posibles de caracteres. Extremadamente lenta, casi nunca práctica.
- **Ataque de diccionario**: prueba una lista de contraseñas reales/comunes (filtradas de brechas de seguridad conocidas, o simplemente palabras típicas). Es lo que vas a hacer aquí, y es lo que realmente se usa en el mundo real.

**Hydra** es una herramienta que automatiza esto: le das un objetivo, un protocolo (SSH, FTP, HTTP...), una lista de usuarios y una lista de contraseñas, y prueba todas las combinaciones.

### Comando explicado

```bash
hydra -l msfadmin -P /usr/share/wordlists/rockyou.txt ssh://192.168.56.X
```
- `-l msfadmin`: usuario fijo a probar (minúscula = un solo usuario; mayúscula `-L` sería una lista de usuarios).
- `-P /usr/share/wordlists/rockyou.txt`: ruta al diccionario de contraseñas a probar (mayúscula = lista de contraseñas; minúscula `-p` sería una sola).
- `ssh://192.168.56.X`: protocolo y objetivo, en formato URL.

> ⚠️ `rockyou.txt` tiene millones de líneas — contra SSH, que es lento por diseño (cada intento tarda), puede llevar mucho tiempo recorrerlo entero. Para este laboratorio, crea un diccionario pequeño tú mismo con unas pocas contraseñas, incluyendo `msfadmin` entre ellas, para que el ataque termine en segundos:

```bash
echo -e "123456\nadmin\npassword\nmsfadmin\nletmein" > mi_diccionario.txt
hydra -l msfadmin -P mi_diccionario.txt ssh://192.168.56.X
```
- `echo -e "...\n..."`: crea el fichero con una contraseña por línea (`\n` es un salto de línea).
- El resto del comando es igual que el anterior, pero apuntando a tu diccionario pequeño.

Cuando Hydra encuentra una combinación válida, te lo indica claramente en la salida (`login: msfadmin   password: msfadmin`).

## 🪜 Pasos a seguir

1. Confirma que el puerto 22 (SSH) está abierto en tu Metasploitable (revisa tu escaneo del Laboratorio 01; si no lo está, usa FTP en el puerto 21 en su lugar, cambiando `ssh://` por `ftp://`).
2. Crea tu propio diccionario pequeño con 5-10 contraseñas, incluyendo la real entre ellas.
3. Lanza Hydra contra el servicio y observa cuánto tarda en encontrar la combinación correcta.
4. Repite el ataque pero con un diccionario donde la contraseña correcta esté la última de la lista, y cronometra la diferencia.
5. En tu `writeup.md`, anota: cuánto tardó el ataque, qué pasaría si el diccionario tuviera miles de contraseñas en vez de cinco, y qué medida defensiva se te ocurre para frenar esto (lo desarrollarás de verdad en el Bloque 4).

## 💡 Pistas si te atascas

- Si Hydra da error de conexión, revisa primero con un `ssh msfadmin@192.168.56.X` manual que el servicio responde.
- Si va muy lento, añade `-t 4` para limitar el número de hilos en paralelo (menos agresivo, más estable en un laboratorio pequeño).
- Recuerda: esto **solo** se hace contra tu propia máquina de laboratorio. Lanzarlo contra cualquier otro sistema sin permiso es ilegal.

## 🚀 Reto extra

- Prueba el mismo ataque pero contra un formulario de login HTTP en vez de SSH (Metasploitable2 tiene DVWA, una aplicación web vulnerable, entre sus servicios). La sintaxis de Hydra para HTTP es distinta (`http-post-form`) — investiga cómo se usa.
