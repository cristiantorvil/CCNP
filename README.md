# ENCOR 350-401 — Banco de preguntas, app de práctica y contexto del proyecto

Cris se está preparando para el examen Cisco CCNP ENCOR 350-401 v1.2. Este repo
contiene el banco de preguntas, la app de práctica (publicada como Claude
Artifact) y el contexto de trabajo para que Claude Code pueda seguir sumando
contenido de forma consistente.

**App de práctica en vivo:** https://claude.ai/code/artifact/c3a52505-24b8-40c8-bbc8-e168455e42d5

## Archivos incluidos

- `data/question_bank.json` — banco de preguntas actual (286 preguntas,
  cobertura 49/49 subtemas del blueprint ENCOR 350-401 v1.2, mínimo 5 preguntas
  por subtema). Formato:
  ```json
  {
    "version": "2026-09-08_v5",
    "total": 286,
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

## Qué falta (meta: 1000 preguntas, ponderado por peso de dominio)

| Dominio | Peso examen | Meta preguntas | Actual | Faltan |
|---|---|---|---|---|
| 1.0 Architecture | 15% | 150 | 35 | 115 |
| 2.0 Virtualization | 10% | 100 | 45 | 55 |
| 3.0 Infrastructure | 30% | 300 | 66 | 234 |
| 4.0 Network Assurance | 10% | 100 | 30 | 70 |
| 5.0 Security | 20% | 200 | 50 | 150 |
| 6.0 Automation & AI | 15% | 150 | 60 | 90 |
| **Total** | **100%** | **1000** | **286** | **714** |

Todos los 49 subtemas tienen ahora al menos 5 preguntas (piso mínimo alcanzado
2026-09-08). Subtemas con profundidad completa (10 c/u): TrustSec/MACsec (5.4.d),
YANG (6.3), APIs Catalyst Center (6.4), RESTCONF (6.9), vSwitch/OVS (2.1.c),
VXLAN EVPN/BUM (2.3.b). El resto tiene entre 5 y 6 preguntas — el próximo paso
natural es seguir sumando volumen priorizando el dominio 3.0 Infrastructure
(mayor peso y mayor brecha).
