# Laboratorio 02 · Enumeración de servicios (SMB, FTP, HTTP)

## 🎯 Objetivo

Coger los puertos abiertos que encontraste en el Laboratorio 01 y "exprimirlos": entrar en detalle en tres servicios muy comunes (SMB, FTP y HTTP) para ver qué información exponen antes incluso de intentar explotar nada.

## 🧪 Entorno necesario

- Las mismas dos VMs del laboratorio anterior (Kali como atacante, Metasploitable2 como objetivo), en la misma red interna.
- El resultado de tu escaneo Nmap del Laboratorio 01 a mano, para saber qué puertos tiene abiertos tu objetivo.

## 📖 Qué es "enumerar" y por qué importa

Nmap te dice *que* un puerto está abierto y, con suerte, qué servicio corre. **Enumerar** es el paso siguiente: entrar en ese servicio concreto y sacarle el máximo detalle posible — nombres de usuario, carpetas compartidas, versión exacta del software, ficheros accesibles... Muchas veces la enumeración por sí sola ya revela información sensible, sin necesidad de "hackear" nada.

### SMB (puerto 445, a veces también 139)

SMB es el protocolo que usa Windows (y Samba en Linux) para compartir carpetas e impresoras en red. Es uno de los servicios más golosos de enumerar porque a menudo revela nombres de usuario y carpetas compartidas sin autenticación.

```bash
smbclient -L //192.168.56.X -N
```
- `smbclient`: cliente SMB de línea de comandos.
- `-L`: lista los recursos ("shares") que ofrece la máquina.
- `-N`: intenta conectar sin contraseña (null session) — en máquinas mal configuradas, esto solo ya te deja ver carpetas compartidas.

```bash
nmap --script smb-enum-shares,smb-enum-users -p445 192.168.56.X
```
- Dos scripts NSE que listan, respectivamente, las carpetas compartidas y los usuarios del sistema, directamente desde Nmap.

### FTP (puerto 21)

FTP es un protocolo de transferencia de ficheros, antiguo y a menudo mal asegurado. Un fallo de configuración clásico es permitir **login anónimo**.

```bash
ftp 192.168.56.X
```
- Cuando te pida usuario, prueba con `anonymous` y una contraseña vacía o cualquier texto a modo de email (es la convención histórica). Si te deja entrar, ya tienes un hallazgo que anotar.

```bash
nmap --script ftp-anon -p21 192.168.56.X
```
- Hace esa misma comprobación automáticamente y te dice si el login anónimo está permitido.

### HTTP (puerto 80, a veces 8080 o 443 para HTTPS)

Un servidor web expone mucho más que la página principal. Interesa ver qué hay "por detrás": otras carpetas, ficheros de configuración olvidados, paneles de administración...

```bash
curl -I http://192.168.56.X
```
- `-I`: pide solo las cabeceras HTTP, no la página entera. Rápido para ver qué servidor web es (Apache, Nginx...) y su versión, si la expone.

```bash
gobuster dir -u http://192.168.56.X -w /usr/share/wordlists/dirb/common.txt
```
- `gobuster dir`: prueba una lista de nombres de carpeta/fichero comunes contra la URL, una a una, y te dice cuáles existen (responden con 200/301 en vez de 404).
- `-w ruta`: el diccionario de palabras a probar. Kali trae varios en `/usr/share/wordlists/`.
- Esto es "fuerza bruta de rutas": no adivina contraseñas, adivina qué URLs existen.

## 🪜 Pasos a seguir

1. Repasa tu escaneo del Laboratorio 01: ¿tiene tu Metasploitable los puertos 21, 80 y 445 abiertos? (Si usas Metasploitable2, sí los tiene.)
2. Enumera SMB con `smbclient -L` y con los scripts de Nmap. Anota qué carpetas o usuarios encuentras.
3. Comprueba si el FTP permite login anónimo, a mano y con el script de Nmap.
4. Mira las cabeceras HTTP con `curl -I` y después lanza `gobuster` para descubrir rutas ocultas.
5. En tu `writeup.md`, resume: qué información sensible conseguiste solo con enumeración (sin explotar nada todavía), y qué servicio te parece el punto de entrada más prometedor para el Bloque 2.

## 💡 Pistas si te atascas

- Si `smbclient` no está instalado en tu Kali, `sudo apt install smbclient`.
- `gobuster` puede tardar varios minutos según el diccionario — empieza con `common.txt`, que es pequeño, antes de probar diccionarios más grandes.
- Si no ves ninguna carpeta SMB, prueba también sin `-N` y con un usuario/contraseña vacíos, algunas versiones de `smbclient` se comportan distinto.

## 🚀 Reto extra

- Investiga qué es **enum4linux** (viene en Kali) y pruébalo contra tu objetivo: automatiza buena parte de la enumeración SMB que acabas de hacer a mano. Compara lo que saca con lo que encontraste tú.
