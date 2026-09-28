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
  proporcionalmente al peso de cada dominio en el examen, con algo de
  desviación en el dominio 5 tras la purga de 2026-09-16 — ver changelog).
  Formato:
  ```json
  {
    "version": "2026-09-27_v30",
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
| 1.0 Architecture | 15% | 176 | 17.0% |
| 2.0 Virtualization | 10% | 122 | 11.8% |
| 3.0 Infrastructure | 30% | 304 | 29.4% |
| 4.0 Network Assurance | 10% | 111 | 10.7% |
| 5.0 Security | 20% | 152 | 14.7% |
| 6.0 Automation & AI | 15% | 170 | 16.4% |
| **Total** | **100%** | **1035** | **100%** |

La distribución por dominio se desvió del blueprint tras la purga de
calidad de 2026-09-16 (ver changelog) — Security quedó 5.3 puntos por
debajo de su peso oficial (20%) porque ahí se concentraron varias de las
preguntas de peor calidad eliminadas. Sigue pendiente una eventual pasada
de contenido nuevo en Security si se quiere recuperar la proporción
exacta; por ahora las 146 preguntas restantes en ese dominio son de
calidad más consistente que antes de la purga.

Meta base de 1000 preguntas alcanzada 2026-09-08, con distribución por
dominio dentro de ±0.8 puntos porcentuales del peso oficial del blueprint
(cifra histórica, ver nota arriba sobre la desviación post-purga).
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

**Bug encontrado y corregido: cobertura por encima de 100% (2026-09-15):**
tras las sucesivas rondas de eliminación de preguntas mal calificadas,
Cris reportó "hay porcentajes sobre el 100%" en la app. Causa: `subtopicStats`
contaba cada `questionId` distinto alguna vez respondido para ese subtema,
sin verificar que la pregunta siguiera existiendo en el banco actual — un
intento antiguo contra una pregunta ya eliminada seguía sumando al
numerador (`respondidas`/`correctas`), y como varios subtemas perdieron
más preguntas de las que ganaron (p. ej. `3.1.a` y `1.1.b`, con varias de
sus preguntas `b6_*` eliminadas en las rondas de calificación), el
numerador terminó superando el denominador (`total` del banco actual):
`3.1.a` llegó a 119% y `1.1.b` a 104%. Corregido agregando un set
`VALID_IDS` (todos los ids que existen hoy en `ALL_QUESTIONS`) y
filtrando por él antes de contar una pregunta como respondida/acertada
en `subtopicStats` — los intentos contra preguntas ya eliminadas se
siguen contando para la precisión histórica (`%resp.`, que no depende del
banco actual) pero ya no inflan la cobertura (`%banco`). Verificado en
vivo tras el fix: `3.1.a` → 96% (21/25/26), `1.1.b` → 83% (19/19/23),
ningún subtema por encima de 100%. Cambio solo en el JS de
`app/index.html`/`index.html`; no requirió tocar `question_bank.json`.

**Cuarta ronda de triage por calificación (2026-09-15):** revisando qué
preguntas se habían calificado desde la última pasada (comparando la
Sheet de ratings contra el banco actual para descartar calificaciones
ya procesadas cuyas preguntas ya fueron eliminadas), aparecieron 6
preguntas nuevas con 2 estrellas — ninguna con 1 estrella — todas del
lote `b6_` (SD-WAN componentes ×2, QoS ×1, EtherChannel ×3), con el
mismo patrón de siempre: respuesta correcta visiblemente más larga y
distractores con lenguaje tipo "reserved solely for", "a rename that
took effect company-wide", "generally assumed to hard-code". Se
eliminaron las 6 y se agregaron 3 nuevas (IDs `r5_*`) en estilo
directo/conciso, una por cada subtema afectado. Banco: 1424 → 1421.

**Gran purga de calidad, solo preguntas de IA (2026-09-16):** a pedido
explícito del usuario ("necesito hacer una gran purga y eliminar una
gran cantidad de preguntas hechas por ia que sean de mala calidad...
quiero reducir el total al menos a 1000. Las preguntas no de ia
dejalas intactas"), con el criterio de selección dejado a discreción
de Claude Code. Metodología:
- **Universo protegido:** las 469 preguntas con campo `source` (OCG,
  Exam Cram, ipcisco.com — es decir, "sacadas de internet" en las
  propias palabras del usuario) quedaron completamente fuera de
  consideración, sin tocar ni una.
- **Universo candidato:** las 952 preguntas sin `source` (generadas por
  IA). De ahí, el lote `b6_` (639 preguntas, el más grande y más
  antiguo del banco, generado antes de que existiera la disciplina
  anti-sesgo del proyecto) se identificó como el objetivo casi
  exclusivo: es el lote que, ronda tras ronda de calificación, sigue
  apareciendo como origen de las preguntas peor puntuadas (ver rondas
  1-4 arriba), así que concentrar la purga ahí evita tocar lotes más
  nuevos (`b2_`-`b5_`, `r3_`-`r5_`, `trustsec_`, `yang_`, `restconf_`,
  `vxlan_`, `apicc_`, `vswitch_`) que no muestran ese patrón.
- **Score de calidad programático** por pregunta (no "a ojo"): +3 por
  cada frase absolutista detectada en los distractores (mismo detector
  de la limpieza de 2026-09-13, extendido con frases nuevas vistas en
  rondas recientes: "reserved solely for", "generally assumed to
  hard-code", "permanently and irreversibly", etc.), +2 por frase de
  meta-comentario ("a common misconception", "it is worth noting"),
  +2 por relleno genérico, +2 por sesgo de longitud extremo (correcta
  >1.6× el promedio de los distractores), +1 por explicación en español
  demasiado corta (<35 caracteres), +1 por opciones excesivamente
  extensas (>900 caracteres combinados) — más un desempate continuo por
  longitud total dentro de un mismo puntaje categórico.
- **Excepción por calificación directa:** 20 preguntas que puntuaban mal
  en el score automático tenían 4-5 estrellas puestas por el propio
  usuario — se excluyeron de la purga sin excepción (el juicio directo
  del usuario prevalece sobre la heurística) y se reemplazaron con las
  siguientes candidatas peor puntuadas de la cola.
- **Piso de cobertura por subtema:** ningún subtema quedó con menos de
  10 preguntas totales tras la purga (se validó programáticamente antes
  de ejecutar; 7 candidatas adicionales del lote `b6_` se salvaron por
  esta razón).
- Verificación manual de una muestra (la peor puntuada, una del límite
  de corte, y una del lote conservado con puntaje 0) confirmó que el
  score correlaciona bien con calidad real — incluso las del límite de
  corte, aunque no dispararan el detector de frases exactas, mostraban
  el mismo patrón de distractor absolutista con redacción ligeramente
  distinta ("no valid use for", "not supported to ever install any",
  "functionally and technically identical").

Resultado: se eliminaron 425 preguntas del lote `b6_` (de 639 originales
quedan 214). Banco: 1421 → 996. Se reaplicó el shuffle de posición y se
reconstruyeron ambos HTML. **Efecto colateral positivo:** el sesgo de
longitud bank-wide (pendiente desde 2026-09-13, ver
`feedback_mc_question_bias.md`) bajó de 38.3% a 27.5% "correcta = más
larga", quedando dentro del rango ~25-30% esperado por azar — las
preguntas `b6_` peor puntuadas resultaron ser, en gran parte, las mismas
que inflaban ese sesgo, así que la purga lo corrigió como subproducto sin
una pasada de rebalanceo dedicada. **Efecto colateral negativo:** la
distribución por dominio se desvió del blueprint (ver tabla de cobertura
arriba), sobre todo en Security (20% → 14.7%), porque ahí se concentró
parte de la purga; queda pendiente si se quiere corregir con contenido
nuevo.

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

**Test de LearnCisco.net agregado (2026-09-27):** Cris pasó un archivo
`ENCOR TEST.odt` con 55 preguntas de un test de práctica de
learncisco.net (sin clave de respuestas ni explicaciones). Se procesaron
con el mismo criterio que ipcisco.com/OCG/Exam Cram: texto reescrito en
palabras propias (nunca copiado literal), respuesta correcta determinada
y verificada contra conocimiento real de ENCOR (no solo asumida), y
chequeo de duplicados contra el banco existente antes de fusionar. De las
55 originales se descartaron 16: 3 duplicados internos del propio test
(TCAM, "switch logging level", TrustSec — cada uno aparecía dos veces),
3 sin subtema de blueprint asociado (TCAM interno, movilidad de WLC
inalámbrico ×2, PPDIOO), 1 que dependía de una figura/tabla incrustada
que no sobrevivió la extracción de texto (troubleshooting de VTP), y 5
que resultaron ser duplicados de hechos ya cubiertos por preguntas
existentes (puerto UDP de LISP data-plane, tipo de cifrado de `service
password-encryption`, lenguaje de Chef, definición de "virtual switch",
comando `ip access-group`, y el estado 2-Way de OSPF en redes
broadcast). Se agregaron las 39 restantes (`lc_*`), marcadas `"source":
"learncisco.net"`, con el shuffle de posición aplicado solo a esas 39
preguntas nuevas (A 12/B 5/C 6/D 9 de 32 preguntas de respuesta única).
Verificado en navegador: pregunta de opción única y de selección
múltiple del lote ambas renderizan y califican correctamente. Banco:
996 → 1035.

**Piloto de acortamiento de preguntas de IA (2026-09-27):** Cris notó
que sus preguntas propias son cortas (como las de learncisco.net) pero
las mías (IA) son "larguísimas", y pidió un piloto antes de aplicarlo
a las 527. Se reescribieron 18 preguntas de los subtemas 4.1/4.2/4.3
(lotes `b2_`/`b3_`/`b5_`/`b6_`, los anteriores a la disciplina de
estilo directo de 2026-09-14 — los lotes `r3_`/`r4_`/`r5_` ya estaban
en formato conciso y se dejaron sin tocar). Primer intento: 51.7% más
corto, pero midiendo el resultado se encontró que la opción correcta
había quedado como la más larga en 17/18 preguntas — el mismo sesgo de
longitud que el proyecto ya había corregido varias veces antes,
reintroducido sin querer al escribir la versión corta con más
completitud que los distractores. Se corrigió en una segunda pasada
alargando un distractor por pregunta con detalle técnico genuino
(mismo método de 2026-09-12), sin tocar la respuesta correcta,
quedando en 4/18 (22.2%, cerca del ~25% esperado por azar) y 50.2% más
corto que el original. Verificado en navegador. **Pendiente de
decisión con Cris:** si se aplica al resto del banco (~509 preguntas
IA restantes en lotes `b2_`-`b6_`), aplicar esta misma disciplina de
dos pasadas (acortar, después medir y corregir sesgo de longitud) en
vez de asumir que la primera pasada ya es suficiente.

**Dominio 1 (Architecture) completo (2026-09-27):** tras el piloto,
Cris pidió aplicar el acortamiento a "todas las preguntas de IA del
punto 1" — las 95 preguntas de dominio 1 en lotes `b2_`/`b3_`/`b5_`/`b6_`
(quedan sin tocar `r3_`/`r4_`/`r5_`, ya en estilo directo). Mismo
proceso de dos pasadas que el piloto: primera pasada 46.4% más corto,
pero con la respuesta correcta como la más larga en 86/95 (90.5%) —
una confirmación a mayor escala de que escribir la versión corta con
más completitud que los distractores es un hábito difícil de evitar
incluso sabiendo del problema. Segunda pasada: se extendió un
distractor por pregunta con detalle técnico genuino en 79 preguntas,
lo cual sobre-corrigió a 8.4% (el patrón inverso — "la correcta nunca
es la más larga" — es igual de explotable); se revirtieron 16 de esas
79 extensiones a su texto de la primera pasada para volver a subir el
número, quedando en **24/95 (25.3%)**, alineado con el ~25% esperado
por azar. Verificado en navegador. Banco: sin cambio en total de
preguntas (sigue en 1035), solo se reescribió el texto de las 95.

**Dominio 2 (Virtualization) completo (2026-09-27):** mismo proceso
sobre las 71 preguntas de IA de dominio 2 en lotes `vswitch_`/`vxlan_`/
`b2_`/`b3_`/`b5_`/`b6_` (quedan intactas `r3_`/`r4_`, ya concisas).
46.0% más corto en total. Sesgo de longitud: primera pasada 88.7%
(63/71) con la correcta como más larga; se corrigió en dos rondas
adicionales (48 distractores extendidos, luego 13 más de ajuste fino)
hasta **18/71 (25.4%)**. Verificado en navegador y directamente contra
el JSON embebido en el HTML. Banco: sigue en 1035 preguntas, solo texto
reescrito.

**Dominio 3 (Infrastructure) completo (2026-09-27):** el más grande de
los tres hechos hasta ahora — 126 preguntas de IA en lotes `b2_`/`b3_`/
`b4_`/`b6_` (quedan intactas `r3_`/`r4_`/`r5_`). 49.6% más corto en
total. Sesgo de longitud: primera pasada 81.0% (102/126), corregido en
dos rondas adicionales (70 distractores extendidos, luego 24 más de
ajuste fino) hasta **40/126 (31.7%)**, dentro del rango 19-31% ya
observado históricamente en el proyecto. Verificado en navegador y
contra el JSON embebido. Banco: sigue en 1035 preguntas.

**Dominio 4 (Network Assurance) completo (2026-09-27):** el más chico
de los cuatro hechos hasta ahora — de las 39 preguntas de IA en total,
22 ya venían resueltas del piloto original (subtemas 4.1/4.2/4.3), así
que solo hicieron falta 17 preguntas nuevas en 4.4 (IP SLA), 4.5
(Catalyst Center) y 4.6 (NETCONF/RESTCONF). Sesgo de longitud sobre
esas 17: primera pasada 94.1% (16/17), corregido en una pasada (12
distractores extendidos) hasta 3/17. Medido sobre el dominio completo
(39 preguntas, incluyendo las ya arregladas del piloto): **9/39
(23.1%)**, dentro del rango esperado. Verificado en navegador. Banco:
sigue en 1035 preguntas.

**Dominio 5 (Security) completo (2026-09-27):** 63 preguntas de IA en
lotes `b2_`/`b3_`/`b5_`/`b6_` (quedan intactas `r3_`/`r4_`/`trustsec_`
— este último lote ya estaba en estilo directo/conciso desde su
creación, no necesitó reescritura). 50.4% más corto en total. Sesgo de
longitud: primera pasada 88.3% sobre el lote reescrito, corregido en
tres rondas (38 distractores extendidos, luego 9 más, más una
corrección manual de una pregunta omitida en el primer pase) hasta
**20/63 (31.7%)**. Verificado en navegador. Banco: sigue en 1035
preguntas.

**Dominio 6 (Automation and AI) completo (2026-09-27) — último de los
seis:** 61 preguntas de IA reescritas en lotes `b2_`/`b3_`/`b5_`/`b6_`/
`yang_`/`apicc_`/`restconf_` (quedan intactas `r3_`/`r4_`, ya concisas,
y las preguntas de estos mismos lotes que ya venían cortas desde su
creación, como `restconf_01-03/06/07` o `apicc_01/02/09/10`). 60.6% más
corto en total. Sesgo de longitud: primera pasada 83.6% (51/61),
corregido en dos rondas (33 distractores extendidos, luego 4 más de
ajuste fino tras detectar empates/gaps insuficientes) hasta **19/61
(31.1%)**, en el límite superior del rango 19-31% ya observado
históricamente en el proyecto. Verificado en navegador (sesión de
práctica en vivo sobre dominio 6, incluida una pregunta reescrita —
`b6_6.1_15`, sobre `response.json()` — renderizada y calificada
correctamente) y contra el JSON embebido. Banco: sigue en 1035
preguntas. Con esto quedan completos los seis dominios del blueprint:
todo el "banco propio (IA)" verboso fue reescrito para igualar el
estilo directo de las preguntas basadas en fuentes reales.
