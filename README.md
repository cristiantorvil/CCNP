# ENCOR 350-401 — Banco de preguntas, app de práctica y contexto del proyecto

Cris se está preparando para el examen Cisco CCNP ENCOR 350-401 v1.2. Este repo
contiene el banco de preguntas, la app de práctica (publicada como Claude
Artifact) y el contexto de trabajo para que Claude Code pueda seguir sumando
contenido de forma consistente.

**App de práctica en vivo:** https://claude.ai/code/artifact/c3a52505-24b8-40c8-bbc8-e168455e42d5

## Archivos incluidos

- `data/question_bank.json` — banco de preguntas actual (1000 preguntas,
  cobertura 49/49 subtemas del blueprint ENCOR 350-401 v1.2, distribuidas
  proporcionalmente al peso de cada dominio en el examen). Formato:
  ```json
  {
    "version": "2026-09-08_v5",
    "total": 1000,
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
- `app/index.html` — app de práctica de una sola página, publicada como Claude
  Artifact. El banco de preguntas va embebido directamente en el HTML, así que
  **cada vez que se agregan preguntas a `data/question_bank.json` hay que
  reconstruir este archivo y republicarlo** (mismo link, se actualiza solo).
  El progreso (qué preguntas se respondieron bien/mal) se guarda en la base de
  datos propia del Artifact, sincronizada entre dispositivos; el % por subtema
  y dominio es "preguntas acertadas alguna vez / total de preguntas que existen
  hoy para ese subtema en el banco" — no una ventana de respuestas recientes
  como el tracker de Drive, porque acá el banco es grande y fijo, no rota.
- `CONTEXTO_PROYECTO.md` — objetivos, convenciones de trabajo, y errores
  conceptuales recurrentes a reforzar al generar más preguntas.

## Cobertura (meta: 1000 preguntas, ponderado por peso de dominio) — completa

| Dominio | Peso examen | Meta preguntas | Actual | % del banco |
|---|---|---|---|---|
| 1.0 Architecture | 15% | 150 | 150 | 15.0% |
| 2.0 Virtualization | 10% | 100 | 101 | 10.1% |
| 3.0 Infrastructure | 30% | 300 | 292 | 29.2% |
| 4.0 Network Assurance | 10% | 100 | 106 | 10.6% |
| 5.0 Security | 20% | 200 | 200 | 20.0% |
| 6.0 Automation & AI | 15% | 150 | 151 | 15.1% |
| **Total** | **100%** | **1000** | **1000** | **100%** |

Meta de 1000 preguntas alcanzada 2026-09-08, con distribución por dominio
dentro de ±0.8 puntos porcentuales del peso oficial del blueprint. Todos los
49 subtemas tienen cobertura proporcional a su dominio.

**Corrección de sesgos (2026-09-08):** el contenido nuevo se auditó y corrigió
para dos sesgos de "test-wiseness" que hacían las preguntas resolubles sin
saber la materia: (1) sesgo de posición — la respuesta correcta se
distribuía de forma dispareja entre A/B/C/D (originalmente A 61%/B 33%/C 6%/D
0%); se corrigió con un shuffle determinístico de las opciones en todo el
banco. (2) sesgo de longitud — la opción correcta tendía a ser sistemáticamente
la más larga (originalmente 88.5% de las preguntas); se corrigió elaborando
distractores para igualar su longitud. Métrica final bancowide: 22.9% de las
preguntas tienen la opción correcta como la más larga (el azar puro con 4
opciones da 25%), y las posiciones A/B/C/D quedaron en 29.7%/24.5%/23.3%/22.5%.
