# Laboratorio 07 · Captura de paquetes con Wireshark y tcpdump

## 🎯 Objetivo

Empezar a "ver" el tráfico de red en vez de solo confiar en lo que te dicen las herramientas de más alto nivel. Vas a capturar tráfico real generado por ti mismo (una conexión SSH, una navegación web) y aprender a leerlo.

## 🧪 Entorno necesario

- Kali con Wireshark instalado (ya viene por defecto) y acceso a la misma red interna que tu Metasploitable2.

## 📖 Qué es capturar tráfico y por qué sirve

Cuando dos máquinas se comunican por red, los datos viajan en **paquetes**: pequeños fragmentos con una cabecera (origen, destino, protocolo...) y un contenido. Capturar tráfico significa poner tu tarjeta de red en modo "escucha" y guardar una copia de esos paquetes a medida que pasan, para poder analizarlos después.

Esto es fundamental tanto en ataque (ver qué datos viajan sin cifrar) como en defensa (detectar un ataque mientras ocurre, o investigar uno después de que haya pasado).

### `tcpdump`: captura desde la terminal

```bash
sudo tcpdump -i eth0 -w captura.pcap
```
- `-i eth0`: interfaz de red por la que escuchar (comprueba la tuya con `ip a`, puede llamarse `eth0`, `enp0s3`, etc.).
- `-w captura.pcap`: guarda el tráfico capturado en un fichero `.pcap` (formato estándar) en vez de solo mostrarlo en pantalla.
- Deja esto corriendo, y en otra terminal genera algo de tráfico (por ejemplo, `ssh msfadmin@192.168.56.X` hacia tu Metasploitable). Luego para la captura con `Ctrl+C`.

### Wireshark: analizar la captura visualmente

```bash
wireshark captura.pcap
```
- Abre el fichero capturado en la interfaz gráfica de Wireshark, donde puedes ver cada paquete, filtrarlos, y reconstruir conversaciones completas.

Filtros útiles para empezar (se escriben en la barra de filtro de Wireshark, arriba):

- `ip.addr == 192.168.56.X`: muestra solo paquetes hacia o desde esa IP.
- `tcp.port == 22`: muestra solo tráfico del puerto 22 (SSH).
- `http`: muestra solo tráfico HTTP (no HTTPS, ese va cifrado y Wireshark no puede leerlo sin la clave).
- Clic derecho sobre un paquete → `Follow > TCP Stream`: reconstruye la conversación completa de esa conexión en un solo panel legible, en vez de paquete a paquete.

## 🪜 Pasos a seguir

1. Lanza una captura con `tcpdump` en Kali mientras te conectas por SSH a tu Metasploitable.
2. Para la captura y ábrela en Wireshark.
3. Filtra por `tcp.port == 22` y observa el "three-way handshake" (los primeros tres paquetes de cualquier conexión TCP: SYN, SYN-ACK, ACK) al principio de la conversación.
4. Repite el ejercicio pero generando tráfico **HTTP** en texto plano (por ejemplo, navegando con `curl http://192.168.56.X` a un servicio web de Metasploitable, o si tiene un login en texto plano, usándolo). Usa `Follow > TCP Stream` para ver si consigues leer contenido en claro — usuario, contraseña, lo que sea.
5. En tu `writeup.md`, documenta: qué pudiste ver en texto plano en el tráfico HTTP (y por qué eso es un problema de seguridad), y qué diferencia notaste al intentar lo mismo con tráfico SSH (que va cifrado).

## 💡 Pistas si te atascas

- Si `tcpdump` no captura nada, revisa que estés usando la interfaz correcta (`ip a` para confirmar el nombre) y que el tráfico realmente pase por esa interfaz (no por `lo`, la interfaz de loopback).
- Para generar tráfico HTTP en claro puedes usar DVWA, la aplicación web vulnerable que trae Metasploitable2 — su login no va cifrado por defecto.

## 🚀 Reto extra

- Investiga qué es ARP y, con un filtro `arp` en Wireshark, observa cómo tu Kali descubre la dirección MAC de tu Metasploitable antes de poder enviarle el primer paquete.
