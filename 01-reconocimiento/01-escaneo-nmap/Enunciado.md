# Laboratorio 01 · Escaneo con Nmap

## 🎯 Objetivo

Realizar un reconocimiento completo de una máquina objetivo usando Nmap: descubrir qué puertos están abiertos, qué servicios corren en ellos y qué versión tiene cada uno. Es el primer paso de cualquier auditoría de seguridad: antes de atacar o defender nada, necesitas saber qué hay expuesto.

## 🧪 Entorno necesario

- VirtualBox con dos VMs en la misma red interna (host-only o NAT network):
  - Tu VM atacante: **Kali Linux** (ya trae Nmap instalado).
  - Tu VM objetivo: **Metasploitable2** (una máquina deliberadamente vulnerable, pensada para practicar — no es un sistema real que puedas "romper" por accidente).
- Si no las tienes montadas todavía: descarga las imágenes OVA de Kali y de Metasploitable2 e impórtalas en VirtualBox con `Archivo > Importar servicio virtualizado`. Configura el adaptador de red de ambas como la misma red interna para que se vean entre sí pero no salgan a tu red doméstica.

## 📖 Qué es Nmap y por qué se usa

**Nmap** (Network Mapper) es una herramienta que manda paquetes a una máquina y, según cómo responde (o si no responde), deduce qué puertos están abiertos y qué servicio hay detrás. Es la herramienta de reconocimiento más usada en el sector, tanto por atacantes como por quien audita su propia red.

La idea de fondo: cada "puerto" es como una puerta numerada (del 0 al 65535) en una máquina. Si un servicio está "escuchando" en un puerto (por ejemplo, un servidor web en el 80), la puerta responde cuando Nmap llama. Si no hay nada escuchando, no responde o responde "cerrado".

### Conceptos y comandos explicados uno a uno

**1. Descubrir qué máquinas están vivas en la red**

```bash
nmap -sn 192.168.56.0/24
```
- `-sn`: "scan no port" — solo comprueba qué IPs responden (ping scan), sin tocar puertos todavía. Útil cuando no sabes la IP exacta de tu objetivo.
- `192.168.56.0/24`: el rango de red a comprobar (ajusta esto a la red interna que hayas configurado en VirtualBox — mira la IP de tu Kali con `ip a` para saber qué rango usar).

**2. Escaneo de puertos TCP, dos formas distintas**

```bash
sudo nmap -sS 192.168.56.X
nmap -sT 192.168.56.X
```
- `-sS` (**SYN scan**): manda el primer paquete de un "saludo" TCP (SYN) y, si el puerto responde, corta la conexión antes de completarla. Es más rápido y más discreto porque nunca llega a abrir una conexión completa — pero necesita privilegios de administrador (`sudo`) para poder manipular paquetes a bajo nivel.
- `-sT` (**TCP connect scan**): completa la conexión TCP entera, como haría cualquier programa normal. No necesita privilegios de root, pero es más lento y deja más rastro en los logs del objetivo.
- Sustituye `192.168.56.X` por la IP real de tu Metasploitable.

**3. Escanear todos los puertos, no solo los más comunes**

```bash
nmap -p- 192.168.56.X
```
- Por defecto, Nmap solo mira los ~1000 puertos más habituales. `-p-` le dice que mire los 65535, por si hay algo escondido en un puerto poco común. Tarda más, pero en una auditoría real es necesario para no dejarte nada.

**4. Detectar qué servicio y qué versión corre en cada puerto abierto**

```bash
nmap -sV 192.168.56.X
```
- `-sV`: una vez sabe qué puertos están abiertos, Nmap manda peticiones específicas a cada uno para identificar qué software hay detrás y en qué versión (por ejemplo, "Apache httpd 2.2.8" en vez de solo "puerto 80 abierto"). Esto es clave porque una versión antigua concreta puede tener una vulnerabilidad conocida.

**5. Detectar el sistema operativo**

```bash
sudo nmap -O 192.168.56.X
```
- `-O`: analiza peculiaridades de cómo responde la pila de red de la máquina (TTL, tamaño de ventana TCP, etc.) para adivinar qué sistema operativo corre. No siempre acierta al 100%, pero suele orientar bastante bien.

**6. Scripts automáticos de reconocimiento (NSE)**

```bash
nmap -sC 192.168.56.X
```
- `-sC`: lanza un conjunto de scripts "seguros" predefinidos (Nmap Scripting Engine) que, por ejemplo, intentan sacar el banner de un servicio, comprobar si un FTP permite login anónimo, o listar carpetas compartidas por SMB. Es información extra gratis sobre lo que ya has descubierto.

**7. Juntarlo todo y guardar el resultado**

```bash
sudo nmap -p- -sV -O -sC -oA resultados/lab01-nmap 192.168.56.X
```
- `-oA ruta`: guarda el resultado en tres formatos a la vez (texto, XML y "grepeable") con el prefijo `ruta` que le des. Así no pierdes el resultado del escaneo y puedes volver a consultarlo luego sin tener que repetirlo.

## 🪜 Pasos a seguir (resumen)

1. Levanta las dos VMs y comprueba que se ven entre ellas (puedes hacer un `ping` simple de una a otra).
2. Con `-sn`, descubre la IP de tu objetivo.
3. Lanza un escaneo de todos los puertos (`-p-`) contra esa IP.
4. Sobre el resultado, añade detección de versión (`-sV`), de SO (`-O`) y scripts por defecto (`-sC`).
5. Guarda el resultado con `-oA`.
6. Abre un fichero `writeup.md` en esta misma carpeta y anota: qué puertos encontraste abiertos, qué servicio/versión corre en cada uno, y cuál de ellos te parece más interesante para investigar en el siguiente laboratorio (y por qué).

## 💡 Pistas si te atascas

- `man nmap` o `nmap -h` listan todas las opciones si olvidas una.
- Si el escaneo completo de puertos tarda demasiado, puedes añadir `-T4` para que vaya más rápido (sacrifica algo de sigilo a cambio de velocidad — para tu propio laboratorio no pasa nada).
- Si `-sS` te da un error de permisos, es porque te falta el `sudo` delante.

## 🚀 Reto extra (opcional, para cuando te sientas cómodo con lo de arriba)

- Prueba un escaneo UDP (`nmap -sU`) contra el mismo objetivo y compara qué tarda y qué encuentra respecto al TCP.
- Elige uno de los servicios con versión identificada y busca en internet (por ejemplo en [cve.mitre.org](https://cve.mitre.org) o `searchsploit <nombre_servicio>` desde Kali) si existe algún CVE conocido para esa versión exacta. No hace falta explotarlo — solo identificarlo. Eso lo harás en el Bloque 2.
