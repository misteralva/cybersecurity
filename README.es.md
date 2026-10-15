# 🛡️ Ciberseguridad: Laboratorios de Ataque y Defensa

[English](README.md) | 🌐 **Español**

Repositorio de laboratorios prácticos de ciberseguridad que cubren tanto **ataque** como **defensa**, en un laboratorio virtual propio y en TryHackMe. Cada laboratorio tiene su propio writeup: objetivo, entorno, pasos seguidos y qué aprendí.

> 📚 Es la continuación de mis repositorios de ejercicios de [Bash](https://github.com/misteralva/Bash) y [python](https://github.com/misteralva/python), con un formato parecido adaptado a laboratorios de seguridad.

---

## 🎯 ¿Para quién es este repositorio?

- Para mí, como diario de aprendizaje y registro de laboratorios.
- Para cualquiera que quiera ver ejercicios prácticos y documentados de ataque/defensa, en vez de solo teoría — hechos sobre VMs reales, no apuntes copiados de un curso.

---

## 🧪 Entorno de laboratorio

- **Local:** VirtualBox en mi propio PC — una VM atacante (Kali) y una o varias VM objetivo (Metasploitable, máquinas vulnerables de VulnHub/HackTheBox).
- **Remoto:** [TryHackMe](https://tryhackme.com) para salas que necesitan una infraestructura difícil de replicar en casa.
- Cada laboratorio indica qué entorno usa.

---

## 📁 Estructura

```
Cybersecurity/
├── README.md
├── README.es.md
├── 01-reconocimiento/
├── 02-explotacion/
├── 03-trafico-y-deteccion/
├── 04-hardening-y-defensa/
└── 05-proyecto-final/
```

Cada carpeta de laboratorio contiene:

| Fichero        | Contenido                                                                 |
| -------------- | -------------------------------------------------------------------------- |
| `Enunciado.md` | Objetivo, entorno necesario, conceptos, pasos a seguir y reto extra       |
| `writeup.md`   | Mi propio writeup: comandos usados, hallazgos, capturas (descritas) y lecciones aprendidas |

---

## 🗺️ Ruta de aprendizaje

### Bloque 1 · Reconocimiento y enumeración

| #  | Laboratorio                        | Enfoque                                                | Estado |
| --- | ------------------------------------ | --------------------------------------------------------- | ------ |
| 01 | [Escaneo con Nmap](01-reconocimiento/01-escaneo-nmap/Enunciado.md) | Tipos de escaneo, timing, detección de servicio/versión, evasión básica | ⏳ |
| 02 | [Enumeración de servicios](01-reconocimiento/02-enumeracion-servicios/Enunciado.md) | Enumeración de SMB, FTP y HTTP | ⏳ |
| 03 | [OSINT y DNS](01-reconocimiento/03-osint-dns/Enunciado.md) | `whois`, `dig`, enumeración de subdominios | ⏳ |

### Bloque 2 · Explotación (solo en laboratorio)

| #  | Laboratorio                        | Enfoque                                                | Estado |
| --- | ------------------------------------ | --------------------------------------------------------- | ------ |
| 04 | Recorrido por Metasploitable         | Explotar un servicio vulnerable conocido con Metasploit    | ⏳ |
| 05 | Fuerza bruta controlada              | Hydra contra un servicio de tu propio laboratorio          | ⏳ |
| 06 | Escalada de privilegios básica       | Privesc en Linux sobre una VM de laboratorio                | ⏳ |

### Bloque 3 · Análisis de tráfico y detección

| #  | Laboratorio                        | Enfoque                                                | Estado |
| --- | ------------------------------------ | --------------------------------------------------------- | ------ |
| 07 | Captura de paquetes básica            | Wireshark/tcpdump, leer una captura                         | ⏳ |
| 08 | Detectar un escaneo de puertos        | Reconocer patrones de escaneo en logs/capturas              | ⏳ |
| 09 | Fundamentos de IDS                    | Configuración mínima de Snort/Suricata y alertas             | ⏳ |
| 10 | Detección de fuerza bruta en logs     | Analizar `auth.log` para ver patrones de login fallido       | ⏳ |

### Bloque 4 · Hardening y defensa activa

| #  | Laboratorio                        | Enfoque                                                | Estado |
| --- | ------------------------------------ | --------------------------------------------------------- | ------ |
| 11 | Hardening de SSH                      | Autenticación solo por clave, cambios de puerto/política, deshabilitar root | ⏳ |
| 12 | Firewall básico                       | Reglas `ufw`/`nftables` para un servidor de laboratorio      | ⏳ |
| 13 | Fail2ban                              | Bloquear intentos de fuerza bruta automáticamente             | ⏳ |
| 14 | Auditoría de permisos y usuarios      | Revisar cuentas, sudoers, permisos de ficheros                | ⏳ |
| 15 | Política de contraseñas               | Forzar contraseñas fuertes y bloqueo de cuentas               | ⏳ |

### Bloque 5 · Proyecto final

| Laboratorio                        | Enfoque                                                | Estado |
| -------------------------------------- | ----------------------------------------------------------- | ------ |
| [Ataque vs Defensa](05-proyecto-final/proyecto-ataque-defensa/Enunciado.md) | Ciclo completo atacar → detectar → defender → comparar, writeup de antes/después | ⏳ |

**Leyenda:** ✅ hecho · ⏳ pendiente · 🔜 por detallar

---

## 🧭 Cómo usar este repositorio

1. Lee el `Enunciado.md` del laboratorio.
2. Monta el entorno que indica.
3. Resuélvelo tú mismo y toma notas mientras avanzas.
4. Escribe tu propio `writeup.md` antes de consultar el mío (cuando esté publicado).

> ⚠️ Todos los laboratorios se ejecutan contra máquinas propias o plataformas de práctica legales (TryHackMe, VulnHub, HackTheBox, Metasploitable). Nada aquí apunta a sistemas sin permiso.

---

## 👤 Autor

**David** · [@misteralva](https://github.com/misteralva) · [misteralva.github.io](https://misteralva.github.io)

Estudiante de ASIR interesado en redes, ciberseguridad y automatización.
