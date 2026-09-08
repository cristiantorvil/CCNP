# ENCOR 350-401 — Banco de preguntas y contexto del proyecto

Este paquete trae todo lo necesario para que Claude Code continúe el trabajo:
subir el banco de preguntas a un repo de GitHub, versionarlo, y (si se quiere)
retomar la app de práctica.

## Archivos incluidos

- `ENCOR_question_bank_2026-09-07.json` — banco de preguntas actual (215 preguntas,
  cobertura 49/49 subtemas del blueprint ENCOR 350-401 v1.2). Formato:
  ```json
  {
    "version": "2026-09-07_v3",
    "total": 215,
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
- `CONTEXTO_PROYECTO.md` — objetivos, convenciones de trabajo, y errores
  conceptuales recurrentes a reforzar al generar más preguntas.

## Qué falta (meta: 1000 preguntas, ponderado por peso de dominio)

| Dominio | Peso examen | Meta preguntas | Actual | Faltan |
|---|---|---|---|---|
| 1.0 Architecture | 15% | 150 | 32 | 118 |
| 2.0 Virtualization | 10% | 100 | 35 | 65 |
| 3.0 Infrastructure | 30% | 300 | 46 | 254 |
| 4.0 Network Assurance | 10% | 100 | 19 | 81 |
| 5.0 Security | 20% | 200 | 33 | 167 |
| 6.0 Automation & AI | 15% | 150 | 50 | 100 |
| **Total** | **100%** | **1000** | **215** | **785** |

Subtemas ya con profundidad completa (10 preguntas c/u): TrustSec/MACsec (5.4.d),
YANG (6.3), APIs Catalyst Center (6.4), RESTCONF (6.9), vSwitch/OVS (2.1.c),
VXLAN EVPN/BUM (2.3.b). El resto de los 49 subtemas tiene entre 2 y 6 preguntas
como semilla inicial.

## Sugerencia de estructura de repo

```
encor-350-401/
├── README.md
├── data/
│   └── question_bank.json
└── app/
    └── (la app HTML de práctica, si se retoma)
```
