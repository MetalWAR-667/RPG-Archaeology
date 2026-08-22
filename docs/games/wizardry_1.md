# Wizardry: Proving Grounds of the Mad Overlord — Autopsia de un dungeon

## 1. El espécimen

*Wizardry: Proving Grounds of the Mad Overlord* aparece a comienzos de los años ochenta como uno de los primeros grandes intentos de trasladar al microordenador doméstico una experiencia de RPG basada en party, exploración de mazmorras, combate, progresión y persistencia.

Su presentación resulta extremadamente austera desde una perspectiva moderna: texto, menús y una pequeña representación lineal en primera persona del dungeon.

Sin embargo, esa austeridad resulta engañosa.

La investigación del juego mediante documentación contemporánea, análisis posteriores y especialmente la reconstrucción del código Pascal/assembler realizada por Thomas William Ewers revela un sistema considerablemente más complejo de lo que comunica su interfaz. Los análisis posteriores advierten además que la reconstrucción no constituye el código fuente original y que algunos nombres de variables y funciones fueron asignados durante la ingeniería inversa.

Ésta será por tanto una distinción fundamental:

La reconstrucción de Ewers permite observar el comportamiento del programa original, pero no debe confundirse con los fuentes escritos originalmente por Greenberg y Woodhead.

Nuestra primera conclusión provisional será también el hilo conductor de esta disección:

La austeridad de *Wizardry* está fundamentalmente en su representación. El modelo de juego que existe detrás es mucho más rico.

## 2. El dungeon como estructura de datos

El mundo de *Wizardry* no necesita almacenar geometría tridimensional.

Cada planta constituye un espacio lógico de 20 × 20 posiciones.

El registro reconstruido TMAZE contiene cuatro matrices cardinales:

```text
W[20][20]
S[20][20]
E[20][20]
N[20][20]
```

Cada entrada utiliza un TWALL con cuatro posibles estados:

- OPEN
- WALL
- DOOR
- HIDEDOOR

Cuatro posibilidades requieren solamente 2 bits.

Por tanto, para una coordenada (x,y):

```text
N = 2 bits
E = 2 bits
S = 2 bits
W = 2 bits
---------
    8 bits
```

Las cuatro matrices cardinales de una planta requieren así aproximadamente:

```text
20 × 20 × 4 × 2 bits
= 3200 bits
= 400 bytes
```

La primera lección de arquitectura aparece inmediatamente:

*Wizardry* no almacena aquello que dibuja. Almacena la información mínima necesaria para reconstruirlo.

### 2.1. Una corrección importante: los edges están duplicados

Una interpretación inicial podría hacernos pensar que la frontera entre dos celdas se almacena una única vez.

No parece ser así.

Existen cuatro arrays cardinales completos. Por tanto:

A.E

y:

B.W

son valores almacenados independientemente.

Conceptualmente *Wizardry* utiliza:

```text
Cell[x,y]
├── North
├── East
├── South
└── West
```

y no:

```text
Cell A ─── SharedEdge ─── Cell B
```

Esto introduce redundancia, pero ofrece una ventaja enorme: consultas espaciales triviales.

Si el grupo mira al norte, se consulta N[x][y]; si mira al este, E[x][y].

No hace falta resolver ownership, localizar una estructura compartida ni normalizar la frontera.

No tenemos evidencia de que la velocidad fuese la razón explícita de esta elección, por lo que debe quedar como hipótesis. Pero arquitectónicamente encontramos un intercambio muy interesante:

pequeña redundancia de datos a cambio de simplicidad extrema en las consultas del runtime.

## 3. Una celda es más que geometría

TMAZE no contiene únicamente walls.

También encontramos estructuras equivalentes a:

```text
FIGHTS[20][20]
SQREXTRA[20][20]
SQRETYPE[]
AUX0[]
AUX1[]
AUX2[]
ENMYCALC[]
```

Es decir, el espacio combina varias capas:

```text
         CELL
          │
     ┌─────┼─────┐
     ▼     ▼     ▼
topology event encounter
```

Los tipos especiales incluyen comportamientos como:

- STAIRS
- PIT
- CHUTE
- SPINNER
- DARK
- TRANSFER
- OUCHY
- BUTTONZ
- ROCKWATE
- FIZZLE
- SCNMSG
- ENCOUNTE

Por tanto, el dungeon no es únicamente un plano.

Es un espacio lógico ejecutable.

Una coordenada puede provocar una transición, cambiar la orientación, causar daño, eliminar iluminación, mostrar información o iniciar un encuentro.

Esto sustituye parcialmente algo que en el RPG de mesa resolvía el referee:

RPG de mesa

```text
posición
   ↓
referee interpreta situación
   ↓
consecuencia
```

*Wizardry* necesita:

```text
posición
   ↓
tipo de celda
   ↓
regla predefinida
   ↓
consecuencia
```

El ordenador obliga a formalizar el espacio.

## 4. El estado espacial de la party

*Wizardry* tampoco necesita representar espacialmente a seis personajes.

La *party* es una única entidad navegacional.

Su estado fundamental puede reducirse aproximadamente a:

```text
level
x
y
facing
```

Moverse hacia delante modifica una coordenada ±1 según la orientación.

Y después ocurre algo maravilloso:

```text
(x + 20) MOD 20
(y + 20) MOD 20
```

El dungeon es topológicamente toroidal.

Salir por un extremo puede introducir al grupo por el opuesto.

No hace falta implementar un sistema especial de teleport para conseguirlo. Es una propiedad matemática del sistema de coordenadas.

Esto produce otra lección:

Una propiedad barata de representación puede convertirse en una propiedad jugable del mundo.

## 5. El renderer: no existe un mundo 3D

La vista de *Wizardry* parece tridimensional.

Pero el programa no necesita construir un espacio tridimensional general.

La rutina reconstruida DRAWMAZE trabaja desde:

```text
MAZEX
MAZEY
DIRECTIO
```

y obtiene información relativa:

- LEFT
- FRONT
- RIGHT

Después avanza virtualmente por el corredor.

Para cada profundidad:

- consultar left edge
- consultar right edge
- consultar front edge
- dibujar líneas
- reducir caja perspectiva
- avanzar posición lógica

La perspectiva se obtiene reduciendo progresivamente dimensiones.

No tenemos:

```text
vertices
polygons
camera
projection matrix
raycasting
```

Tenemos esencialmente:

```text
GRID STATE
    ↓
CARDINAL QUERIES
    ↓
PREDEFINED LINE GEOMETRY
    ↓
SCREEN
```

La imagen no representa un mundo geométrico independiente.

Es una visualización del estado lógico del grid.

## 6. La luz modifica el renderer

La iluminación proporciona otro ejemplo excelente de integración entre mecánica y representación.

La variable que determina la distancia visible modifica directamente cuántas posiciones hacia delante inspecciona DRAWMAZE.

Conceptualmente:

```text
SPELL
  ↓
LIGHT
  ↓
VIEW DISTANCE
  ↓
NUMBER OF CELLS INSPECTED
```

No hace falta un sistema de iluminación tridimensional.

Una mecánica mágica modifica simplemente la profundidad de consulta del renderer.

El resultado para el jugador sigue siendo perfectamente legible: ahora puede ver más lejos.

## 7. Estado real frente a percepción

Éste probablemente sea uno de los principios espaciales más importantes de *Wizardry*.

El ordenador conoce siempre:

```text
posición
orientación
topología
tipo de frontera
special square
```

El jugador no.

Podemos distinguir:

```text
WORLD STATE
     │
     ▼
PERCEPTION RULES
     │
     ▼
PLAYER INFORMATION
     │
     ▼
PLAYER'S MENTAL MAP
```

Y *Wizardry* ataca deliberadamente los enlaces entre esas capas.

**Hidden Door**

El mapa puede saber que una frontera es HIDEDOOR mientras la representación muestra aparentemente una pared.

**Darkness**

Reduce información disponible.

**Teleporter**

Cambia el estado espacial sin necesariamente proporcionar al jugador suficiente información para reconstruir inmediatamente qué ha ocurrido.

**Spinner**

Es quizá el ejemplo perfecto.

Conceptualmente su efecto esencial es casi ridículo:

```text
facing = random(4)
```

La posición puede permanecer exactamente igual.

Pero el mapa mental del jugador puede quedar destruido.

**DUMAPIC**

Hace justamente lo contrario: revela al jugador información espacial que el programa conoce internamente.

La navegación de *Wizardry* se convierte así en un juego sobre información espacial, no únicamente sobre movimiento.

## 8. La economía de una mecánica

Aquí aparece una idea especialmente aprovechable para diseño moderno.

Podemos analizar las mecánicas no sólo por lo que producen, sino por lo que cuestan.

### Spinner

Objetivo:
- desorientar

Estado:
- special square

Operación:
- `facing = random(4)`

Resultado:
- destrucción potencial del mapa mental

Coste técnico diminuto.

Consecuencia jugable enorme.

### Hidden Door

Estado:
- `TWALL = HIDEDOOR`

Coste:
- 2 bits dentro de la representación existente

Runtime:
- regla adicional de percepción

Resultado:
- exploración + secretos

### Poison

El veneno tiene un 25 % de probabilidad de actuar durante cada round de combate o cada paso por el maze.

Esto convierte el movimiento en un tick de simulación:

```text
MOVE
 ↓
simulation tick
 ↓
poison check
```

Una mecánica diminuta conecta navegación, gestión de recursos y urgencia.

Ésta será una de las grandes lecciones de *Wizardry*:

La profundidad de una mecánica no guarda una relación directa con su coste técnico.

## 9. La party como proyecto

La creación del personaje ya introduce planificación.

Raza determina atributos iniciales y además interviene en sistemas ocultos como saving throws.

Las clases poseen requisitos diferentes y las clases avanzadas exigen combinaciones mucho más restrictivas. Las curvas de experiencia también divergen considerablemente.

Pero el elemento realmente interesante aparece con el cambio de clase.

El personaje no puede describirse únicamente mediante:

class
level

Su pasado importa.

Puede conservar:

HP procedentes de su desarrollo anterior;
hechizos aprendidos;
capacidad mágica derivada de esos hechizos;
información relacionada con niveles previos.

El personaje posee por tanto historia mecánica.

## 10. MaxLev: el pasado existe en los datos

MaxLev es especialmente revelador.

El sistema recuerda un nivel máximo relevante incluso cuando el personaje cambia de clase o posteriormente sufre level drain.

Eso permite situaciones como:

```text
FIGHTER LEVEL 10
       │
       │ class change
       ▼
MAGE LEVEL 1
```

Pero:

```text
MaxLev = 10
HP heredados
hechizos/capacidades previas
```

El estado actual no describe completamente al personaje.

Esto es una forma primitiva pero potente de persistencia histórica del build.

## 11. HP: progresión mediante reroll

La subida de HP tampoco es una suma trivial.

*Wizardry* vuelve a calcular un resultado basado en los dados de la clase, nivel y Vitality. Si el nuevo resultado supera los HP existentes, lo utiliza; de lo contrario el personaje obtiene solamente una mejora mínima.

Eso produce una consecuencia estadística interesante:

los malos rerrolls no destruyen el progreso anterior.

Con sucesivas oportunidades, los HP pueden tender hacia resultados favorables dentro de su distribución.

Y esto interactúa con los cambios de clase porque un personaje puede conservar temporalmente HP muy superiores a los que su nueva clase/nivel producirían normalmente.

## 12. Los atributos no son escalas lineales

Strength muestra perfectamente que una estadística de 3–18 no significa necesariamente una función continua.

Los datos recuperados muestran:

```text
STR 3  → -15 % hit / -3 damage
STR 4  → -10 % / -2
STR 5  → -5 % / -1

STR 6–15 → zona neutra

STR 16 → +5 % / +1
STR 17 → +10 % / +2
STR 18 → +15 % / +3
```

Por tanto:

```text
STR 7
STR 11
STR 15
```

pueden ser funcionalmente equivalentes para determinados cálculos de combate.

El número mostrado al jugador no describe directamente la función matemática subyacente.

## 13. El personaje visible y el personaje real

*Wizardry* posee estadísticas derivadas que prácticamente no enseña.

Un ejemplo fundamental son los cinco saving throws:

- Death
- Petrify
- Wand
- Breath
- Spell

Clase, raza, Luck y nivel intervienen en ellos. Sin embargo, no aparecen como cinco valores claramente expuestos en la ficha. El análisis del código incluso concluye que Save vs. Wand aparentemente no tiene aplicación efectiva en *Wizardry* I.

Esto produce dos modelos:

```text
VISIBLE CHARACTER
STR IQ PIE VIT AGI LUC
Class / Level / HP / AC
        │
        ▼
HIDDEN CHARACTER MODEL
saving throws
HitCalcMod
SwingCount
HitDam
CritHit
MaxLev
HealPts
...
```

El jugador controla un sistema más complejo de lo que la UI representa.

## 14. El combate como pipeline

Un ataque físico tampoco equivale simplemente a lanzar un dado.

Conceptualmente encontramos algo parecido a:

```text
CLASS + LEVEL
       │
STRENGTH
       │
WEAPON
       ▼
HitCalcMod
       │
       ▼
chance to hit
       │
       ▼
SwingCount
       │
       ▼
N independent strikes
       │
       ▼
HitDam
       │
       ▼
damage
       │
       ▼
critical / resistance / status
```

SwingCount permite que Fighters, Samurai, Lords y especialmente Ninjas obtengan múltiples strikes; cada strike comprueba independientemente su posibilidad de impacto. Las armas también pueden proporcionar su propio SwingCount, utilizándose el valor superior en lugar de acumular ambos.

La probabilidad de impacto queda además limitada para evitar probabilidades absolutas.

Eso preserva incertidumbre incluso ante grandes diferencias estadísticas.

### 14.1. SwingCount: progresión y equipo no se acumulan

SwingCount determina cuántos strikes independientes puede intentar el personaje durante un ataque físico.

La progresión de determinadas clases incrementa su SwingCount.

Las armas pueden proporcionar también un SwingCount propio.

El hallazgo importante es que ambas fuentes NO se suman.

Conceptualmente:

```text
CLASS / LEVEL ----\
                  MAX ---> EFFECTIVE SWINGCOUNT
WEAPON -----------/
```

Ejemplo conceptual:

```text
Character = 3
Weapon = 4

max(3, 4) = 4
```

NO:

```text
3 + 4 = 7
```

Como consecuencia, la regla limita el stacking:

- progresión de personaje y equipo pueden modificar la misma capacidad;
- las fuentes compiten en lugar de acumularse;
- un arma puede proporcionar una mejora enorme mientras supera la capacidad natural del personaje;
- conforme la capacidad natural aumenta, el beneficio relativo del SwingCount del arma puede disminuir;
- evita crecimiento acumulativo de esa variable.

### 14.2. El Ninja desarmado: el personaje como arma

El Ninja clásico ejemplifica cómo *Wizardry* crea identidad de clase modificando reglas fundamentales existentes, sin introducir un subsistema completamente separado de "artes marciales".

**A) Defensa natural.**

La AC desarmada del Ninja sigue:

AC = 8 - floor(Level / 3)

| Level | AC |
| --- | --- |
| 1 | 8 |
| 15 | 3 |
| 30 | -2 |

Una AC inferior es mejor.

**B) Daño desarmado.**

Ninja: 1d4 + 1d4

Otras clases desarmadas: 1d2 + 1d2

Los modificadores aplicables, como Strength, continúan participando en el pipeline normal.

**C) SwingCount natural.**

La progresión encontrada es:

Ninja: 2 + floor(Level / 5)

Usando Level 15 como ejemplo:

2 + floor(15/5) = 5 swings

El Ninja reutiliza el pipeline físico existente pero modifica sus parámetros y reglas.

### 14.3. CritHit: una sola oportunidad después de los swings

Contrariamente a lo que podría inferirse de un sistema de "una tirada crítica por swing", el comportamiento reconstruido es:

1. Se resuelven los swings.
2. Cada swing realiza su propia comprobación de impacto.
3. El daño causado se acumula en HPDAMAGE.
4. Sólo si HPDAMAGE > 0 puede comprobarse CritHit.
5. Se realiza UNA única comprobación de CritHit para el ataque completo.
6. Si ésta tiene éxito, se pasa a la segunda comprobación (MonsterLevel).

```text
N SWINGS
   ↓
independent hit checks
   ↓
accumulate HPDAMAGE
   ↓
HPDAMAGE > 0 ?
   ↓
one CritHit check
   ↓
MonsterLevel check
   ↓
instant kill
```

La probabilidad de activar CritHit para el Ninja escala aproximadamente como:

min(2 × NinjaLevel, 50) %

Ejemplo:

Ninja Level 15 → 30 %

Es importante no describir esto como probabilidad final de decapitación: todavía existe la segunda comprobación del monstruo.

Más swings NO significa más tiradas críticas.

Más swings aumenta la probabilidad de que al menos uno produzca HPDAMAGE > 0, permitiendo alcanzar la única comprobación de CritHit.

### 14.4. MonsterLevel como resistencia al crítico

Después de superar la comprobación de CritHit, se realiza una segunda comprobación:

(RANDOM MOD 35) > (MonsterLevel + 10)

RANDOM MOD 35 produce valores 0..34.

Esto genera una resistencia progresiva basada únicamente en MonsterLevel:

| MonsterLevel | Resultados favorables | Probabilidad de superar la segunda comprobación |
| ---: | ---: | ---: |
| 1 | 12–34 | 65,71 % |
| 10 | 21–34 | 40,00 % |
| 20 | 31–34 | 11,43 % |
| 23 | 34 | 2,86 % |
| 24+ | ninguno | 0 % |

El umbral surge de la fórmula:

MonsterLevel 23: 23 + 10 = 33. Sólo RANDOM = 34 supera 33.

MonsterLevel 24: 24 + 10 = 34. Se necesitaría un valor >34. Pero RANDOM MOD 35 nunca supera 34.

Por tanto:

MonsterLevel ≥ 24 → inmunidad matemática al crítico, sin necesidad de una flag CRITICAL_IMMUNE explícita.

### 14.5. La fórmula frente al bestiario

**MAELIFIC**

MonsterLevel = 25

Consecuencia: matemáticamente inmune al CritHit del Ninja.

**VAMPIRE LORD**

MonsterLevel = 20

No es inmune. Sólo 4 resultados de 35 superan la segunda comprobación: 31, 32, 33, 34.

P(second check) = 4/35 ≈ 11,43 %

**WERDNA**

MonsterLevel = 10

No posee inmunidad matemática derivada de esta fórmula.

P(second check) = 14/35 = 40 %

Para un Ninja Level 15, condicionado a HPDAMAGE > 0:

0.30 × 0.40 = 0.12

aproximadamente 12 % de probabilidad de crítico final tras alcanzar este punto del pipeline.

Ser el antagonista final NO introduce por sí mismo inmunidad mediante esta mecánica.

**POISON GIANT**

Su definición peculiar, 1d8 + 50, hace que determinados sistemas que consultan su nivel lógico puedan tratarlo como MonsterLevel 1 pese a su elevada reserva de HP.

Esto ilustra: una variable compartida puede producir coherencia sistémica, pero una anomalía en esa variable puede propagarse a todos los sistemas que dependen de ella.

Muchos HP ≠ MonsterLevel alto.

## 15. Los estados conectan sistemas

Sleep/Hold no son simplemente etiquetas.

Un personaje o monstruo dormido o inmovilizado recibe doble daño. Determinadas armas también producen daño doble contra categorías concretas de enemigos.

**Poison** conecta combate y navegación.

Paralysis y Stone conectan combate, traps, magia y recuperación.

Death puede conducir a Ashes.

Ashes puede conducir a Lost.

El sistema produce así cadenas:

```text
OK
 ↓
POISON / SLEEP / PARALYSIS / STONE
 ↓
DEAD
 ↓
ASHES
 ↓
LOST
```

No todas son necesariamente secuencias obligatorias, pero constituyen una jerarquía de deterioro con diferentes herramientas de recuperación.

## 16. La muerte no es un simple Game Over

Éste es uno de los puntos de diseño más importantes.

El personaje existe persistentemente fuera de la expedición.

La derrota individual no implica necesariamente:

```text
DEAD → reload
```

Puede implicar:

```text
DEAD
 ↓
recover body
 ↓
temple / spell
 ↓
resurrection attempt
 ↓
success / ashes / lost
```

DI y KADORTO utilizan Vitality para determinar la probabilidad de resurrección y pueden producir pérdida permanente de Vitality; determinadas situaciones pueden acabar convirtiendo al personaje en Lost.

La muerte se convierte así en estado del personaje, no necesariamente en estado final de la partida.

## 17. La expedición como unidad estratégica

Eso transforma completamente la estructura del juego.

*Wizardry* no consiste sólo en avanzar hacia Werdna.

Su ciclo fundamental es:

```text
         CASTLE
           │
           ▼
    PREPARE PARTY
           │
           ▼
        DUNGEON
           │
    ┌──────┼───────┐
    ▼      ▼       ▼
  combat  loot   attrition
    │      │       │
    └──────┼───────┘
           ▼
    CONTINUE?
       /       \
     YES        NO
      │          │
  deeper       return
      │          │
      └──────────┘
           ↓
         CASTLE
```

La decisión importante no es sólo cómo ganar un combate, sino:

¿cuánto riesgo adicional puedo asumir antes de regresar?

Las recomendaciones de juego conservadas muestran precisamente expediciones muy cortas durante los primeros niveles y posteriormente rutas de progreso y farming.

Aquí tenemos uno de los antepasados claros del expedition loop.

## 18. El dungeon como curva de dificultad

Las trampas muestran una integración especialmente elegante entre espacio y progresión.

Su desarmado incorpora:

```text
Character Level
-
Maze Level
```

con enormes ventajas para Thief/Ninja frente a otras clases.

Por tanto, la profundidad del dungeon no es sólo geográfica:

```text
DEEPER
  ↓
higher Maze Level
  ↓
harder systemic checks
```

La coordenada vertical del mundo se convierte en parámetro de dificultad.

Esto evita tener que configurar individualmente cada comprobación.

## 19. La party distribuye competencias

*Wizardry* tampoco exige que cada personaje pueda resolver todo.

- Thief/Ninja dominan trampas.
- Mage/Priest tienen familias mágicas distintas.
- Bishop combina progresiones pero con compromisos.
- Fighter/Samurai/Lord/Ninja poseen diferentes curvas de ataques y capacidades.
- Priest, Bishop y Lord pueden Dispel undead con distintas penalizaciones y niveles de acceso.

La unidad real de diseño no es necesariamente el personaje.

Es la party.

Eso permite crear personajes especializados sin exigir autosuficiencia individual.

## 20. Progresión horizontal dentro de la vertical

*Wizardry* tiene niveles, pero la progresión no es exclusivamente:

```text
Level ↑
numbers ↑
```

También incorpora:

- new spells
- new spell circles
- more strikes
- class eligibility
- class changes
- equipment
- special abilities
- resistances

Los spell points dependen de clase, nivel y círculo, y los hechizos ya conocidos pueden garantizar disponibilidad incluso tras cambios de clase.

El personaje acumula por tanto opciones, no únicamente magnitudes.

## 21. El envejecimiento introduce coste temporal

La edad está almacenada con granularidad de semanas.

Cambiar de clase envejece al personaje varios años y la edad interviene en la evolución de atributos. Los cálculos recuperados muestran además que personajes jóvenes tienen mejores probabilidades de desarrollo favorable.

Esto introduce un precio no monetario:

```text
CLASS CHANGE
    ↓
new capabilities
    +
age increase
    ↓
future development affected
```

Es una forma de evitar que la reconversión de clase sea una decisión completamente gratuita.

## 22. Los encounters escalan estructuralmente

La dificultad tampoco necesita depender únicamente de incrementar HP y daño.

El sistema puede modificar:

- número de grupos;
- número de enemigos;
- tipos disponibles;
- nivel del dungeon;
- resistencias;
- composición.

Eso permite que la dificultad crezca mediante combinatoria, no sólo mediante inflación numérica.

Es otra lección todavía perfectamente aprovechable:

Añadir relaciones entre amenazas puede escalar dificultad más eficientemente que multiplicar estadísticas.

## 23. Bugs como fósiles

La reconstrucción contiene comportamientos programados que aparentemente no llegan a utilizarse correctamente.

HAMAN/MAHAMAN poseen efectos adicionales que parecen quedar inaccesibles debido a una expresión CASE incorrecta. Entre ellos aparecen protección extrema de AC y resurrección de la party.

También encontramos:

AFRAID, aparentemente sin productores claros en *Wizardry* I;
Save vs. Wand, aparentemente sin función efectiva;
poison capaz de representar valores que el contenido de *Wizardry* I no parece producir;
poderes especiales de objetos programados pero no utilizados.

Esto permite distinguir:

```text
ENGINE CAPABILITY
       ≠
CONTENT ACTUALLY USED
```

No debemos inferir automáticamente por qué existe ese código.

Puede ser:

funcionalidad descartada;
infraestructura anticipada;
restos de desarrollo;
soporte compartido;
bugs.

Pero constituye evidencia de evolución interna del software.

## 24. Cuando la implementación se convierte en mecánica

LostXYL es probablemente nuestro fósil favorito.

La estructura tiene múltiples usos y durante una expedición uno de sus campos termina relacionado con poison.

El efecto observable documentado es:

disolver la party elimina el veneno.

Esto probablemente no representa una decisión narrativa del tipo:

«volver al castillo cura mágicamente el veneno».

Es una consecuencia de cómo se reutiliza estado.

Tenemos:

```text
MEMORY / DATA REPRESENTATION
           ↓
     reused variable
           ↓
    state transition
           ↓
     gameplay quirk
```

Aquí literalmente podemos ver la costura entre arquitectura y diseño.

## 25. El manual tampoco es la verdad absoluta

La ingeniería inversa permite otra clase de arqueología: comparar documentación e implementación.

El análisis recuperado encuentra fórmulas, comportamientos y valores que no siempre coinciden con aquello que comunica el manual. Snafaru, por ejemplo, señala que TILTOWAIT realiza 10d15 de daño aunque el manual lo describa como 10d10.

Esto obliga a distinguir:

```text
DESCRIBED RULE
IMPLEMENTED RULE
OBSERVED BEHAVIOR
```

Para RPG Archaeology ésta debería ser una norma metodológica permanente.

## 26. Donde se ven las costuras

*Wizardry* también permite comportamientos emergentes o exploits que surgen de la interacción entre sistemas.

Las guías documentan, dependiendo de la versión:

parties Good/Evil construidas mediante separación y reunión dentro del dungeon;
recuperación mediante utilities;
duplicación mediante transferencia entre escenarios;
generación repetida de personajes para acumular oro;
exploits específicos del Bishop.

No todos tienen la misma naturaleza.

Conviene distinguir:

- intended strategy
- emergent strategy
- edge case
- implementation leak
- bug
- exploit

Esta clasificación puede convertirse en una sección recurrente de nuestras futuras autopsias.

## 27. El diseño del dungeon

El mapa que hemos examinado resulta especialmente revelador porque sobre una cuadrícula aparentemente elemental aparecen:

- walls
- doors
- secret doors
- encounters
- monster lairs
- darkness
- spinners
- teleports
- pits
- chutes
- elevators
- stairs
- magic suppression
- messages.

El dungeon puede entenderse entonces como una composición de transformaciones del estado del jugador.

Una secuencia como:

```text
CORRIDOR
   ↓
DARKNESS
   ↓
SPINNER
   ↓
SECRET DOOR
   ↓
ENCOUNTER
```

no es sólo arquitectura.

Es un programa de experiencia:

```text
remove information
       ↓
alter orientation
       ↓
hide topology
       ↓
apply pressure
```

Ésta probablemente sea una de las ideas más fértiles para estudiar posteriormente planta por planta.

## 28. Del referee al algoritmo

Aquí *Wizardry* enlaza directamente con nuestra genealogía anterior.

En *Blackmoor*, Arneson podía recibir:

«Intento hacer X.»

y decidir qué ocurría.

El ordenador no puede hacerlo libremente.

Debe convertir el mundo en vocabulario finito:

```text
OPEN
WALL
DOOR
HIDEOOR

STAIRS
PIT
SPINNER
DARK
TRANSFER
...
```

Podemos representar esta transición:

```text
BLACKMOOR
human referee
interpretive space
continuous possibilities
        │
        ▼
      CRPG
        │
        ▼
 WIZARDRY
formal state
discrete space
enumerated behavior
algorithmic resolution
```

No significa que *Wizardry* derive exclusivamente de *Blackmoor* ni que esta transformación ocurriera únicamente aquí.

Pero es una excelente demostración material de qué debe ocurrir cuando una experiencia arbitrada se convierte en software.

## 29. Restricción como herramienta de diseño

Lo más interesante de *Wizardry* quizá no sea que lograra superar las limitaciones del *Apple II*.

Es que muchas de sus soluciones utilizan esas limitaciones productivamente.

No hay geometría general:

→ grid.

No hay renderer 3D complejo:

→ consultas cardinales + line drawing.

No puede describirse cualquier interacción espacial:

→ special squares.

No hay referee:

→ estados y fórmulas.

Memoria limitada:

→ packed arrays, enums pequeños y reutilización de datos.

Representación visual mínima:

→ el jugador completa mentalmente el espacio.

Y de ahí emerge una paradoja:

Reducir la representación puede aumentar la importancia de la imaginación, la información y los sistemas.

## 30. Lecciones aprovechables para diseño moderno

Aquí sí tenemos material directamente útil.

| Principio | Wizardry | Aplicación general |
| --- | --- | --- |
| Representa estado, no apariencia | TMAZE almacena topología | Derivar visuales del modelo lógico |
| Busca mecánicas baratas y multiplicativas | Spinner | Poco código, gran efecto |
| Separa verdad y percepción | Hidden doors / darkness | Información como recurso |
| Haz que el espacio participe en los sistemas | Maze Level afecta traps | Geografía = dificultad |
| Usa la party como unidad de diseño | Especialización de clases | Interdependencia |
| Permite historia mecánica | Class change / spells / MaxLev | Builds con trayectoria |
| Conecta sistemas pequeños | Poison + movement | Emergencia |
| Haz significativa la retirada | Expedition loop | Riesgo acumulativo |
| Escala mediante composición | Encounter groups | Evitar sólo HP inflation |
| Acepta abstracción | Renderer de líneas | No simular lo innecesario |
| Controla cómo se componen las fuentes | SwingCount usa max(class, weapon) | Evitar stacking accidental cuando varias fuentes modifican una misma capacidad |
| Reutiliza variables sistémicas | MonsterLevel regula progresivamente CritHit | Obtener comportamientos complejos sin añadir estados especiales innecesarios |

Y una undécima que me parece particularmente relevante:

No gastes complejidad técnica donde el jugador puede aportar complejidad cognitiva.

*Wizardry* no necesita simular desorientación.

Cambia facing.

El jugador se desorienta solo.

Eso es diseño eficiente de cojones.

## 31. La tesis arqueológica

Después de abrir el cadáver, *Wizardry* I deja de parecer un RPG rudimentario al que posteriormente se añadieron sistemas sofisticados.

La lectura que empieza a emerger es otra:

*Wizardry* ya posee una sorprendente densidad sistémica. Lo que todavía es primitivo es principalmente la capacidad de representarla.

El *Apple II* obliga a comprimir.

La navegación del dungeon termina reducido a matrices empaquetadas.

El espacio, a coordenadas y orientación.

La perspectiva, a unas líneas.

Las interacciones espaciales, a enums.

Los personajes, a registros.

La incertidumbre, a fórmulas y tablas.

Lo mismo ocurre en el combate: pocos valores (SwingCount, CritHit, MonsterLevel) y fórmulas pequeñas se reutilizan y combinan para producir una riqueza de comportamientos que supera lo que cada pieza aislada sugiere.

Pero esas abstracciones se conectan:

```text
              SPACE
                │
       ┌────────┼────────┐
       ▼        ▼        ▼
  perception  events   encounters
       │        │        │
       └────────┼────────┘
                ▼
              PARTY
                │
       ┌────────┼────────┐
       ▼        ▼        ▼
    classes   magic    combat
       │        │        │
       └────────┼────────┘
                ▼
           ATTRITION
                │
           continue?
            /      \
         dungeon   castle
                     │
                     ▼
              PERSISTENCE
```

Y esa red de relaciones es la que produce profundidad.

No los gráficos.

No la cantidad de contenido.

No una simulación físicamente compleja.

Las relaciones entre sistemas pequeños.