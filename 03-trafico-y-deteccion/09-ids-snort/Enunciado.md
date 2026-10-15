# Laboratorio 09 · Fundamentos de IDS con Snort

## 🎯 Objetivo

Automatizar lo que hiciste "a mano" en el Laboratorio 08: en vez de mirar capturas tú mismo buscando patrones sospechosos, vas a montar un **IDS** (Intrusion Detection System) que lo haga por ti y te avise.

## 🧪 Entorno necesario

- Una VM Linux donde instalar Snort (puede ser tu propia Kali, o si quieres practicar algo distinto, una VM nueva de Ubuntu Server — ambas valen).
- Tu Kali o Metasploitable generando tráfico de prueba hacia esa máquina.

## 📖 Qué es un IDS y cómo funciona Snort

Un **IDS** (sistema de detección de intrusiones) analiza tráfico de red en tiempo real y lo compara contra un conjunto de **reglas** (firmas de patrones conocidos: un escaneo, un intento de exploit concreto, tráfico hacia una IP maliciosa conocida...). Cuando algo coincide con una regla, genera una **alerta**. A diferencia de un firewall, un IDS no bloquea nada por sí solo — solo avisa (eso sería un IPS, "sistema de prevención", un paso más allá).

**Snort** es uno de los IDS más usados y de código abierto. Funciona con un fichero de configuración y un conjunto de reglas en texto plano, bastante legibles una vez entiendes la sintaxis.

### Instalación básica

```bash
sudo apt install snort
```
- Durante la instalación te pedirá la red local a monitorizar (`HOME_NET`) — pon el rango de tu red interna de laboratorio, por ejemplo `192.168.56.0/24`.

### Anatomía de una regla

```
alert tcp any any -> $HOME_NET 22 (msg:"Posible conexion SSH entrante"; sid:1000001;)
```
- `alert`: la acción a tomar (generar una alerta; otras opciones serían `log` o `pass`).
- `tcp`: protocolo a vigilar.
- `any any -> $HOME_NET 22`: origen (cualquier IP, cualquier puerto) → destino (tu red local, puerto 22). La flecha indica la dirección del tráfico que te interesa.
- `msg:"..."`: el texto que verás en la alerta, para identificar qué la disparó.
- `sid:1000001`: un identificador único obligatorio para cada regla personalizada (los números por debajo de 1.000.000 están reservados para reglas oficiales de Snort).

### Una regla más útil: detectar un escaneo

```
alert tcp any any -> $HOME_NET any (msg:"Posible escaneo de puertos"; flags:S; threshold: type threshold, track by_src, count 20, seconds 5; sid:1000002;)
```
- `flags:S`: solo paquetes con el flag SYN activado (el primer paso de una conexión, como viste en el Laboratorio 08).
- `threshold: ... count 20, seconds 5`: solo dispara la alerta si la misma IP origen genera 20 o más paquetes así en 5 segundos — exactamente el patrón de un escaneo que identificaste a mano en el laboratorio anterior.

### Ejecutar Snort en modo de detección

```bash
sudo snort -A console -q -c /etc/snort/snort.conf -i eth0
```
- `-A console`: muestra las alertas directamente por pantalla.
- `-q`: modo silencioso (sin el banner inicial, solo las alertas).
- `-c /etc/snort/snort.conf`: fichero de configuración principal, donde se incluyen tus reglas.
- `-i eth0`: interfaz a escuchar.

## 🪜 Pasos a seguir

1. Instala Snort y configura `HOME_NET` a tu red de laboratorio.
2. Añade las dos reglas de ejemplo de arriba a tu fichero de reglas locales (normalmente `/etc/snort/rules/local.rules`, referenciado desde `snort.conf`).
3. Arranca Snort en modo consola.
4. Desde tu Kali, lanza un `nmap -sS` contra la máquina donde corre Snort y observa si salta la alerta de escaneo.
5. Conéctate también por SSH y confirma que salta la primera regla.
6. En tu `writeup.md`, documenta qué alertas viste, y escribe una tercera regla propia para detectar algo que te parezca relevante (por ejemplo, tráfico FTP, o conexiones a un puerto que en tu red no debería usarse nunca).

## 💡 Pistas si te atascas

- Si Snort no arranca, revisa el log de errores que imprime — normalmente apunta a una línea concreta del `.conf` o de tus reglas con un error de sintaxis.
- El `threshold` de la regla de escaneo significa que necesitas generar tráfico suficiente (un escaneo completo, no un solo paquete) para que salte — un único `ping` no la disparará.

## 🚀 Reto extra

- Investiga la diferencia entre Snort (basado en firmas) y herramientas basadas en anomalías (que aprenden qué es "normal" y alertan ante desviaciones). ¿Qué tipo de ataque detectaría mejor cada enfoque?
