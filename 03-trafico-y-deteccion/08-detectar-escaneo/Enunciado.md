# Laboratorio 08 · Detectar un escaneo de puertos en una captura

## 🎯 Objetivo

Ponerte ahora en el lado defensivo: vas a provocar tú mismo un escaneo Nmap (como en el Laboratorio 01) mientras capturas el tráfico desde la víctima, y aprenderás a reconocer visualmente el patrón que deja un escaneo — la base de cualquier sistema de detección.

## 🧪 Entorno necesario

- Kali (atacante) y Metasploitable2 (objetivo), misma red interna.
- Wireshark o `tcpdump` instalado en la máquina desde la que vas a capturar (puede ser la propia Kali, capturando su propio tráfico saliente, o idealmente un tercer punto de la red si lo tienes).

## 📖 Qué patrón deja un escaneo de puertos

Cuando ejecutas `nmap -sS` o `nmap -sT` contra una máquina, generas un patrón de tráfico muy característico y distinto del tráfico normal:

- **Muchas conexiones distintas en muy poco tiempo**, todas hacia la misma IP, cada una a un puerto diferente.
- En un **SYN scan** (`-sS`), verás muchos paquetes SYN que nunca llegan a completar el saludo de tres vías — el atacante manda SYN, la víctima responde SYN-ACK, pero el atacante nunca manda el ACK final (porque solo quería saber si el puerto respondía, no completar la conexión).
- En un **TCP connect scan** (`-sT`), sí verás conexiones completas (SYN, SYN-ACK, ACK) pero seguidas casi inmediatamente de un cierre (RST o FIN), sin que se transmita ningún dato real — porque Nmap abre y cierra la conexión solo para confirmar que el puerto existe.

Esto contrasta con tráfico normal, donde una conexión (por ejemplo, tu sesión SSH) se mantiene abierta y con datos circulando durante minutos.

## 🪜 Pasos a seguir

1. Arranca una captura con `tcpdump -i eth0 -w escaneo.pcap` en tu Kali (vas a capturar tu propio tráfico saliente, que es lo que vería un sistema de monitorización situado en la red).
2. En otra terminal, lanza `nmap -sS -p- 192.168.56.X` contra tu Metasploitable.
3. Para la captura cuando el escaneo termine y ábrela en Wireshark.
4. Usa el filtro `tcp.flags.syn==1 and tcp.flags.ack==0` para ver solo los paquetes SYN iniciales — deberías ver uno por cada puerto escaneado, todos casi al mismo tiempo.
5. Fíjate en `Statistics > Conversations` dentro de Wireshark: te resume cuántas "conversaciones" TCP distintas hubo entre las dos IPs y cuántos paquetes/bytes tuvo cada una — un escaneo se ve como decenas o cientos de conversaciones muy cortas.
6. Repite la captura pero esta vez con una sesión SSH normal en paralelo, y compara: ¿cuántas conversaciones hay, y cuánto duran?
7. En tu `writeup.md`, describe con tus propias palabras qué patrón usarías tú para distinguir "esto es un escaneo" de "esto es tráfico normal", si tuvieras que programarlo.

## 💡 Pistas si te atascas

- Si no ves el patrón esperado, revisa que el filtro de Wireshark esté bien escrito — un error de sintaxis simplemente no muestra nada, sin avisar claramente.
- `Statistics > Conversations` está en el menú superior de Wireshark una vez tienes la captura abierta.

## 🚀 Reto extra

- Prueba a lanzar el escaneo con `-T0` (el timing más lento/sigiloso de Nmap) y observa cómo cambia el patrón en el tiempo — ¿sigue siendo tan fácil de distinguir visualmente si los paquetes llegan muy espaciados?
