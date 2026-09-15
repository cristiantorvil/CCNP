# ENCOR 350-401 — Banco de preguntas, app de práctica y contexto del proyecto

Cris se está preparando para el examen Cisco CCNP ENCOR 350-401 v1.2. Este repo
contiene el banco de preguntas, la app de práctica (publicada como Claude
Artifact) y el contexto de trabajo para que Claude Code pueda seguir sumando
contenido de forma consistente.

**App de práctica en vivo:**
- Claude Artifact (sincronizado vía Google Sheets, ver abajo): https://claude.ai/code/artifact/c3a52505-24b8-40c8-bbc8-e168455e42d5
- GitHub Pages (público, sin login): https://cristiantorvil.github.io/CCNP/

## Archivos incluidos

- `data/question_bank.json` — banco de preguntas actual (1424 preguntas,
  cobertura 49/49 subtemas del blueprint ENCOR 350-401 v1.2, distribuidas
  proporcionalmente al peso de cada dominio en el examen). Formato:
  ```json
  {
    "version": "2026-09-14_v27",
    "total": 1424,
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
  three"), el formato real que usa Cisco además de opción única. El objeto
  `options` no está limitado a 4 llaves: preguntas verdadero/falso usan solo
  `{"A":..., "B":...}` y algunas de selección múltiple usan hasta 6
  (`A`-`F`), reflejando los formatos reales que aparecen en el examen. La
  app renderiza dinámicamente el número de opciones que tenga cada pregunta
  (ver `renderQuestion`/`applyLockedState` en el JS), no asume siempre 4.
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
| 1.0 Architecture | 15% | 211 | 14.8% |
| 2.0 Virtualization | 10% | 146 | 10.3% |
| 3.0 Infrastructure | 30% | 427 | 30.0% |
| 4.0 Network Assurance | 10% | 158 | 11.1% |
| 5.0 Security | 20% | 253 | 17.8% |
| 6.0 Automation & AI | 15% | 229 | 16.1% |
| **Total** | **100%** | **1424** | **100%** |

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

**Extracción completa de ambos libros (2026-09-12):** a pedido explícito del
usuario ("agrega todas las preguntas de ambos libros"), se procesaron todos
los capítulos restantes de ambos libros que no se habían tocado aún —
excluyendo siempre Wireless (sin subtema trackeado en este proyecto) y los
capítulos sin subtema de blueprint asociado (OCG "Packet Forwarding",
Exam Cram "On-Premises vs. Cloud" y "Switching" a nivel CCNA). Esto incluyó
capítulos ya cubiertos parcialmente antes (por ejemplo, EIGRP/OSPF/BGP,
seguridad, virtualización), agregando preguntas adicionales aunque el tema ya
tuviera cobertura — decisión explícita del usuario de priorizar completitud
por encima de evitar solapamiento temático. Se usaron 12 agentes en paralelo
(uno por área temática: IGP, BGP, Multicast, QoS, IP Services, Overlay/VRF/
LISP/VXLAN, Arquitectura/SD-WAN/SD-Access, Network Assurance, Seguridad,
Virtualización, y dos de Automatización) para extraer y reescribir cada
pregunta de las secciones "Do I Know This Already?"/"Cram Quiz"/"Review
Questions" de los capítulos asignados, cada uno hacia el mismo criterio de
siempre (reescritura propia, verificación contra conocimiento real de ENCOR,
nunca copiar texto del libro). Se agregaron 376 preguntas nuevas (1093 →
1469). Control de calidad post-extracción antes del merge:
- Verificación de esquema (4 opciones A-D exactas, `correct`/`correctSet`
  válido) sobre las 376 preguntas; se encontraron y corrigieron 22 preguntas
  mal formadas (14 de tipo verdadero/falso con solo 2 opciones en vez de 4, y
  6 preguntas de selección múltiple con 5-6 opciones en vez de 4) generadas
  por los agentes pese a la instrucción explícita del formato.
- Verificación de tildes en español: 2 de los 12 lotes (Multicast y
  Overlay/VRF/GRE/LISP/VXLAN) resultaron con texto en español sistemáticamente
  sin acentuar; se corrigieron manualmente todas las palabras afectadas.
- Sin colisiones de ID contra el banco existente ni duplicados internos.
- Se reaplicó el shuffle determinístico de posición (seed fija) sobre el
  banco completo (1469 preguntas) tras el merge, quedando A 26.7%/B 26.0%/
  C 23.0%/D 24.3% a nivel banco completo.

**Eliminación de preguntas mal calificadas (2026-09-13):** el usuario
calificó 144 preguntas con el sistema de 1-5 estrellas de la app (guardado
en la Google Sheet, pestaña "ratings"). Se revisaron las 20 con 1-2
estrellas: en su mayoría venían del lote `b6_` (OCG, cargado antes del
tercer pase de corrección de sesgo de longitud/lenguaje absolutista del
2026-09-12) y mantenían el patrón que se suponía ya corregido — respuesta
correcta visiblemente más larga y distractores con frases tipo "mistakenly
assumed"/"absolutely identical in every respect". El usuario pidió
eliminarlas directamente en vez de reescribirlas. Banco: 1469 → 1449.
**Regla de calificaciones ajustada otra vez, tercera ronda (2026-09-14):**
al revisar una tercera tanda de calificaciones nuevas (6 de 1★, 25 de 2★,
todas del lote `b6_` de nuevo), el usuario refinó la regla una vez más:
**1★ se elimina** (sin cambios), pero **2★ ahora también se elimina en
vez de reescribirse** — con la salvedad de que **la mitad de las
preguntas eliminadas se reemplazan** por preguntas nuevas, escritas en un
formato más parecido al de las preguntas de fuente externa (OCG/Exam
Cram/ipcisco.com): directas, sin el estilo "meta-comentario" verboso
característico del lote `banco propio (IA)` (frases tipo "a
misconception that overlooks..." o "since X is mistakenly assumed to...")
que ha sido la causa raíz de los sesgos de longitud y lenguaje absolutista
detectados repetidamente en ese lote. Las preguntas nuevas no llevan
`source` (no vienen de un libro real), pero sí siguen la disciplina de
redacción directa/concisa de esas fuentes. Se eliminaron las 6+25=31
preguntas de baja calificación y se agregaron 12 nuevas (IDs `r3_*`) en
subtemas variados (CoPP, AAA, NGFW/AVC, ACLs, seguridad REST API, eBGP,
EtherChannel, Multicast, APIs Catalyst Center, diseño de red, QoS, VRF).
Banco: 1464 → 1445. **Regla permanente actualizada:** 1★ = eliminar; 2★ =
eliminar, reemplazando aproximadamente la mitad por preguntas nuevas de
estilo directo/conciso (no reescribir en el lugar como antes).

**Aplicación retroactiva de la regla actual a las 42 preguntas de 2★ de
las rondas 1 y 2 (2026-09-14):** las 18 preguntas de la primera ronda y
las 24 de la segunda habían sido *reescritas en el lugar* bajo la regla
anterior ("2★ = reescribir"), antes de que el usuario la corrigiera por
última vez a "2★ = eliminar + reemplazar la mitad". Para que las 67
preguntas de 2★ tratadas en total durante el proyecto sigan una sola
regla consistente, se eliminaron esas 42 y se agregaron 21 preguntas
nuevas (IDs `r4_*`) en el mismo estilo directo/conciso, cubriendo una
muestra representativa de los subtemas afectados (AAA, CoPP, threat
defense, NAC/ISE, diagnóstico, diseño de red, vSwitch, Catalyst Center,
alta disponibilidad, NTP/PTP, NAT/PAT, policy-based routing, multicast,
EEM, líneas/autenticación local, NETCONF/RESTCONF, trunking 802.1Q,
Flexible NetFlow, IP SLA, SPAN/RSPAN/ERSPAN, APIs Catalyst Center).
Banco: 1445 → 1424. Se reaplicó el shuffle determinístico de posición
sobre el banco completo tras el cambio (A 26.6%/B 24.2%/C 25.1%/D
24.2% a nivel banco completo, sobre las 1362 preguntas de respuesta
única; `correctSet` de selección múltiple excluido de este conteo por
letra) y se verificaron manualmente los
valores de cobertura/precisión (correctas/respondidas/total) a nivel de
subtema, dominio y total general contra un cálculo independiente en
Python sobre los datos reales de la Google Sheet — coinciden
exactamente con lo que muestra la app en vivo, y la suma de los
subtemas de cada dominio cuadra con el total de ese dominio, así como
la suma de los 6 dominios cuadra con el total general (493/587/1424).

**Nota pendiente (sin resolver):** dos registros de prueba
(`__test__`, `__browser_test__`) quedaron guardados en la Google Sheet
de intentos bajo el subtema 1.1.a, inflando en +2/+2 sus valores de
`correct`/`answered` (impacto mínimo, ~1 punto porcentual en ese
subtema). No se limpiaron en este pase por ser una edición directa
sobre datos de la Sheet, fuera del alcance de esta tarea.

**Limpieza masiva de lenguaje absolutista, todo el banco (2026-09-13):** a
pedido explícito del usuario ("revisa todo el banco para eliminar esas
respuestas absolutistas que obviamente no son posibles"), se auditó el
banco completo (1464 preguntas) con un detector de frases tipo "no
relationship at all", "little relationship", "exclusively", "the exact
same", "always automatically", "purely cosmetic", "mistakenly assumed",
"the reverse of", "does not exist", "fundamentally incapable", "in every
respect/case/scenario", entre otras — el mismo patrón de "obviamente
falso por el tono" que ya se había atacado antes (ver punto 3 del
2026-09-08) pero que había reaparecido con fuerza en un lote específico.
Resultado de la auditoría: **517 de 1464 preguntas (35.3%)** tenían al
menos un distractor con este problema, concentradas casi enteramente en
el lote `b6_` (461 de 709 preguntas de ese lote, 65%) — el lote más
grande y más antiguo del banco, generado antes de que esta disciplina
estuviera bien establecida en el proyecto.

Se procesó con **9 agentes en paralelo**, cada uno recibiendo ~58
preguntas con sus distractores problemáticos ya identificados
automáticamente, con instrucción de reescribir *solo* esas opciones para
que sigan siendo incorrectas pero por un motivo técnico específico y
verosímil (no por su tono), sin tocar la pregunta, la respuesta correcta,
ni las demás opciones. Cada salida se validó automáticamente antes de
fusionar: mismo set de IDs y de llaves de opciones que el archivo
original, ninguna opción no marcada fue alterada, y un re-escaneo
confirmó que el lenguaje absolutista había desaparecido de las opciones
reescritas (con algunos falsos positivos del detector revisados a mano —
frases como "exclusively" o "the exact same" usadas de forma natural
dentro de una afirmación técnica específica, no como descarte vacío).
Resultado final: **23 de 1464 (1.6%)** siguen coincidiendo con el
detector, todas revisadas manualmente y aceptadas como afirmaciones
técnicas específicas (aunque incorrectas), no como descartes obvios.

**Nota pendiente:** el sesgo de longitud bank-wide subió a 37.8%
"correcta = más larga" (4 opciones) tras esta limpieza, por encima del
~25% ideal — probablemente porque varios agentes alargaron distractores
que habían quedado demasiado cortos tras quitarles el lenguaje
absolutista, sin siempre alargar también la opción correcta en la misma
proporción. Queda pendiente una pasada de rebalanceo de longitud si se
quiere volver a bajar esa cifra al rango histórico (~25-30%).

**Segunda ronda de triage por calificación (2026-09-13):** revisando de
nuevo las calificaciones (backend intermitente ese día — ver nota de
estabilidad más abajo), aparecieron 3 preguntas nuevas con 1 estrella
(eliminadas) y 24 con 2 estrellas (reescritas con el mismo criterio:
distractores balanceados en longitud, sin lenguaje absolutista). Quedó en
6/24 = 25% "correcta = más larga" en el subconjunto reescrito, alineado con
el ~25% esperado por azar. Banco: 1467 → 1464 (−3 eliminadas, +0 netas de
las reescritas ya que solo cambian contenido, no cantidad).

**Nota de estabilidad del backend (2026-09-13):** el Google Apps Script Web
App que guarda intentos/calificaciones (`SHEETS_WEBAPP_URL` en
`app/index.html`) mostró comportamiento intermitente (a veces 404, a veces
200, a veces sin responder) durante esta sesión, causando que los
porcentajes de la app cayeran a 0% momentáneamente al no poder cargar el
historial. No es un bug del código de la app — es infraestructura del lado
de Google Apps Script fuera de este repo. Si vuelve a pasar, revisar
Extensions → Apps Script en la Sheet → Deploy → Manage deployments.

**Regla ajustada (2026-09-13):** el usuario refinó la política — las de
**1 estrella se eliminan**, pero las de **2 estrellas se reescriben** (no
se borran). Se revirtió la eliminación de las 18 preguntas de 2 estrellas
(recuperadas del historial de git) y se reescribieron sus distractores
quitando el lenguaje absolutista y balanceando la longitud de las opciones
respecto a la respuesta correcta (de 16/18 "correcta = más larga" a 4/18,
cerca del ~25% esperado por azar). Las 2 de 1 estrella permanecen
eliminadas. Banco: 1449 → 1467. **Regla permanente:** de ahora en más,
1★ = eliminar, 2★ = reescribir (nunca solo borrar) — no hace falta
pedirlo de nuevo cada vez que se revisen calificaciones.

**Corrección: preservar formatos verdadero/falso y selección múltiple
extendida (2026-09-13):** el usuario aclaró que el examen real de CCNP sí
incluye preguntas verdadero/falso (2 opciones) y preguntas de selección
múltiple con más de 4 opciones entre las que elegir — el paso de "corrección
de esquema" del punto anterior había forzado esas 29 preguntas a 4 opciones
por error, asumiendo incorrectamente que el formato fijo de 4 opciones era
un requisito del banco. Se revirtieron las 29 preguntas a su formato
original (23 verdadero/falso de 2 opciones, 6 de selección múltiple con 5-6
opciones), y se actualizó la app (`app/index.html` e `index.html`) para
renderizar dinámicamente el número real de opciones de cada pregunta en vez
de asumir siempre A-D fijo (antes hardcodeado en `renderQuestion` y
`applyLockedState`). Se verificó en navegador que el render y el marcado de
correcto/incorrecto funcionan bien con 2 opciones. Se re-ejecutó el shuffle
de posición con una versión generalizada que soporta cualquier cantidad de
opciones (no solo 4).
