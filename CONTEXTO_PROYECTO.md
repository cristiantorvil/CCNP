# Contexto del proyecto — CCNP ENCOR 350-401

## Objetivo
Cris se está preparando para el examen Cisco CCNP ENCOR 350-401 v1.2.
Puntaje objetivo: 82.5% (825/1000 puntos).
Enfoque estrictamente sin dumps — las preguntas se generan a partir del
blueprint oficial para construir comprensión real, no memorización.

## Pesos de dominio del examen (blueprint oficial v1.2)
- 1.0 Architecture — 15%
- 2.0 Virtualization — 10%
- 3.0 Infrastructure — 30%
- 4.0 Network Assurance — 10%
- 5.0 Security — 20%
- 6.0 Automation and Artificial Intelligence — 15%

Se trackean los 49 subtemas individuales del blueprint (ver el PDF oficial
"350-401ENCORv1_2.pdf" si está disponible, o el índice de códigos usado en
`question_bank.json`, campo `subtopic`).

## Tracking de progreso (separado del banco de preguntas)
- Fuente de verdad: Google Drive, carpeta "ENCOR CCNP Tracker"
  (folder ID `1N46yy0l46OLwA96rTFrsD65ZzUETuI91`)
- Archivo activo: `ENCOR_progress_2026-07-29_consolidado`
- El tracker usa una ventana deslizante de 15 preguntas por subtema — solo
  las últimas 15 respondidas cuentan para el porcentaje de ese subtema
- Los porcentajes de dominio se calculan sumando correctas/total de todos
  los subtemas del dominio, NO promediando los porcentajes de cada subtema
- Estado más reciente conocido (2026-09-07, re-verificar antes de confiar):
  ~77.8% global, brecha de ~4.7% contra la meta
- Subtemas prioritarios (todos cerca del 60%): TrustSec/MACsec (5.4.d),
  YANG (6.3), APIs Catalyst Center (6.4), RESTCONF (6.9), vSwitch/OVS (2.1.c),
  VXLAN EVPN/BUM (2.3.b)

**Nota:** el banco de preguntas de este paquete (`question_bank.json`) es un
proyecto NUEVO y separado del tracker de progreso — una base de datos grande
de preguntas (meta: 1000), pensada para servir preguntas sin repetición desde
una app, no para reemplazar el sistema de tracking por ventana deslizante que
ya existe en Drive.

## Errores conceptuales recurrentes a reforzar
Estos son errores que Cris ha cometido más de una vez — vale la pena que
las preguntas nuevas sigan poniendo el foco ahí:

- EF (no AF31/CS3) es el marcado DSCP correcto para voz
- AH vs. ESP: AH autentica sin cifrar; ESP hace ambas cosas
- BGP EVPN es el mecanismo de mapeo MAC-a-VTEP en VXLAN
- Distinción YANG `list` vs. `leaf-list`
- Definición de flow record en Flexible NetFlow
- MACsec es hop-by-hop, no end-to-end
- Estructura de URL base de RESTCONF: `/restconf/data/<módulo>:<recurso>` (RFC 8040)
- BGP Weight se evalúa antes que Local Preference
- JSON requiere comillas dobles, no simples
- HTTP 201 Created (no 200 OK) se retorna al crear un recurso
- Sintaxis EEM para triggers de CPU/CLI
- OSPFv3: la adyacencia usa direcciones link-local; la topología está
  separada de la información de prefijos en el diseño de LSA

## Convenciones de trabajo con Claude (para continuidad si se retoma acá)
- Preguntas en inglés, estilo examen, 4 opciones
- Explicaciones en español después de cada respuesta
- Sesiones típicas de 10 preguntas (a veces 5 por subtema)
- Prioridad: subtemas más débiles (<70%) reciben más volumen
- Cris corrige errores de cálculo activamente — aceptar y ajustar de inmediato
- Respuestas concisas, sin tablas largas salvo que se esté explicando un concepto

## Limitaciones de herramientas conocidas
- El conector de Google Drive (`create_file`) falla intermitentemente con
  "No approval received" — es un problema del conector, no de contenido/tamaño
- No hay acceso de escritura a GitHub desde este entorno — de ahí el traspaso
  a Claude Code, que sí puede hacer `git push` con las credenciales locales
