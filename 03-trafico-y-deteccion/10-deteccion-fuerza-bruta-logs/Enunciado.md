# Laboratorio 10 · Detectar fuerza bruta analizando logs

## 🎯 Objetivo

Cerrar el círculo del ataque de fuerza bruta del Laboratorio 05: ahora vas a ponerte del lado de la víctima y analizar sus logs para confirmar que sufrió un ataque, aunque no hubieras capturado tráfico de red en el momento. Es exactamente lo que haría un analista investigando un incidente después de que ha pasado.

## 🧪 Entorno necesario

- Tu Metasploitable2 (la víctima), con acceso por SSH a su propio sistema de ficheros (puedes usar la cuenta `msfadmin` que ya conoces).
- Haber ejecutado el Laboratorio 05 contra ella (o repetirlo ahora), para tener entradas reales en el log que analizar.

## 📖 Dónde quedan registrados los intentos de login

En la mayoría de distribuciones Linux basadas en Debian/Ubuntu (como Metasploitable), los intentos de autenticación —exitosos y fallidos— quedan registrados en:

```
/var/log/auth.log
```

Cada línea de este fichero es un evento con fecha, hora, el proceso que lo generó (`sshd` para SSH) y un mensaje. Un intento fallido de SSH se ve parecido a:

```
Jan 10 14:23:01 metasploitable sshd[1234]: Failed password for msfadmin from 192.168.56.10 port 54321 ssh2
```

Y uno exitoso:

```
Jan 10 14:23:05 metasploitable sshd[1240]: Accepted password for msfadmin from 192.168.56.10 port 54322 ssh2
```

### Comandos para analizar el log

```bash
grep "Failed password" /var/log/auth.log
```
- `grep`: busca líneas que contengan ese texto exacto. Te da todos los intentos fallidos, uno por línea.

```bash
grep "Failed password" /var/log/auth.log | wc -l
```
- `wc -l`: cuenta cuántas líneas ha devuelto el `grep` anterior — es decir, cuántos intentos fallidos hubo en total.

```bash
grep "Failed password" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -nr
```
Este comando encadena varias herramientas, cada una haciendo una cosa pequeña:
- `awk '{print $11}'`: de cada línea, imprime solo la columna número 11 (que en este formato de log es la IP de origen — cuenta las palabras separadas por espacio en una línea de ejemplo para confirmarlo en tu caso, el número de columna puede variar ligeramente).
- `sort`: ordena esas IPs alfabéticamente (necesario para que el siguiente paso funcione).
- `uniq -c`: agrupa líneas repetidas consecutivas y cuenta cuántas veces aparece cada una.
- `sort -nr`: ordena el resultado de mayor a menor según ese contador.
- El resultado final: una lista de IPs ordenada por cuántos intentos fallidos generó cada una — si una IP aparece con un número muy alto en muy poco tiempo, es un indicio fuerte de fuerza bruta.

```bash
grep "Accepted password" /var/log/auth.log
```
- Para ver si, después de muchos intentos fallidos, hubo un login exitoso — la señal más clara de que el ataque tuvo éxito.

## 🪜 Pasos a seguir

1. Si no lo hiciste ya, repite el Laboratorio 05 contra tu Metasploitable para generar entradas reales en el log.
2. Conéctate a Metasploitable y revisa `/var/log/auth.log` con `grep "Failed password"`.
3. Cuenta cuántos intentos fallidos hubo y en qué rango de tiempo (mira la primera y la última marca de hora).
4. Usa el comando encadenado de arriba para confirmar desde qué IP vinieron los intentos.
5. Busca si hubo un `Accepted password` justo después de la ráfaga de fallos — eso confirma que el ataque tuvo éxito.
6. En tu `writeup.md`, redacta un mini informe de incidente: qué encontraste, desde cuándo hasta cuándo, cuántos intentos, desde qué IP, y si el atacante consiguió entrar.

## 💡 Pistas si te atascas

- Si no tienes permisos para leer `/var/log/auth.log` directamente, necesitarás `sudo cat` o `sudo grep`.
- El número de columna exacto en `awk '{print $11}'` depende del formato concreto del log en tu sistema — si el resultado no parece una IP, cuenta las columnas a mano en una línea de ejemplo y ajusta el número.

## 🚀 Reto extra

- Investiga qué es **Fail2ban** (lo vas a configurar de verdad en el Laboratorio 13) y cómo hace exactamente esto mismo que acabas de hacer a mano, pero de forma automática y en tiempo real, bloqueando la IP tras X intentos fallidos.
