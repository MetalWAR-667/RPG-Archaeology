# Del RPG de mesa al CRPG — Cuando el referee se convirtió en software

## 1. Propósito de este documento

La historia completa del *Computer Role-Playing Game* es demasiado extensa para el propósito de RPG Archaeology.

Existen ya obras dedicadas específicamente a reconstruirla con mucha mayor profundidad.

Nuestro objetivo aquí es más limitado.

Queremos responder una pregunta:

> ¿Qué ocurrió cuando las estructuras del RPG de mesa tuvieron que convertirse en software?

La transición es especialmente interesante porque *Dungeons & Dragons* había formalizado buena parte de la experiencia del juego mediante:

- atributos;
- puntos de vida;
- clases;
- niveles;
- experiencia;
- tablas;
- monstruos;
- objetos;
- hechizos;
- mapas;
- procedimientos de combate.

Pero todavía conservaba una pieza fundamental que no podía trasladarse fácilmente al ordenador:

el referee.

El nacimiento del CRPG puede interpretarse, en parte, como el intento de sustituir las funciones de ese referee por algoritmos.

## 2. D&D como sistema formalizable

Un RPG de mesa contiene elementos narrativos difíciles de representar mediante software.

Pero también contiene estructuras extremadamente formales.

Un personaje de *D&D* podía reducir parte de su estado a números:

```text
CHARACTER
│
├── Attributes
├── Class
├── Level
├── Experience
├── Hit Points
├── Armor Class
├── Inventory
└── Spells
```

Un monstruo podía describirse mediante otra colección de valores.

Un combate podía resolverse mediante tablas, dados y procedimientos.

Un dungeon podía representarse mediante un mapa.

Por tanto, una parte considerable del juego ya tenía características muy próximas a un sistema computacional:

```text
ESTADO
  +
REGLAS
  +
AZAR
  +
INPUT DEL JUGADOR
=
NUEVO ESTADO
```

El ordenador era extraordinariamente adecuado para ejecutar esta parte del RPG.

## 3. El problema del referee

El referee humano hacía mucho más que consultar tablas.

También podía interpretar intenciones.

Un jugador podía decir:

> Intento distraer al guardia haciendo rodar una moneda por el pasillo.

Aunque ningún reglamento describiera exactamente esa situación, el referee podía:

- comprender la intención;
- considerar el contexto;
- elegir una regla apropiada;
- improvisar una resolución;
- modificar el mundo.

El ordenador no podía hacer eso.

Sólo podía reconocer acciones previamente representadas por el programador.

Por tanto:

```text
RPG DE MESA


¿Qué quieres hacer?
        │
        ▼
posibilidades abiertas
        │
        ▼
      REFEREE

debía transformarse en algo parecido a:

CRPG


¿Qué quieres hacer?
        │
        ▼
MOVE
ATTACK
CAST
USE
OPEN
TAKE
        │
        ▼
    ALGORITMO
```

Ésta constituye una de las diferencias fundamentales entre RPG de mesa y CRPG.

## 4. De la intención al comando

La transformación puede expresarse de otra manera.

En el tabletop:

```text
INTENCIÓN
    ↓
INTERPRETACIÓN
    ↓
REGLA / ADJUDICACIÓN
    ↓
RESULTADO
```

En el ordenador:

```text
COMANDO
    ↓
PROCEDIMIENTO
    ↓
CÁLCULO
    ↓
RESULTADO
```

El ordenador elimina gran parte de la ambigüedad.

A cambio obtiene enormes ventajas.

Puede ejecutar reglas rápidamente.

Puede mantener miles de valores de estado.

Puede recordar exactamente qué ha ocurrido.

Puede realizar cálculos sin intervención humana.

Puede ocultar información al jugador.

Puede generar azar.

Puede representar gráficamente el espacio.

Puede ejecutar enemigos automáticamente.

El CRPG no sería simplemente una versión limitada del RPG de mesa.

Se convertiría progresivamente en otro medio con fortalezas propias.

## 5. El ordenador hereda parte del trabajo del Dungeon Master

Podemos dividir algunas funciones tradicionales del referee:

```text
DUNGEON MASTER
│
├── mantiene el estado del mundo
├── conoce el mapa
├── oculta información
├── mueve enemigos
├── aplica reglas
├── calcula resultados
├── genera encuentros
├── controla tesoros
├── interpreta acciones
├── interpreta lenguaje
└── improvisa
```

Las primeras computadoras podían absorber bastante bien:

- ✓ mantener estado
- ✓ almacenar mapas
- ✓ ocultar información
- ✓ ejecutar enemigos
- ✓ aplicar reglas
- ✓ realizar cálculos
- ✓ generar azar
- ✓ gestionar tesoros

Pero tenían enormes dificultades con:

- ✗ interpretar intenciones
- ✗ comprender lenguaje abierto
- ✗ improvisar
- ✗ responder creativamente

El CRPG surgió alrededor de esa frontera.

## 6. El dungeon era casi una estructura de datos esperando ocurrir

Dentro de *D&D* existía además un entorno especialmente fácil de formalizar:

el dungeon.

Un dungeon estaba compuesto normalmente por elementos discretos:

```text
ROOM
  │
DOOR
  │
CORRIDOR
  │
DOOR
  │
ROOM
```

Eso podía transformarse fácilmente en datos.

Por ejemplo:

```text
CELL
│
├── north
├── south
├── east
├── west
├── contents
└── state
```

O mediante otras representaciones equivalentes.

El dungeon proporcionaba además límites naturales.

Una pared impide avanzar.

Una puerta conecta espacios.

Una habitación contiene entidades.

Un corredor conduce a otra posición.

El mundo podía reducirse a una estructura finita que el ordenador podía almacenar y consultar.

## 7. El dungeon también limitaba las acciones

La propia naturaleza del dungeon reducía el número de interacciones necesarias.

Gran parte de la experiencia podía representarse mediante:

- MOVE
- TURN
- OPEN
- CLOSE
- ATTACK
- CAST
- USE
- TAKE
- DROP
- REST

Eso era extremadamente importante.

El ordenador no necesitaba comprender:

cualquier acción imaginable.

Sólo necesitaba implementar un conjunto suficientemente interesante de verbos.

Por tanto:

> El dungeon no sólo era una ambientación apropiada para los primeros CRPG.

> Era un espacio de problemas especialmente compatible con las limitaciones del software temprano.

## 8. El grid como abstracción natural

La cuadrícula utilizada durante décadas por wargames y juegos de mesa encontró también un aliado perfecto en el ordenador.

Un espacio podía representarse mediante coordenadas:

**X, Y**

o mediante índices:

**MAP[12][8]**

Cada posición podía contener información.

- WALL
- FLOOR
- DOOR
- MONSTER
- TREASURE
- STAIRS
- PLAYER

El movimiento podía convertirse en una operación matemática.

```text
NORTH → y - 1
SOUTH → y + 1
WEST  → x - 1
EAST  → x + 1
```

La geometría compleja del mundo real podía reducirse a topología discreta.

Esta abstracción resultaría extraordinariamente fértil.

Décadas después seguirá apareciendo, de diferentes maneras, en numerosos dungeon crawlers.

## 9. Los primeros experimentos

Durante la década de 1970, estudiantes y programadores con acceso a sistemas universitarios comenzaron a trasladar ideas procedentes de *D&D* al ordenador.

Aquí aparecen títulos asociados a sistemas como PLATO y otros mainframes.

Entre los nombres habitualmente citados en esta etapa se encuentran:

- pedit5;
- dnd;
- *Dungeon*;
- otros experimentos universitarios hoy parcialmente perdidos o documentados de forma incompleta.

No resulta necesario para nuestro propósito determinar aquí cuál merece exactamente el título de:

> "primer CRPG de la historia".

La cuestión de prioridad depende en parte de qué características exijamos para considerar algo un RPG y de la supervivencia irregular de aquellos programas.

Lo importante es el fenómeno.

Poco después de aparecer *D&D*, diferentes programadores comenzaron a preguntarse:

> ¿Podemos hacer que el ordenador ejecute esto?

## 10. PLATO y los mundos universitarios

Los sistemas universitarios ofrecían capacidades extraordinarias para la época.

Especialmente importante fue PLATO, que proporcionaba terminales conectadas a sistemas centrales y permitió desarrollar software interactivo relativamente sofisticado.

Dentro de este entorno aparecieron experimentos que incorporaban características reconocibles del RPG:

- creación de personajes;
- estadísticas;
- experiencia;
- niveles;
- exploración;
- monstruos;
- combate;
- tesoros;
- representación visual de dungeons.

Algunos experimentaron además con capacidades multijugador.

El RPG comenzaba a separarse físicamente de la mesa.

## 11. De los mainframes al ordenador personal

La siguiente transformación fue tecnológica.

Los RPG informáticos dejaron progresivamente de depender exclusivamente de instalaciones universitarias.

La aparición y expansión de los microordenadores permitió llevar estas experiencias a hogares particulares.

Entre los títulos tempranos importantes encontramos *Beneath Apple Manor*, publicado originalmente en 1978.

Poco después aparecerían juegos que resultarían fundamentales para la consolidación comercial del género.

Entre ellos:

- *Akalabeth*;
- *Ultima*;
- *Wizardry*.

No fueron simplemente copias digitales de *D&D*.

Cada uno empezó a explorar qué podía hacer el ordenador de manera diferente.

## 12. La primera gran bifurcación

Muy pronto comenzaron a aparecer distintas formas de representar el RPG.

Podemos simplificarlas en dos tendencias tempranas.

**Mundo representado desde arriba**

```text
   @
  ###
 #...#
 #.M.#
 #...#
  ###
```

El jugador observa el espacio como mapa.

Esta línea será especialmente importante para juegos como *Ultima*.

**Mundo representado desde los ojos del personaje**

```text
┌─────────────────┐
│                 │
│     PASILLO     │
│                 │
└─────────────────┘


PARTY / COMMANDS
```

Esta línea resultará especialmente importante para *Wizardry* y posteriormente para el dungeon crawler en primera persona.

Ambas procedían del mismo problema:

> ¿Cómo representamos un mundo de RPG dentro de una pantalla?

Pero llegaron a soluciones diferentes.

## 13. Wizardry y la abstracción del dungeon

*Wizardry: Proving Grounds of the Mad Overlord*, publicado en 1981, constituye uno de los grandes hitos de esta genealogía.

El jugador controlaba una *party* que exploraba un dungeon representado en primera persona.

El espacio continuaba siendo esencialmente discreto.

```text
TURN LEFT
TURN RIGHT
MOVE FORWARD
```

La pantalla proporcionaba la ilusión de encontrarse dentro del dungeon.

Pero bajo esa representación existía una estructura lógica mucho más sencilla.

Este principio será fundamental para buena parte del dungeon crawler posterior:

> La representación puede sugerir un espacio continuo mientras la simulación permanece discretizada.

## 14. Ultima y otra dirección

La serie *Ultima* exploraría una dirección diferente.

En lugar de concentrarse exclusivamente en el dungeon como espacio cerrado, desarrollaría progresivamente:

- mundos exteriores;
- ciudades;
- exploración;
- personajes;
- conversación;
- sistemas sociales;
- narrativa;
- simulación.

La genealogía del CRPG comenzó a ramificarse.

```text
                   CRPG
                    │
        ┌─────────┴─────────┐
        │                   │
        ▼                   ▼
     WIZARDRY              ULTIMA
        │
        ▼
  DUNGEON CRAWLER
        │
        ▼
        ?
```

Esta división es deliberadamente simplificada.

Ambas ramas se influirán constantemente.

Pero resulta útil para nuestra investigación.

## 15. Del tablero a la pantalla

Podemos observar ahora una transformación completa.

El tabletop utilizaba:

- PAPEL
- LÁPIZ
- DADOS
- MAPA
- MINIATURAS
- REFEREE

El CRPG comenzó a absorber esas funciones:

```text
PAPEL          → memoria
LÁPIZ          → input
DADOS          → RNG
MAPA           → datos
MINIATURAS     → representación gráfica
TABLAS         → algoritmos
REFEREE        → software
```

No todas las equivalencias son perfectas.

Pero muestran la dirección de la transformación.

## 16. Lo que ganó el CRPG

La computerización proporcionó capacidades que acabarían modificando profundamente el género.

**Cálculo automático**

El jugador ya no necesitaba consultar constantemente tablas.

**Persistencia exacta**

El estado podía guardarse y recuperarse.

**Información oculta**

El ordenador podía conocer información que el jugador desconocía.

**Simulación automática**

Monstruos y sistemas podían ejecutarse sin intervención humana.

**Representación visual**

El mundo podía mostrarse directamente.

**Velocidad**

Numerosos procedimientos podían resolverse instantáneamente.

**Consistencia**

Una misma regla podía ejecutarse siempre exactamente de la misma manera.

## 17. Lo que perdió

La transformación también tuvo un coste.

El RPG de mesa permitía declarar prácticamente cualquier intención que el referee estuviera dispuesto a interpretar.

El CRPG temprano sólo podía responder a situaciones previstas por sus programadores.

Podemos representarlo así:

```text
TABLETOP


       imaginación
           │
           ▼
      intención libre
           │
           ▼
         referee

frente a:

CRPG


       interfaz
          │
          ▼
acciones disponibles
          │
          ▼
        código
```

El diseñador del CRPG debía decidir de antemano qué poder existir.

## 18. La restricción se convierte en diseño

Pero esta limitación produjo también una nueva disciplina.

Si el ordenador sólo puede ejecutar aquello que está representado, el diseñador debe decidir:

- ¿Qué importa?
- ¿Qué estados existen?
- ¿Qué puede hacer el jugador?
- ¿Qué constituye una entidad?
- ¿Cómo se representa el espacio?
- ¿Cómo se almacena una puerta?
- ¿Qué significa que un monstruo esté vivo?
- ¿Cómo sabe dónde puede moverse?

Estas preguntas son profundamente técnicas.

Y constituyen precisamente el territorio que RPG Archaeology pretende estudiar.

## 19. El CRPG como sistema

El RPG de mesa podía depender de interpretación.

El CRPG necesitaba estructuras explícitas.

Por ejemplo:

```text
PLAYER
│
├── position
├── stats
├── inventory
└── state


MAP
│
├── cells
├── walls
├── doors
└── objects


MONSTER
│
├── position
├── stats
├── behaviour
└── state
```

El mundo imaginario tenía que convertirse en modelo de datos.

Y después ese modelo debía convertirse en imagen, sonido e interacción.

Aquí comienza nuestra arqueología técnica.

## 20. Del género a los artefactos

A partir de este punto RPG Archaeology dejará de intentar narrar sistemáticamente toda la historia del CRPG.

La bibliografía existente ya realiza esa tarea mucho mejor.

Nuestro enfoque cambia.

Cada juego será tratado como un artefacto técnico y de diseño.

La pregunta ya no será principalmente:

> ¿Qué juego apareció después?

Sino:

> ¿Qué problema tenía este juego y cómo lo resolvieron sus desarrolladores?

## 21. Método de disección

Para cada artefacto podremos investigar, cuando exista evidencia suficiente:

- **Arquitectura del mundo**
  - ¿Cómo se representa internamente el espacio?
- **Renderer**
  - ¿Cómo se convierte esa representación en imagen?
- **Movimiento**
  - ¿Continuo, discreto, grid-based?
- **Navegación**
  - ¿Cómo saben jugador y criaturas dónde pueden desplazarse?
- **Interacción**
  - ¿Cómo se representan puertas, interruptores, objetos y mecanismos?
- **Simulación**
  - ¿Qué cambia aunque el jugador no actúe directamente?
- **Combate**
  - ¿Cómo relaciona estadísticas, espacio, tiempo y representación?
- **IA**
  - ¿Cómo perciben, deciden y navegan los enemigos?
- **Party**
  - ¿Cómo se representa un grupo dentro del espacio?
- **Authoring**
  - ¿Cómo construían mapas, criaturas y contenido?
- **Restricciones**
  - ¿Qué imponían CPU, memoria, almacenamiento y plataforma?
- **Problema → Solución**
  - ¿Qué decisión técnica permitió superar cada limitación?

## 22. Evidencia arqueológica

No siempre tendremos código fuente.

Por ello conviene distinguir diferentes niveles de evidencia.

**DOCUMENTADO**

Existe evidencia directa:

- código fuente;
- documentación técnica;
- manuales;
- herramientas;
- declaraciones contemporáneas;
- testimonios explícitos de desarrolladores.

**RECONSTRUIDO**

El comportamiento puede determinarse mediante:

- reverse engineering;
- formatos de archivo;
- emulación;
- herramientas comunitarias;
- observación sistemática.

**HIPÓTESIS**

Existe una explicación técnicamente plausible, pero todavía carecemos de evidencia suficiente.

Esta distinción será especialmente importante para evitar convertir nuestras propias deducciones en hechos históricos.

## 23. Innovar, popularizar y refinar

También debemos evitar otro problema habitual en la historia de los videojuegos.

Que un juego sea famoso por una característica no significa necesariamente que la inventara.

Por ello intentaremos distinguir:

**INTRODUJO**

> Existe evidencia razonable de que constituye una innovación original o extremadamente temprana.

**POPULARIZÓ**

> Existían precedentes, pero el juego consiguió difundir ampliamente la solución.

**REFINÓ**

> La técnica ya existía, pero fue desarrollada de una manera especialmente influyente.

**HEREDÓ**

> La solución procede claramente de juegos anteriores.

Esta clasificación será provisional y podrá cambiar conforme aparezcan nuevas evidencias.

## 24. Una genealogía técnica

Esto permitirá construir otro tipo de historia.

En lugar de:

```text
GAME A
  ↓
GAME B
  ↓
GAME C
  ↓
GAME D
```

podremos estudiar:

```text
PROBLEMA
   │
   ├── Game A → solución 1
   │
   ├── Game B → solución 2
   │
   └── Game C → solución 3
```

Por ejemplo:

> ¿Cómo representar un dungeon tridimensional?

o:

> ¿Cómo mover una party dentro de un grid?

o:

> ¿Cómo representar criaturas dentro de un espacio discreto?

o:

> ¿Cómo simular puertas, proyectiles y objetos?

Entonces juegos separados por décadas pueden compararse directamente.

## 25. La línea del dungeon crawler

Dentro de esta investigación habrá una rama de especial interés:

```text
TABLETOP DUNGEON
        │
        ▼
EARLY CRPG
        │
        ▼
    WIZARDRY
        │
        ▼
   DUNGEON MASTER
        │
        ├──────────────┐
        ▼              ▼
EYE OF THE BEHOLDER   otros
        │
        ▼
  LANDS OF LORE
        │
        ▼
    STONEKEEP
        │
        ▼
      ...
```

No debe interpretarse como una línea exclusiva ni completa de influencia.

Nos proporciona simplemente una ruta de excavación.

Otros títulos podrán entrar cuando resulten relevantes para resolver preguntas concretas.

## 26. Otros caminos

También será necesario abandonar ocasionalmente esa línea.

*Ultima Underworld*, por ejemplo, plantea preguntas completamente diferentes sobre:

- espacio continuo;
- geometría;
- física;
- interacción;
- simulación;
- representación tridimensional.

Otros juegos podrán aportar soluciones relevantes aunque no pertenezcan estrictamente al dungeon crawler tradicional.

El criterio será:

> ¿Hay algo interesante que desmontar aquí?

No completar una enciclopedia.

## 27. Síntesis arqueológica

La transición del RPG de mesa al CRPG no consistió simplemente en introducir *Dungeons & Dragons* dentro de un ordenador.

Fue una transformación del propio medio.

*D&D* había convertido una parte considerable de la fantasía en:

```text
DATOS
+
ESTADOS
+
TABLAS
+
PROCEDIMIENTOS
+
AZAR
```

El ordenador podía ejecutar todo eso extraordinariamente bien.

Lo que no podía reproducir era la capacidad abierta del referee para comprender cualquier intención.

Los primeros CRPG tuvieron por tanto que seleccionar.

Conservaron aquello que podía formalizarse.

Reducieron las acciones posibles.

Convirtieron mapas en datos.

Convirtieron dados en generadores aleatorios.

Convirtieron tablas en algoritmos.

Convirtieron personajes en estructuras de estado.

Y comenzaron a convertir al referee en software.

## 28. Genealogía provisional

Nuestro recorrido fundacional queda así:

```text
KRIEGSSPIEL
     │
     ▼
WARGAMING
     │
     ▼
BRAUNSTEIN
     │
     ▼
TWIN CITIES SCHOOL
     │
     ▼
BLACKMOOR
     │
     ├─────────────────────┐
     │                     │
     │                 CHAINMAIL
     │                     │
     └────────┬────────────┘
              ▼
     DUNGEONS & DRAGONS
            1974
              │
              ▼
    PRIMEROS EXPERIMENTOS
      INFORMÁTICOS
              │
        ┌─────┴─────┐
        ▼           ▼
     PLATO      MICROCOMPUTERS
        │           │
        └─────┬─────┘
              ▼
             CRPG
              │
        ┌─────┴─────┐
        ▼           ▼
     WIZARDRY      ULTIMA
        │
        ▼
    DUNGEON CRAWLER
        │
        ▼
        ?
```

A partir de aquí dejamos de seguir únicamente las capas históricas.

Comenzamos a abrir las máquinas.

## 29. Para continuar la excavación

Este documento no pretende ofrecer una historia exhaustiva del CRPG.

Su función dentro de RPG Archaeology es establecer el puente entre el RPG de mesa y el videojuego y explicar algunos de los problemas fundamentales que aparecieron durante esa transformación.

Para una historia mucho más amplia del género existen obras específicamente dedicadas a ello.

### Matt Barton — *Dungeons and Desktops*

La obra de Matt Barton ofrece una historia extensa de los computer role-playing games, desde sus antecedentes hasta etapas mucho más recientes.

Resulta especialmente útil para estudiar cronológicamente la evolución del género, sus desarrolladores, compañías y principales tradiciones.

La primera edición de *Dungeons and Desktops: The History of Computer Role-Playing Games* apareció en 2008.

Posteriormente se publicó una segunda edición ampliada junto con Shane Stacks.

### Felipe Pepe — *The CRPG Book*

[*The CRPG Book: A Guide to Computer Role-Playing Games*](https://www.thecrpgbook.com/), editado por Felipe Pepe, adopta una aproximación diferente.

Es una obra colectiva que recorre el género principalmente mediante juegos concretos.

Su estructura lo convierte en una excelente herramienta para:

- descubrir títulos;
- localizar influencias;
- comparar diferentes tradiciones;
- seguir la evolución del género;
- encontrar nuevos puntos de partida para investigaciones específicas.

### Alcance de RPG Archaeology

Estas obras pueden proporcionar una historia del CRPG mucho más completa de la que necesitamos reproducir aquí.

RPG Archaeology seguirá otra dirección.

No intentaremos documentar todos los RPG.

Seleccionaremos determinados juegos como artefactos y los desmontaremos para comprender:

> qué problema intentaban resolver, qué restricciones tenían, qué solución encontraron y qué dejaron a los juegos posteriores.

La cronología seguirá siendo importante.

Pero desde este punto será principalmente el contexto de la excavación.

## 30. El siguiente estrato

Hasta aquí hemos preguntado:

> ¿De dónde procede el RPG?

Después:

> ¿Cómo se convirtió en un producto reproducible?

Y finalmente:

> ¿Cómo empezó a convertirse en software?

A partir de ahora la pregunta cambia.

Tomaremos un juego.

Abriremos su mapa.

Observaremos sus datos.

Estudiaremos su renderer.

Seguiremos el movimiento de sus criaturas.

Buscáremos entrevistas, herramientas, formatos y código cuando existan.

Compararemos lo que aparenta hacer con lo que realmente hace.

Y preguntaremos:

> ¿Cómo demonios consiguieron hacer esto con aquella máquina?

Ahí empieza propiamente RPG Archaeology.