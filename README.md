# ENCOR 350-401 — Banco de preguntas, app de práctica y contexto del proyecto

Cris se está preparando para el examen Cisco CCNP ENCOR 350-401 v1.2. Este repo
contiene el banco de preguntas, la app de práctica (publicada como Claude
Artifact) y el contexto de trabajo para que Claude Code pueda seguir sumando
contenido de forma consistente.

**App de práctica en vivo:**
- Claude Artifact (sincronizado vía Google Sheets, ver abajo): https://claude.ai/code/artifact/c3a52505-24b8-40c8-bbc8-e168455e42d5
- GitHub Pages (público, sin login): https://cristiantorvil.github.io/CCNP/

## Archivos incluidos

- `data/question_bank.json` — banco de preguntas actual (1056 preguntas,
  cobertura 49/49 subtemas del blueprint ENCOR 350-401 v1.2, distribuidas
  proporcionalmente al peso de cada dominio en el examen). Formato:
  ```json
  {
    "version": "2026-09-12_v12",
    "total": 1093,
    "questions": [
      {
        "id": "trustsec_01",
        "subtopic": "5.4.d",
        "name": "TrustSec/MACsec",
        "question": "... (inglés, estilo examen)",
        "options": {"A": "...", "B": "...", "C": "...", "D": "..."},
        "correct": "B",
        "explanation_es": "... (explicación en español)"
      }
    ]
  }
  ```
  Dos campos opcionales extienden el formato: `source` (string, ej.
  `"ipcisco.com"`) marca preguntas adaptadas de una fuente externa —
  reescritas en palabras propias, nunca copiadas literalmente — y visibles
  como un badge en la app; `correctSet` (array de letras, ej. `["A","D"]`,
  reemplaza a `correct`) marca preguntas de selección múltiple ("select two/
  three"), el formato real que usa Cisco además de opción única.
- `app/index.html` (Artifact) e `index.html` (raíz, GitHub Pages) — misma app,
  dos despliegues. El banco va embebido directamente en el HTML, así que
  **cada vez que se agregan preguntas a `data/question_bank.json` hay que
  reconstruir AMBOS archivos y republicar/pushear**. El progreso se guarda en
  una Google Sheet propia (vía un Apps Script Web App desplegado por el
  usuario), compartida entre las dos apps — no en la base de datos propia del
  Artifact ni en localStorage, así que el progreso es el mismo sin importar
  desde cuál de las dos URLs se practique. El % por subtema y dominio es
  "preguntas acertadas alguna vez / total de preguntas que existen hoy para
  ese subtema en el banco" (cobertura), mostrado junto a la precisión sobre
  lo efectivamente respondido. Después de responder, se puede calificar la
  pregunta de 1 a 5 estrellas (guardado en la misma Sheet, pestaña
  "ratings") para ir identificando preguntas mal planteadas con el uso real.
- `CONTEXTO_PROYECTO.md` — objetivos, convenciones de trabajo, y errores
  conceptuales recurrentes a reforzar al generar más preguntas.

## Cobertura (ponderado por peso de dominio)

| Dominio | Peso examen | Actual | % del banco |
|---|---|---|---|
| 1.0 Architecture | 15% | 158 | 14.5% |
| 2.0 Virtualization | 10% | 111 | 10.2% |
| 3.0 Infrastructure | 30% | 340 | 31.1% |
| 4.0 Network Assurance | 10% | 111 | 10.2% |
| 5.0 Security | 20% | 202 | 18.5% |
| 6.0 Automation & AI | 15% | 171 | 15.6% |
| **Total** | **100%** | **1093** | **100%** |

Meta base de 1000 preguntas alcanzada 2026-09-08, con distribución por
dominio dentro de ±0.8 puntos porcentuales del peso oficial del blueprint.
Todos los 49 subtemas tienen cobertura proporcional a su dominio. Se
agregaron 35 preguntas adicionales el 2026-09-10 (ver abajo).

**Corrección de sesgos (2026-09-08 y 2026-09-10):** se auditó y corrigió el
banco para tres tipos de "test-wiseness" que hacían las preguntas resolubles
sin saber la materia:
1. **Sesgo de posición** — la respuesta correcta se distribuía de forma
   dispareja entre A/B/C/D (originalmente A 61%/B 33%/C 6%/D 0%); corregido
   con shuffle determinístico de las opciones en todo el banco.
2. **Sesgo de longitud** — la opción correcta tendía a ser sistemáticamente
   la más larga (originalmente 88.5% de las preguntas); corregido elaborando
   distractores para igualar su longitud.
3. **Lenguaje absolutista ("obviamente falso")** — reportado por el usuario:
   muchas opciones incorrectas usaban lenguaje tipo "exclusivamente", "no
   tiene relación alguna", "bajo ninguna circunstancia", identificable como
   falso sin conocer la materia (54.9% de las preguntas). Se corrigió
   eliminando ~1300 oraciones de relleno sin contenido (usadas para el punto
   2) y suavizando el lenguaje absolutista del resto (→ 0%, ver punto 4).
4. **Regresión del sesgo de longitud (2026-09-12)** — el usuario probó 10
   preguntas del dominio 1.0 eligiendo siempre la opción más larga y sacó
   10/10, confirmando que quitar el relleno del punto 3 había vuelto a subir
   el sesgo de longitud a 84% en ese dominio (80% bancowide). Se corrigió
   de fondo esta vez: 8 agentes en paralelo reescribieron ~546 distractores
   (uno por dominio, ~75% de las preguntas con sesgo en cada uno) agregando
   detalle técnico específico y genuino — no relleno genérico ni lenguaje
   absolutista — hasta igualar o superar la longitud de la respuesta
   correcta. Resultado final, medido por dominio: los 6 dominios quedaron
   entre 25.2% y 25.8% de "correcta = más larga" (el azar puro da 25%), y
   0 frases con lenguaje absolutista en todo el banco.
5. **Regresión del sesgo de posición (2026-09-12):** al revisar el banco de
   nuevo (ya en 1056 preguntas, tras varias tandas agregadas desde el fix de
   posición original en 286 preguntas) se encontró que el sesgo de posición
   había vuelto a aparecer, concentrado en los dominios 1.0/3.0/4.0 (A
   sobrerrepresentada 34-37% en vez de 25%) — el shuffle de 2026-09-08 nunca
   se reaplicó a las tandas agregadas después. Corregido con un shuffle
   determinístico (seed fija) sobre las 4 opciones de las 1056 preguntas del
   banco completo, remapeando `correct`/`correctSet` sin tocar ningún texto.
   Resultado: A/B/C/D quedaron entre 19% y 31% por dominio (antes hasta 37%/0%
   en algunos), y 24.4%/25.5%/23.8%/26.3% a nivel banco completo.

**Preguntas de fuente externa (2026-09-10):** 35 preguntas fueron adaptadas
de dos quizzes de ipcisco.com que el usuario compartió, reescritas en
palabras propias (nunca copiadas literalmente) y corrigiendo ~8 errores
detectados en el material original (comandos incorrectos, etiquetas
IPv4/IPv6 confundidas, opciones con más de una respuesta verdadera en
preguntas de opción única). Quedan marcadas con `"source": "ipcisco.com"` y
visibles como badge en la app. Se descartaron ~8 preguntas del material
original por no alinear con el blueprint de ENCOR 350-401 (RIP/RIPng no es
examinable, trivia de subnetting/EUI-64 es prerrequisito CCNA) o por errores
irrecuperables en el enunciado.

**Preguntas del libro Cisco Press OCG (2026-09-12, en curso):** se está
extrayendo contenido de *CCNP and CCIE Enterprise Core ENCOR 350-401 Official
Cert Guide, 2nd Ed (2023)* (Edgeworth/Garza Rios/Hucaby/Gooley), a partir de
las quizzes "Do I Know This Already?" de cada capítulo (el libro no incluye
"Review Questions" impresas por capítulo en esta edición; solo quedaron
online). Mismo criterio que con ipcisco.com: preguntas reescritas en palabras
propias (nunca copiadas del libro), verificadas contra conocimiento propio de
ENCOR (no solo contra la respuesta del libro), y con sesgo de posición/
longitud corregido en la misma tanda en que se agregan, no como parche
posterior. Se decidió con el usuario no cubrir los 5 capítulos de Wireless
(17-21) porque ninguno de los 49 subtemas trackeados (acá y en el tracker de
Drive) tiene código para wireless, y se descarta el Capítulo 1 ("Packet
Forwarding": dominios de colisión/broadcast, CEF) por ser contenido de nivel
CCNA sin subtema de blueprint asociado — mismo criterio ya aplicado antes con
RIP/EUI-64. Primer lote: 21 preguntas de los capítulos 2-5 (STP/RSTP/MST,
VTP/DTP, EtherChannel — subtemas 3.1.a/b/c), marcadas con `"source": "Cisco
Press ENCOR OCG 2nd Ed (2023)"`.

**Preguntas del libro Exam Cram de Bacha (2026-09-12):** segunda fuente,
*CCNP and CCIE Enterprise Core ENCOR 350-401 Exam Cram* (Bacha, Pearson
2022) — 32 capítulos organizados directamente por dominio del blueprint.
Extraídas sus secciones "Cram Quiz" (una por sección, con respuesta y
justificación inmediatamente después) y "Review Questions" de fin de
capítulo, filtrando el resto del texto narrativo antes de leerlas. Mismo
criterio de siempre: reescritas en palabras propias, verificadas contra
conocimiento propio de ENCOR, chequeadas contra el banco existente para
evitar duplicar hechos ya cubiertos (varios temas de este libro — LISP/VXLAN,
VRF-lite, AH/NAT, SPAN/RSPAN/ERSPAN, NetFlow, IP SLA responder — resultaron
ya estar muy cubiertos por tandas anteriores y se descartaron por redundantes
en vez de agregarse). Se agregaron 47 preguntas nuevas en 5 tandas cubriendo:
Automatización (Python, JSON/XML, YANG, DNA Center APIs, códigos REST, EEM,
orquestación agent/agentless — dominio 6.0), Arquitectura (SD-WAN, SD-Access,
QoS — dominio 1.0), Virtualización (hypervisors, vSwitch, FlexVPN/NAT-T —
dominio 2.0) y Network Assurance (SNMPv3, traceroute, DNA Center Assurance —
dominio 4.0). Se descartó el Capítulo 21 (cloud IaaS/PaaS/SaaS) por el mismo
motivo que Wireless: sin subtema de blueprint asociado en el esquema de 49
subtemas. Marcadas con `"source": "CCNP ENCOR 350-401 Exam Cram (Bacha,
2022)"`. Pendiente de este libro: capítulos 2-4 (IGP/BGP/IP Services), 6-11
(Seguridad), 25 (Switching, alto riesgo de duplicar con OCG).

**Regresión del sesgo de posición, otra vez (2026-09-12):** cada tanda nueva
agregada en sesión reintroduce sesgo de posición local (ej. la primera tanda
de este libro quedó 13 B / 3 D sobre 32) porque se escribe more rápido de lo
que se verifica. Se corrigió reaplicando el shuffle determinístico de
2026-09-12 (ver punto 5 arriba) sobre el banco completo (ahora 1093
preguntas) después de cada tanda. **Nota para el futuro:** re-ejecutar este
shuffle bank-wide después de agregar cualquier tanda nueva, no asumir que
escribir con "buenas intenciones" de variar la posición alcanza.
