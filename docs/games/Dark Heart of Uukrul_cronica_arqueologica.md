# The Dark Heart of Uukrul

## Anatomía de una ciudad imposible --- crónica arqueológica de Eriosthe

> **RPG Archaeology --- Expedición Uukrul, agosto de 2026**\
> Una lectura del juego, de sus autores y de lo que encontramos al abrir
> el cadáver digital.

------------------------------------------------------------------------

## Prólogo --- Entramos buscando un mapa

Hay excavaciones que empiezan con una gran pregunta y otras que empiezan
porque alguien mira un archivo de 1024 bytes y piensa: **«¿qué demonios
hay aquí dentro?»**

La nuestra fue de las segundas.

*The Dark Heart of Uukrul* no llegó a la mesa de disección como un juego
cualquiera. Había aparecido durante la investigación de los dungeon
crawlers clásicos porque tenía algo extraño incluso contemplado desde
2026: un mundo enorme sin necesitar mapas enormes, una exploración en
primera persona que desembocaba en combate táctico cenital, una magia
dividida entre hechizos fiables y plegarias caprichosas, santuarios que
eran simultáneamente refugios, hitos de progresión y parte de la
ficción, y un diseño que parecía empeñado en que el jugador **aprendiera
el mundo en lugar de limitarse a recorrerlo**.

La pregunta inicial era mucho más pequeña:

> **¿Podemos reconstruir los mapas de Eriosthe a partir de los datos
> originales?**

Podíamos.

El problema fue que, una vez abierta la primera puerta, detrás había
otra. Y otra. Y otra.

Terminamos reconstruyendo la geometría de los niveles, persiguiendo
transiciones y teleportadores, diseccionando los archivos de guardado,
construyendo un pequeño editor seguro, descubriendo que el juego
rechazaba modificaciones perfectamente legales, siguiendo una ruta de
validación por el ejecutable y los overlays, configurando DOSBox-X como
laboratorio de depuración y, finalmente, parcheando experimentalmente un
ejecutable de 1990 para intentar arrancarle el valor de un registro
justo antes de una comparación misteriosa.

En algún punto dejamos de investigar un dungeon crawler.

**Nos metimos dentro de Eriosthe.**

------------------------------------------------------------------------

# I. Dos estudiantes, demasiadas tazas de café y ninguna obligación de obedecer a D&D

Ian Boswell y Martin Buis estudiaban Informática en la Universidad de
Auckland a principios de los años ochenta. Habían jugado a *Wizardry I*
y *II* en Apple II y llegaron a una conclusión peligrosísima para
cualquier programador joven:

**«Esto podríamos hacerlo nosotros.»**

Pero la influencia importante no fue solamente *Wizardry*. Ambos
disfrutaban de las aventuras de texto y de su componente intelectual.
Boswell recordaría décadas después que desde el principio querían algo
con el sabor de una aventura narrativa y de puzles, no simplemente otro
RPG de combatir monstruos. Tampoco estaban especialmente interesados en
obedecer un reglamento heredado. Buis había jugado a wargames y algo de
D&D; Boswell apenas tenía experiencia con el rol de mesa. Decidieron
seguir las convenciones únicamente cuando les parecieran útiles.

Ese detalle explica mucho de Uukrul.

No diseñaron una adaptación informática de un sistema previo.
**Diseñaron reglas para el problema que querían plantear.**

El proyecto empezó como trabajo de tiempo libre. Boswell terminó
especializándose en programación y Buis en planificación y diseño,
aunque ambos describen las ideas importantes como fruto de sesiones
conjuntas y abundante café. También tuvieron que fabricar buena parte de
sus propias herramientas y bibliotecas: en aquel momento no existía el
ecosistema de motores, middleware y utilidades que hoy damos por
sentado.

El resultado fue un juego hecho desde las restricciones.

No a pesar de ellas.

------------------------------------------------------------------------

# II. Eriosthe no es una colección de niveles

El manual contiene una declaración de intenciones extraordinariamente
útil para entender el juego:

Eriosthe fue diseñada para sentirse como **un lugar enorme y real**.

Los autores incluso advierten al jugador de que no debe interpretar las
escaleras como la progresión tradicional de «nivel 1, nivel 2, nivel 3».
La dificultad avanza principalmente **entre Santuarios**, no
verticalmente.

Décadas después, Boswell explicó que el laberinto se diseñó precisamente
de esa manera. La unidad práctica de autoría era la región comprendida
entre dos santuarios consecutivos. Normalmente uno de los dos
diseñadores se responsabilizaba de una región y después el otro la
probaba y ajustaba. Eso ayuda a explicar por qué diferentes partes de
Eriosthe poseen identidad propia.

Es una idea sorprendentemente moderna:

``` text
Santuario
    ↓
región con identidad
    ↓
problemas / encuentros / reglas
    ↓
nuevo Santuario
```

El Santuario no es únicamente un checkpoint.

Es **puntuación espacial**.

Divide la aventura en frases sin convertir la ciudad en una secuencia de
pisos artificiales.

Y la arqueología binaria terminó mostrando lo económico que era el
soporte físico de aquella ilusión.

------------------------------------------------------------------------

# III. 1024 bytes para construir un mundo

Los niveles `MZ*.LEV` estudiados ocupan exactamente **1024 bytes**.

La geometría utiliza los primeros `0x320` bytes:

``` text
25 filas
× 16 columnas
× 2 bytes por celda
= 800 bytes
```

Los 224 bytes restantes forman un bloque estructurado asociado a eventos
o scripting del nivel.

La celda, por tanto, es la unidad fundamental.

Y en su byte bajo encontramos la topología cardinal:

``` text
0x01 = EAST
0x04 = SOUTH
0x10 = WEST
0x40 = NORTH
```

Bit activado significa **muro / dirección bloqueada**.

No encontramos evidencia de dos grandes arrays independientes de edges.
La conectividad está codificada por celda. El runtime consulta esa
información para movimiento y línea de visión.

La ciudad que desde fuera parecía enorme podía expresarse con una
economía brutal:

``` text
celda
→ paredes
→ orientación
→ feature
→ flags
→ evento
```

Eso fue uno de los hallazgos más interesantes de toda la excavación.

**Uukrul no necesita una gran cantidad de espacio para producir
densidad. Necesita que cada posición importe.**

El manual lo dice desde el otro extremo del sistema: monstruos, objetos
y elementos del laberinto fueron diseñados para funcionar conjuntamente
como **puzles interconectados**.

Treinta y seis años después, los bytes cuentan exactamente la misma
historia.

------------------------------------------------------------------------

# IV. El jugador no recorre el dungeon: aprende su gramática

Una puerta secreta en Uukrul no tiene necesariamente una solución.

Puede requerir fuerza. Puede esconder un mecanismo. Puede responder a
una palabra. Puede ceder ante magia, plegarias u objetos. Algunas
necesitan primero ser descubiertas. En regiones avanzadas incluso la
búsqueda convencional deja de ser suficiente y entra en juego `TALIS`.

Los teleportadores tampoco son meros ascensores. Existen destinos
nombrados, dispositivos de botones, enlaces que primero deben
descubrirse y mecanismos cuya lógica forma parte del puzle.

El automapa parece una concesión amable hasta que el propio juego
empieza a mentirle.

Hay regiones donde no funciona. Lugares donde el espacio se retuerce.
Spinners. Pasajes que no obedecen intuitivamente a la representación. En
CHAOS, incluso las paredes pueden comportarse de forma unilateral y
desplazarse en una dirección sin permitir el retorno equivalente.

El sistema enseña una regla.

Después enseña excepciones.

Finalmente pregunta si el jugador ha comprendido realmente la regla o
sólo se había acostumbrado a su apariencia.

Buis lo explicó de una forma que podría servir como manifiesto de
diseño:

> Gran parte del placer de los juegos consiste en aprender sus reglas y
> descubrir después cómo explotarlas.

Por eso hablaba de puzles a distintas escalas: una región puede ser un
puzle, el funcionamiento de los sacerdotes puede ser otro y la historia
completa puede ser otro más.

No hay una frontera clara entre **mecánica** y **acertijo**.

El sistema es el acertijo.

------------------------------------------------------------------------

# V. Cuatro Aspirantes, cuatro formas de relacionarse con el mundo

La party de Uukrul está fijada alrededor de cuatro disciplinas:

-   Fighter;
-   Paladin;
-   Priest;
-   Magician.

Fighter y Paladin ocupan roles reconocibles: fuerza física, armadura,
armas y, en el caso del Paladin, la capacidad de imponer o restaurar
vida mediante *lay on hands*.

Pero Priest y Magician revelan el carácter propio del juego.

El Magician trabaja con cinco Arkana:

``` text
Fire
Frost
Protection
Healing
Knowledge
```

Cada una posee su propio anillo, progresando desde hierro hasta cristal.
Los hechizos cuestan Psychic Points, requieren el anillo adecuado y, si
se lanzan correctamente, **funcionan**.

El Priest vive en otro universo epistemológico.

Sus cuatro dioses son:

``` text
Ufthu   — War
Drutho  — Underworld
Golthur — Physical Self
Fshofth — Spiritual Self
```

También dispone de anillos y de Virtue Points.

Pero una plegaria no es una orden.

Es una petición.

El manual insiste en que los dioses pueden ignorar al sacerdote y que un
aspirante demasiado ambicioso puede incluso ser castigado. También hace
algo todavía más interesante: **se niega deliberadamente a explicar
completamente qué hacen las plegarias**. El sacerdote debe adquirir ese
conocimiento mediante experiencia.

El jugador aprende religión exactamente como el personaje:

**probando.**

------------------------------------------------------------------------

# VI. Favor divino: cuando una interfaz puede ser una teología

El sistema de plegarias es uno de los mejores ejemplos de Uukrul porque
combina ficción, incertidumbre y mecánica sin necesidad de una pantalla
de «reputación con los dioses».

El manual proporciona nombres, textos rituales y advertencias.

No entrega una tabla transparente de porcentajes.

La guía de Lee Beng Hai, escrita desde la experiencia del jugador, hace
precisamente lo contrario: convierte años de descubrimiento en
conocimiento práctico. `RALKOR` ataca, `DROM` fortalece, `MORPETH`
reduce encuentros, `TULAR` proporciona alimento bajo condiciones
concretas, `KUURAOTH` despierta una visión, `LAIRIAN` pertenece al
desenlace.

Entre ambos documentos existe una distancia preciosa:

``` text
Manual
→ lo que el mundo te permite saber

Experiencia del jugador
→ lo que terminas descubriendo

Código
→ lo que realmente sucede
```

Ésa es la capa que una futura excavación con Ghidra puede intentar
cerrar.

Durante esta investigación apareció material secundario que afirmaba que
el favor divino estaba completamente revertido y reducido a fórmulas
exactas. No lo hemos aceptado como evidencia: esas afirmaciones no
venían acompañadas de una cadena técnica verificable equivalente a la
que exigimos durante la autopsia.

Por ahora, la conclusión arqueológica más interesante no necesita
conocer todavía cada porcentaje:

**Uukrul transforma deliberadamente una función matemática en una
relación incierta con una divinidad.**

El jugador no pulsa «Heal III --- 73 %».

Pronuncia una plegaria y espera que alguien escuche.

------------------------------------------------------------------------

# VII. La muerte debía doler

Boswell era incómodo con una convención que hoy seguimos discutiendo:
morir, cargar una partida y comportarse como si nada hubiera ocurrido.

Su idea inicial era mucho más radical: **save/resume, pero no
save/restore**.

Muere un personaje, permanece muerto.

Broderbund rechazó la propuesta.

Y probablemente hizo bien.

La solución fue mejor: los Santuarios.

El personaje muerto conserva su alma temporalmente mediante su Soul
Amulet. Puede ser llevado al Mausoleum para intentar una resurrección,
pero el cadáver envejece y las posibilidades disminuyen. Resucitar
cuesta dinero, vitalidad y experiencia. Si ya no puede recuperarse, el
amuleto pasa al Guild y otro personaje de la misma clase puede ocupar su
lugar.

La muerte deja de ser:

``` text
GAME OVER
→ LOAD
```

y se convierte en:

``` text
muerte
→ pérdida
→ retirada
→ coste
→ recuperación
→ posible sustitución
→ continuación de la historia
```

El manual llega a pedir explícitamente al jugador que no abuse de los
backups porque el juego es mejor cuando la muerte significa algo.

Buis explicó el objetivo: querían que ese riesgo hiciera más cargados
emocionalmente tanto el combate como la exploración y produjera momentos
donde el jugador fuese agresivo y otros donde actuase con cautela.

La mecánica no busca castigar por sadismo.

Busca producir **comportamiento**.

------------------------------------------------------------------------

# VIII. Mara: el fantasma de una partida que jugó antes que nosotros

Antes de que lleguemos, otra party ya intentó derrotar a Uukrul.

Mara dejó su diario.

Al principio es el relato de un grupo poderosísimo que cree haber
comprendido la situación. Avanzan deprisa. Vencen enemigos. Interpretan
la retirada de Uukrul como miedo.

Incluso cuando encuentran resistencia, la leen desde su propia
superioridad.

Entonces descubren que estaban siendo conducidos.

Uukrul no huía.

**Los estaba colocando.**

La entrada del 18º día de verano destruye la ilusión. El martillo que
debía derrotarlo se rompe. La party queda atrapada al otro lado del
Palacio. Comienzan las pérdidas. Nalusi desaparece. Kiriya cae. Esal
muere. Mara comprende finalmente que Uukrul quiere mantenerla viva como
trofeo.

El diario hace algo magnífico: enseña al jugador cómo **no** debe
interpretar el juego.

Mara es poderosa y fracasa porque cree que poder equivale a comprensión.

Los protagonistas, en cambio, empiezan como Aspirantes precisamente
porque Uukrul no los considera una amenaza.

La progresión del jugador no consiste solamente en hacerse más fuerte.

Consiste en reconstruir el conocimiento que destruyó a la expedición
anterior.

Boswell y Buis necesitaban además una forma de mantener a Mara presente
sin que estuviera físicamente acompañando al grupo. Las apariciones
resolvieron el problema: podía convertirse en guía, pista y vínculo
emocional sin romper la estructura del dungeon.

Una limitación técnica produjo un recurso narrativo.

Otra vez.

------------------------------------------------------------------------

# IX. El corazón que no estaba en el cuerpo

La campaña entera termina revelándose como una gigantesca operación de
preparación.

Uukrul no puede ser derrotado simplemente golpeándolo más fuerte.

Su corazón y su alma están protegidos.

El jugador debe reunir corazones de piedra, obtener el corazón oscuro,
recuperar el martillo de obsidiana, comprender los mecanismos del
Palacio, armar el Beacon, fijar a Uukrul a este plano y sólo entonces
destruirlo.

La guía convierte esa secuencia en instrucciones.

El juego la convierte en arqueología interna:

``` text
rumor
→ inscripción
→ objeto
→ profecía
→ región
→ mecanismo
→ revelación
```

El enfrentamiento final no es la comprobación de que el grupo posee
suficientes puntos de golpe.

Es la comprobación de que el jugador **ha entendido Eriosthe**.

Por eso el manual afirma que el juego no trata de contar monstruos o
medir el tamaño del laberinto.

El tamaño real de Uukrul está en la cantidad de relaciones que existen
entre sus sistemas.

------------------------------------------------------------------------

# X. Y entonces nosotros abrimos el cadáver

Nuestra expedición de 2026 empezó mucho más abajo.

No queríamos resolver los puzles.

Queríamos saber cómo estaban hechos.

Los primeros resultados fueron limpios. Los `.LEV` cedieron. Apareció el
grid 16×25. Aparecieron los dos bytes por celda. Los cuatro bits
cardinales terminaron identificados de forma consistente. El bloque
final de eventos mostró estructura.

Después perseguimos los teleportadores.

Encontramos tablas, relaciones y una cadena runtime capaz de acabar
cargando un nuevo nivel:

``` text
evento / port
→ selección
→ datos de destino
→ X / Y / Level
→ SetLevel
→ nuevo MZ
```

No cerramos todo el world graph, pero sí demostramos su arquitectura.

Entonces cometimos el error más natural del mundo:

**«Ya que sabemos dónde está el mundo... ¿y si tocamos una partida?»**

------------------------------------------------------------------------

# XI. GMI e IMG: dos mitades de una memoria

Los experimentos controlados con saves revelaron una separación
elegante.

`.GMI` contiene principalmente el estado estructurado de la party.

Encontramos cuatro registros de personaje, con campos identificables
para estado, HP actual, HP máximo, nombre e inventario, preservando
cuidadosamente aquello cuya semántica todavía desconocíamos.

`.IMG`, en cambio, se comporta como una imagen del estado runtime del
mundo.

Las pruebas A/B/C fueron especialmente reveladoras.

Una partida base.

La misma partida después de girar.

La misma partida después de moverse.

El GMI permanecía idéntico mientras IMG cambiaba.

Terminamos localizando:

``` text
+0x0A → Y
+0x0C → X
+0x0E → LEVEL
+0x14 → FACING
```

Con suficiente confianza para construir una pequeña herramienta de
arqueología de saves.

El parser hacía round-trip byte-perfect.

Los bytes desconocidos permanecían intactos.

Podíamos cambiar HP.

Podíamos cambiar orientación.

El binario resultante era coherente.

Y Uukrul dijo:

**No.**

------------------------------------------------------------------------

# XII. La puerta que no debía existir

Probamos una modificación ridículamente inocente:

``` text
HP 42 → 40
```

Rechazada.

Probamos otra:

``` text
FACING 0 → 1
```

Rechazada.

No estábamos intentando dar al Fighter nueve millones de puntos de vida
ni teletransportar al grupo fuera del mapa.

Estábamos escribiendo estados que el propio juego podía producir
legalmente.

Y aun así el loader detectaba que algo no cuadraba.

Ahí cambió la expedición.

Lo que había empezado como reconstrucción de mapas se convirtió en una
autopsia del sistema de integridad.

Localizamos una ruta:

``` text
save loader
→ validation / dispatch
→ handler dinámico
→ AX
→ CMP AX,0x0097
→ decisión
```

Apareció un salto indirecto:

``` text
ljmp cs:[0x05DE]
```

El destino se configuraba en runtime mediante una cadena de punteros.

La disección estática empezaba a pelear contra overlays, segmentos,
direcciones dinámicas y las decisiones de un ejecutable DOS de hace más
de tres décadas.

Y entonces llegó el gato y el ratón.

------------------------------------------------------------------------

# XIII. El día que casi pusimos PBR a Uukrul

DOSBox-X podía ejecutar el juego.

Podíamos preparar el debugger.

El agente podía lanzar el proceso.

Lo que no podía hacer de manera fiable era algo tecnológicamente
revolucionario:

**pulsar P cuando el juego esperaba que un humano pulsara P.**

Intentamos automatizar interacción con la ventana. SendKeys. PowerShell.
Foco. Logging. Configuraciones de depuración.

Cada solución abría otro pequeño problema.

La lección terminó siendo bastante más valiosa que la tecla:

> Si un humano puede resolver en cinco segundos una interacción que al
> agente le cuesta veinte minutos automatizar, el flujo correcto no es
> seguir automatizando.

De ahí salió una regla para futuras excavaciones:

``` text
HUMAN_ACTION_REQUIRED
```

debe ser un estado legítimo del agente.

No un fracaso.

Una herramienta.

El agente prepara el breakpoint.

Metal pulsa P.

El agente continúa.

Fin del drama.

No hace falta construir Skynet para atravesar un menú de DOS.

------------------------------------------------------------------------

# XIV. Laguna contra el cadáver

El último tramo de la sesión fue casi cómico.

Al no poder obtener cómodamente el valor de `AX` desde el debugger, la
investigación escaló hacia instrumentación del ejecutable.

Se preparó la idea de interceptar la ejecución justo antes de:

``` text
CMP AX,0x0097
```

capturar el registro, emitir sus bytes mediante I/O y devolver el flujo
al programa original.

Se conservaron backups.

Se corrigieron interpretaciones anteriores sobre el segmento de carga.

Se calcularon saltos relativos independientes del `load_seg`.

Y el parche llegó a aplicarse.

En ese momento decidimos detener la excavación antes de que el agente
terminara añadiendo iluminación físicamente basada al Palacio de Adron.

El resultado final quedó perfectamente delimitado:

``` text
INSTRUMENTATION IMPLEMENTED
FINAL MEASUREMENT PENDING
```

No sabemos todavía qué significa exactamente `AX`.

No sabemos qué representa `0x97`.

No sabemos cuál es el algoritmo final de integridad.

Y eso está bien.

Porque una autopsia seria también debe saber decir:

**UNKNOWN.**

------------------------------------------------------------------------

# XV. Lo que sí sabemos del cadáver

La expedición dejó una cantidad considerable de terreno firme.

## Mundo

``` text
LEV size                 1024 bytes
Grid                     16 × 25
Cell                     2 bytes
East wall                0x01
South wall               0x04
West wall                0x10
North wall               0x40
Event/script tail        0x320..0x3FF (estructura fuerte)
```

## Saves

``` text
GMI
→ party / character state

IMG
→ runtime / spatial world state
```

Con posición, orientación y nivel identificados experimentalmente.

## Integridad

Sabemos que:

-   cambios mínimos y válidos son rechazados;
-   existe una invariante adicional;
-   no basta con mantener campos semánticamente plausibles;
-   `0x24E` no explica por sí solo la validación;
-   las hipótesis iniciales de suma/XOR simple no resolvieron el
    problema;
-   la ruta del loader llega hasta un handler dinámico y una comparación
    con `0x97`.

Eso no es «no haber encontrado el checksum».

Es haber demostrado que la pregunta era más complicada que **«¿dónde
está el checksum?»**

------------------------------------------------------------------------

# XVI. Las hipótesis también mueren

Una de las mejores pequeñas historias de la investigación fue `30 E4`.

Aparecía repetidamente en el overlay.

Durante un momento pareció una pista hacia operaciones XOR relacionadas
con el supuesto checksum.

Después lo miramos correctamente:

``` asm
XOR AH, AH
```

Simplemente estaba limpiando `AH`.

Hipótesis muerta.

Marcada como **RETRACTED**.

Y seguimos.

Este detalle resume bien la filosofía que terminó imponiéndose durante
la excavación:

``` text
CONFIRMED
STRONG
SPECULATIVE
UNKNOWN
RETRACTED
```

No basta con guardar descubrimientos.

Hay que guardar **cuánta confianza tenemos en ellos**.

La arqueología digital se vuelve peligrosa cuando una interpretación
provisional se copia tres veces, entra en un resumen y reaparece una
hora después disfrazada de hecho.

------------------------------------------------------------------------

# XVII. El verdadero puzle de Uukrul

Después de abrir mapas, saves y código, resulta tentador pensar que el
gran secreto del juego es una técnica concreta.

No lo es.

Su mayor logro parece estar en cómo muchas decisiones apuntan en la
misma dirección.

Los autores querían que el jugador aprendiera reglas.

Por eso:

-   la ciudad está dividida en regiones con personalidad;
-   el automapa es útil hasta que deja de ser fiable;
-   las puertas admiten distintos lenguajes de interacción;
-   los objetos pueden tener funciones no evidentes;
-   los sacerdotes deben descubrir qué significan sus plegarias;
-   la muerte genera consecuencias en lugar de una simple pantalla de
    fracaso;
-   Mara enseña mediante el fracaso de una expedición anterior;
-   Uukrul no puede derrotarse sólo mediante estadísticas;
-   el final exige reunir conocimiento disperso por toda la campaña.

Incluso la tecnología refleja esa filosofía.

Un mapa diminuto en bytes produce un espacio enorme en decisiones.

**La densidad no está en la geometría. Está en las relaciones.**

------------------------------------------------------------------------

# XVIII. Por qué todavía merece ser excavado

El juego visible está extraordinariamente documentado. El manual es
generoso. Existen walkthroughs completos. Los puzles principales llevan
décadas resueltos. La entrevista de 2012 permite además escuchar
directamente a Boswell y Buis explicar muchas de sus intenciones.

Pero la arqueología de implementación todavía tiene zonas interesantes.

Nuestra propia autopsia dejó abiertas:

-   la semántica completa de los flags especiales de celda;
-   el bytecode final de eventos de los `.LEV`;
-   el world graph completo de transiciones;
-   la semántica del handler dinámico de validación;
-   el significado exacto de `AX` y `0x97`;
-   el algoritmo de integridad;
-   y una comparación sistemática Apple II ↔ DOS.

El siguiente banco de trabajo será distinto.

En lugar de intentar convertir DOSBox-X en un robot pianista, la próxima
excavación puede combinar **Ghidra + MCP + Codex**, utilizando el
decompilador como mapa navegable de funciones, referencias cruzadas y
estructuras.

El objetivo no será «reabrir Uukrul entero».

Será elegir una pregunta y perforar.

Como debería hacerse en cualquier excavación.

------------------------------------------------------------------------

# XIX. Lo que Uukrul nos enseñó sobre trabajar con agentes

El cadáver también terminó diseccionando nuestro propio workflow.

Cuando la tarea era acotada:

``` text
construye parser
preserva bytes desconocidos
añade tests
haz round-trip
```

el agente era extraordinariamente eficaz.

Cuando la orden se convertía en:

``` text
averigua qué demonios hace esto
```

el contexto empezaba a crecer.

Aparecían hipótesis paralelas.

Algunas se demostraban.

Otras morían.

Los resúmenes necesitaban compactarse.

El agente podía perder la jerarquía entre una conjetura de hacía tres
horas y un experimento recién confirmado.

La solución no es simplemente «un modelo más grande».

Es un método.

``` text
OBJECTIVE

CONFIRMED
STRONG
SPECULATIVE
RETRACTED
BLOCKED

ARTIFACTS

NEXT QUESTION
```

Y una segunda regla:

> **Cada corte debe reducir una incertidumbre concreta.**

Cuando el análisis estático deja de avanzar:

``` text
static → runtime
```

Cuando automatizar la interacción es absurdo:

``` text
agent → human-in-the-loop
```

Cuando hace falta picar piedra durante horas:

``` text
modelo fuerte → dirige
modelo rápido/local → ejecuta
```

Uukrul terminó siendo también una prueba de concepto de cómo queremos
hacer arqueología asistida por IA.

Nada mal para un cadáver de 1989.

------------------------------------------------------------------------

# XX. El juego que llegó tarde

Hay cierta ironía en la historia de *The Dark Heart of Uukrul*.

Boswell y Buis trabajaron durante años en él como proyecto de tiempo
libre. Cuando apareció, la tecnología visual ya estaba avanzando
deprisa. El propio Boswell reconocería que, terminado un año antes, su
aspecto habría resultado mucho más competitivo.

Tampoco fue un gran éxito comercial.

Boswell estimó en retrospectiva que el tiempo invertido pudo equivaler a
unos **dos dólares por hora**.

No hubo secuela.

Ambos continuaron sus carreras tecnológicas fuera de la industria del
videojuego.

Y, sin embargo, aquí estamos.

Décadas después, abriendo sus archivos.

Leyendo su manual.

Siguiendo las huellas de Mara.

Preguntándonos por qué un byte de orientación hace que un ejecutable DOS
se enfade.

Reconstruyendo un mapa que dos estudiantes de Auckland diseñaron entre
clases, trabajo y café.

El mercado decidió una cosa.

El tiempo decidió otra.

------------------------------------------------------------------------

# XXI. Epitafio para Eriosthe

La carta de los autores termina deseando que el jugador disfrute
explorando un universo nuevo.

Treinta y seis años después, ésa sigue siendo una descripción
sorprendentemente precisa de lo que ocurre cuando uno abre Uukrul.

Sólo que ahora la exploración puede continuar debajo del suelo.

Primero caminamos por Eriosthe.

Después miramos el automapa.

Después abrimos los `.LEV`.

Después los saves.

Después el ejecutable.

Y al final descubrimos que la ciudad seguía teniendo puertas.

Una de ellas todavía dice:

``` text
CMP AX, 0x0097
```

No sabemos qué hay detrás.

Todavía.

Y quizá ése sea el final correcto para esta expedición.

Porque *The Dark Heart of Uukrul* fue diseñado alrededor de una idea muy
concreta: el placer no está en recibir todas las respuestas.

Está en ver las piezas.

Probar.

Equivocarse.

Volver.

Y, de repente, comprender el dibujo.

------------------------------------------------------------------------

## Estado de la expedición

``` text
ERIOSTHE MAP FORMAT        CONFIRMED
CARDINAL WALL TOPOLOGY     CONFIRMED
SAVE SPLIT GMI/IMG         CONFIRMED
SAVE TOOL CORE             COMPLETE
WORLD EDIT BINARY          IMPLEMENTED
WORLD EDIT ORIGINAL GAME   REJECTED BY INTEGRITY
SAVE INTEGRITY EXISTENCE   CONFIRMED
INTEGRITY ALGORITHM        UNRESOLVED
FULL WORLD GRAPH           PARTIAL

EXPEDITION STATUS          CLOSED — FOR NOW
```

------------------------------------------------------------------------

# Fuentes y materiales de trabajo

## Fuentes primarias

**Boswell, Ian; Buis, Martin. *The Dark Heart of Uukrul --- Player's
Guide*. Broderbund / Digital Studios Ltd., 1989.**\
Manual original. Utilizado para reglas, estructura de clases, magia,
plegarias, exploración, combate, muerte, Santuarios, diario de Mara y la
carta de los autores.

**Boswell, Ian; Buis, Martin. Entrevista retrospectiva de RPG Codex, 26
de julio de 2012.**\
Utilizada para historia del desarrollo, influencias, proceso de diseño,
estructura regional del dungeon, filosofía de puzles, muerte,
Santuarios, copy protection y retrospectiva personal de los autores.

## Fuente comunitaria

**Lee Beng Hai (Volume). *The Dark Heart of Uukrul FAQ/Walkthrough*,
v1.1, 24 de mayo de 2002.**\
Utilizada como registro de conocimiento empírico del jugador: efectos
prácticos de plegarias, progresión, puzles, Santuarios, corazones,
Palacio de Adron, CHAOS y secuencia final.

## Excavación RPG Archaeology --- agosto de 2026

***The Dark Heart of Uukrul --- Informe de autopsia técnica*.**\
Resultados de análisis de `MZ*.LEV`, `UUKRUL.EXE`, `UUKRUL.OVR`,
`UUKRUL.LIB`, tablas auxiliares, saves controlados, herramienta de save
archaeology y experimentos runtime con DOSBox-X.

### Principio editorial

Las afirmaciones técnicas de esta crónica respetan la taxonomía de la
autopsia:

-   **CONFIRMED** --- demostrado mediante datos, código o experimento.
-   **STRONG** --- inferencia fuertemente apoyada.
-   **SPECULATIVE** --- hipótesis pendiente.
-   **UNKNOWN** --- no resuelto.
-   **RETRACTED** --- interpretación descartada.

Las afirmaciones externas no acompañadas por evidencia verificable no se
han convertido en hechos técnicos por el mero hecho de aparecer en una
búsqueda o resumen secundario.

------------------------------------------------------------------------

> **RPG Archaeology**\
> *No jugamos solamente a los viejos mundos. A veces levantamos las
> tablas del suelo para ver qué los mantenía en pie.*
