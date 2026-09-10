# ENCOR 350-401 — Banco de preguntas, app de práctica y contexto del proyecto

Cris se está preparando para el examen Cisco CCNP ENCOR 350-401 v1.2. Este repo
contiene el banco de preguntas, la app de práctica (publicada como Claude
Artifact) y el contexto de trabajo para que Claude Code pueda seguir sumando
contenido de forma consistente.

**App de práctica en vivo:**
- Claude Artifact (sincronizado vía Google Sheets, ver abajo): https://claude.ai/code/artifact/c3a52505-24b8-40c8-bbc8-e168455e42d5
- GitHub Pages (público, sin login): https://cristiantorvil.github.io/CCNP/

## Archivos incluidos

- `data/question_bank.json` — banco de preguntas actual (1035 preguntas,
  cobertura 49/49 subtemas del blueprint ENCOR 350-401 v1.2, distribuidas
  proporcionalmente al peso de cada dominio en el examen). Formato:
  ```json
  {
    "version": "2026-09-08_v5",
    "total": 1035,
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
  lo efectivamente respondido.
- `CONTEXTO_PROYECTO.md` — objetivos, convenciones de trabajo, y errores
  conceptuales recurrentes a reforzar al generar más preguntas.

## Cobertura (ponderado por peso de dominio)

| Dominio | Peso examen | Actual | % del banco |
|---|---|---|---|
| 1.0 Architecture | 15% | 150 | 14.5% |
| 2.0 Virtualization | 10% | 103 | 10.0% |
| 3.0 Infrastructure | 30% | 319 | 30.8% |
| 4.0 Network Assurance | 10% | 106 | 10.2% |
| 5.0 Security | 20% | 202 | 19.5% |
| 6.0 Automation & AI | 15% | 155 | 15.0% |
| **Total** | **100%** | **1035** | **100%** |

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
   2) y suavizando el lenguaje absolutista del resto (→ 3.6%). Efecto
   secundario conocido: el sesgo de longitud volvió a subir de 22.5% a ~80%
   porque ese relleno era lo que balanceaba las longitudes — pendiente una
   pasada de contenido real (no relleno) para volver a bajarlo sin
   reintroducir el problema de lenguaje absolutista.

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
