# Proyecto Final · Ataque vs Defensa

## 🎯 Objetivo

Este es el laboratorio que une todo el repositorio. No aprendes nada nuevo aquí — **demuestras** que lo que has hecho en los Bloques 1-4 forma un ciclo completo: reconoces, atacas, lo detectas y lo bloqueas. Es también, con diferencia, el ejercicio con más valor para enseñar en una entrevista o en un portfolio, porque muestra el "antes y después" de forma muy visual.

## 🧪 Entorno necesario

- Una VM objetivo **sin endurecer todavía** (tu Metasploitable2, o un snapshot limpio de la VM que usaste en el Bloque 4 antes de aplicar ningún cambio).
- Tu Kali, como atacante.
- Capacidad de capturar tráfico o revisar logs en el objetivo (Wireshark/tcpdump del Laboratorio 07, y acceso a `/var/log/auth.log` del Laboratorio 10).

## 📖 La idea del proyecto, explicada

Vas a elegir **un solo ataque** de los que ya hiciste en el Bloque 2 (por ejemplo, la fuerza bruta por SSH del Laboratorio 05) y vas a recorrer el ciclo completo dos veces: una contra la máquina sin proteger, y otra contra la misma máquina ya endurecida con lo del Bloque 4. La comparación entre ambas ejecuciones *es* el resultado del proyecto.

No hace falta que combines los 15 laboratorios anteriores — con uno bien documentado de principio a fin ya demuestras la idea. Si luego quieres, puedes repetir el ciclo con un segundo ataque distinto.

### Las cuatro fases de cada ronda

**Fase 1 — Ataque** (reutiliza lo del Bloque 2)
Ejecuta el ataque elegido contra la máquina tal cual está en ese momento (sin endurecer la primera vez, endurecida la segunda). Anota el resultado: ¿tuvo éxito?, ¿cuánto tardó?

**Fase 2 — Detección** (reutiliza lo del Bloque 3)
Mientras ocurre el ataque (o justo después), captura tráfico o revisa los logs, según corresponda al ataque que elegiste. Confirma si el ataque dejó rastro detectable y cuál.

**Fase 3 — Defensa** (reutiliza lo del Bloque 4)
Solo en la segunda ronda: antes de repetir el ataque, aplica el hardening correspondiente (por ejemplo, si el ataque es fuerza bruta SSH, aplica el Laboratorio 11 y/o 13).

**Fase 4 — Comparación**
Pon lado a lado ambas rondas: qué cambió en el resultado del ataque, qué cambió en lo que viste en la detección, y por qué el cambio defensivo concreto es el que marcó la diferencia (no "lo hice más seguro en general", sino "este cambio específico corta esta vía específica").

## 🪜 Pasos a seguir

1. Elige un ataque del Bloque 2 que quieras usar como hilo conductor (sugerencia: la fuerza bruta SSH del Laboratorio 05, porque es la más fácil de medir con claridad — número de intentos, tiempo, éxito/fracaso).
2. **Ronda 1 (sin defensa):** ejecuta el ataque contra la máquina sin endurecer. Captura el tráfico o recoge el log relevante. Documenta el resultado.
3. Aplica el hardening correspondiente del Bloque 4 a esa misma máquina.
4. **Ronda 2 (con defensa):** repite exactamente el mismo ataque, de la misma forma, contra la máquina ya endurecida. Vuelve a capturar/revisar logs. Documenta el resultado.
5. Escribe el `writeup.md` de este laboratorio como un informe con cuatro secciones claras: **Resumen** (qué atacaste y qué defendiste, en 2-3 frases), **Ronda 1 — sin defensa** (qué hiciste y qué pasó), **Ronda 2 — con defensa** (qué cambiaste y qué pasó), **Conclusión** (qué control defensivo concreto marcó la diferencia y por qué).

## 💡 Pistas si te atascas

- No te compliques combinando varios ataques a la vez para tu primera versión del proyecto — un ciclo completo y bien documentado vale mucho más que cinco a medias.
- Si tienes capturas de pantalla o salidas de terminal relevantes, inclúyelas (o descríbelas con detalle) en el writeup — es lo que hace que un informe de este tipo sea convincente para quien lo lea.

## 🚀 Reto extra

- Repite el ciclo completo con un segundo ataque distinto (por ejemplo, el escaneo de puertos del Laboratorio 01/08, defendido con el firewall del Laboratorio 12), para tener dos ejemplos de ataque/defensa documentados en vez de uno.
- Una vez tengas uno o dos de estos escritos, considera convertir el mejor en una entrada de blog o publicación en LinkedIn (sin datos sensibles de tu entorno real) — es exactamente el tipo de contenido que suele llamar la atención en procesos de selección para puestos de seguridad.
