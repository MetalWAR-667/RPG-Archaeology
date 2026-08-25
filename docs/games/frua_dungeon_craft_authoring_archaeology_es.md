# Unlimited Adventures / Dungeon Craft — Arquitectura de Creación, Lecciones y Preguntas para el Futuro

> **Arqueología de RPG — Referencia de Trabajo**
>
> **Ámbito:** Forgotten Realms: Unlimited Adventures (FRUA), su modelo de creación, la fricción de la comunidad y la respuesta posterior Dungeon Craft / UAF.
>
> **Propósito:** Este documento no es una especificación para Lands of Folklore (LoF). Es una referencia arqueológica destinada a preservar preguntas de diseño, soluciones históricas, modos de fallo y posibles direcciones futuras. Su valor radica en ser revisado cuando una característica o un recorte arquitectónico de LoF plantee una pregunta similar.

------------------------------------------------------------------------

## 1. Por qué FRUA importa para la Arqueología de RPG

Forgotten Realms: Unlimited Adventures es especialmente relevante porque su objetivo es inusualmente próximo a una de las ambiciones a largo plazo detrás de LoF:

> **Darle al usuario una herramienta para crear aventuras de RPG, no un entorno de programación.**

La pregunta interesante no es tanto cómo FRUA representaba mapas, combate, inventario o reglas de AD&D. La pregunta más útil es:

> **¿Cómo exponía SSI lo suficiente de un RPG para permitir que no programadores crearan aventuras manteniendo la maquinaria del motor fuera de su camino?**

La historia posterior de la comunidad de FRUA y Dungeon Craft hace el caso aún más valioso. Proporciona algo cercano a un experimento longitudinal:

``` text
FRUA
  ↓
los usuarios crean aventuras durante años
  ↓
los usuarios descubren límites duros
  ↓
aparecen hacks y herramientas externas
  ↓
Dungeon Craft / UAF reexamina el modelo
  ↓
mayor extensibilidad
  ↓
nuevos problemas de complejidad y compatibilidad
```

Esto nos permite estudiar no solo lo que SSI diseñó, sino también **qué límites los usuarios finalmente intentaron romper**.

------------------------------------------------------------------------

## 2. Principio fundamental: el Runtime pertenece a la herramienta, no al autor

La conclusión más fuerte del estudio es sencilla:

> **El Runtime debería ser nuestro, no del usuario.**

FRUA expone conceptos que pertenecen al dominio de los RPG:

-   mapas;
-   muros;
-   bloqueos;
-   zonas;
-   encuentros;
-   misiones;
-   PNJs;
-   tiendas;
-   tesoros;
-   texto;
-   escalones;
-   teleportes;
-   eventos.

No requiere que el autor de la aventura entienda la máquina de estados interna del motor.

 Conceptualmente:

``` text
AUTOR
  │
  │ expresa su intención
  ▼
MODELO DE CREACIÓN
  │
  │ interpretado por
  ▼
RUNTIME
```

Esto nos brinda una separación útil para LoF:

``` text
CREACIÓN DE MUNDO
¿Dónde?

CREACIÓN DE EVENTOS
¿Qué sucede y cuándo?

RUNTIME
¿Cómo se ejecuta?
```

El usuario no debería necesitar conocer los internos del raycaster, la navegación, el registro de actores, las implementaciones de puertas en runtime, las tuberías de renderizado o las máquinas de estados del motor para poder decir:

> Cuando el grupo entre en esta habitación llevando la llave plateada, muestre este evento y abra esa puerta.

El Runtime sigue siendo autoritativo sobre la ejecución.

------------------------------------------------------------------------

## 3. El mapa como superficie de creación

FRUA/UAF trata al mapa como el lugar donde el autor expresa su intención espacial.

El editor expone modos de creación explícitos para conceptos como:

``` text
MURO
EVENTO
FONDO
ZONA
PUNTO DE ENTRADA
BLOQUEO
```

La lección importante no es la implementación exacta de la interfaz. Es la separación conceptual.

La misma ubicación puede participar de varios sistemas sin que esos sistemas necesiten ser la misma estructura de datos.

Una ubicación seleccionada puede decirle al autor:

``` text
Posición
Muros
Bloqueos
Zona
Fondo
Evento
```

mientras internamente esos conceptos permanecen separados.

### Lección para LoF

> **El Inspector puede agregar todo lo relevante a un lugar seleccionado sin implicar que todo pertenezca arquitectónicamente a StructuralCell.**

Esta distinción se vuelve cada vez más importante a medida que crece la creación.

------------------------------------------------------------------------

## 4. Muros, bloqueos y el valor de StructuralEdge

FRUA/UAF separa el muro visual de su semántica de bloqueo.

Conceptualmente, un lado de una celda puede contener varias dimensiones:

``` text
apariencia
+
pasabilidad
+
condición de interacción
```

Los tipos de bloqueo pueden expresar conceptos como:

``` text
ABIERTO
SECRETO
BLOQUEADO
PUERTA FALSA
CERRADO CON LLAVE
CERRADO PARA HECHICEROS
CERRADO CON LLAVE ESPECÍFICA
...
```

Esto es útil porque demuestra que:

> **Lo que una frontera parece y lo que esa frontera permite no son necesariamente la misma propiedad.**

FRUA/UAF almacena información de muros direccionales por celda y, por tanto, necesita lógica de editor para mantener el lado opuesto de la celda vecina.

Conceptualmente:

``` text
Celda A ESTE
    ║
Celda B OESTE
```

Si uno cambia, la herramienta puede necesitar actualizar el otro.

El `StructuralEdge` explícito de LoF resuelve el mismo problema de creación a nivel de modelo:

``` text
      StructuralEdge
         /       \
     Celda A     Celda B
```

En lugar de mantener dos copias conceptuales de una misma frontera, LoF puede representar la frontera en sí.

### Valor arqueológico

Esto nos brinda un ejemplo histórico concreto del problema que `StructuralEdge` elimina.

------------------------------------------------------------------------

## 5. Zonas: contexto compartido por encima de celdas individuales

Uno de los conceptos más útiles de FRUA/UAF es la **Zona**.

Una zona puede proporcionar propiedades contextuales compartidas a un área en lugar de forzar a cada celda individual a repetirlas.

Los conceptos potenciales a nivel de zona incluyen:

-   comportamiento ambiental;
-   comportamiento de descanso;
-   reglas ambientales;
-   contexto de combate;
-   restricciones mágicas;
-   sonidos;
-   modificadores locales;
-   valores por defecto de presentación.

Conceptualmente:

``` text
CELDA
  ↓ pertenece a
ZONA / REGIÓN
  ↓ suministra contexto compartido
```

### Pregunta futura para LoF

Cuando crezca la creación ambiental, preguntar:

> **¿Qué propiedades pertenecen realmente a Cell/Edge, y cuáles deberían pertenecer a una Región/Zona compartida?**

Posibles responsabilidades futuras de la región de LoF podrían incluir:

``` text
ambiente
perfil de iluminación
música
reglas de encuentro
reglas de descanso
estado ambiental
modificadores mágicos
valores por defecto de presentación local
```

Esta es una pregunta para el diseño futuro, no para un requisito de implementación.

------------------------------------------------------------------------

## 6. Creación de eventos: programar sin presentar programación

Esta probablemente es la lección de creación más importante de FRUA.

El autor no se le presenta con primitivas de programación genéricas como:

``` text
variable
función
retrollamada
entero
ramificación
```

En lugar de ello, el vocabulario se expresa en conceptos de RPG:

``` text
COMBATE
TEXTO
DAR TESORO
Misión
Tienda
Escalera
Teletransporte
AGREGAR PNJ
DAÑO
Templo
ENCUENTRO
```

El usuario está construyendo lógica, pero la herramienta habla el idioma del diseño de aventuras.

### Modelo núcleo

El modelo esencial puede reducirse a:

``` text
DISPARADOR
   ↓
EVENTO
   ↓
CADENA
```

Las condiciones pueden representar conceptos como:

``` text
el grupo tiene el objeto
el grupo carece del objeto
estado de la misión
condición de tiempo/día
probabilidad
composición del grupo
posición
dirección
tecla especial
presencia de PNJ
...
```

El resultado es lógicamente equivalente a la programación:

``` text
SI el grupo tiene la Llave de Rubí
ENTONCES mostrar texto
SINO hacer otra cosa
```

pero el autor lo experimenta como creación de RPG.

------------------------------------------------------------------------

## 7. Las cadenas de eventos como un lenguaje específico de dominio

Los eventos pueden dar lugar a más eventos.

Al nivel más simple:

``` text
EVENTO A
   │
   ├── ocurrió ─────→ EVENTO B
   │
   └── no ocurrió ─→ EVENTO C
```

Algunos tipos de eventos pueden exponer múltiples salidas semánticas:

``` text
                 ┌─ LUCHAR ─→ Combate
                 │
Encuentro ───────┼─ HABLAR ──→ Diálogo
                 │
                 └─ HUIR ──→ Escape
```

Esto es efectivamente un pequeño **lenguaje de programación específico de dominio para aventuras de RPG**.

La diferencia crucial respecto a la programación visual genérica es que cada evento de nivel alto ya entiende su dominio.

Un evento de contraseña sabe lo que significa una contraseña.

Un evento de encuentro sabe lo que significan los resultados de un encuentro.

Un evento de tesoro sabe lo que significa dar un tesoro.

### Lección

> **El poder no requiere primitivas infinitamente genéricas. Un vocabulario finito de conceptos de RPG suficientemente expresivos puede producir una flexibilidad combinatoria enorme.**

Esto debería seguir siendo un punto de referencia importante si LoF eventualmente desarrollara creación de eventos.

------------------------------------------------------------------------

## 8. La colocación no es comportamiento

FRUA/UAF nos brinda una separación útil:

``` text
UBICACIÓN EN EL MAPA
     │
     └── el evento existe aquí
              │
              ▼
          DATOS DEL EVENTO
              │
              ▼
          CADENA DE EVENTOS
```

La estructura espacial identifica **dónde** comienza la lógica.

El sistema de eventos define **qué** sucede.

El Runtime sabe **cómo** realizarlo.

Para LoF:

> **El mapa no debería convertirse en el lenguaje de secuencias.**

Una celda, arista, instancia colocada o región puede referenciar comportamiento sin necesidad de contener la implementación de ese comportamiento.

------------------------------------------------------------------------

## 9. Las restricciones de creación no son restricciones de Runtime

FRUA/UAF también demuestra que un editor no necesita obedecer cada restricción de juego.

Por ejemplo, una herramienta de creación puede permitir el movimiento a través de geometría que bloquearía al jugador.

El principio general es:

> **Las restricciones del Runtime no necesitan ser automáticamente restricciones de la herramienta de creación.**

Esto es relevante para el DRP de LoF y para las futuras herramientas de vista previa/navegación.

El autor puede necesitar operaciones privilegiadas que el jugador final nunca recibe.

------------------------------------------------------------------------

## 10. Validación antes de la ejecución

Una herramienta de creación madura necesita razonar sobre el contenido sin requerir que el usuario descubra cada problema durante el juego.

FRUA/UAF expone conceptos como:

``` text
Validar Diseño
Probar Diseño Guardado
Probar Diseño Guardado Desde el Inicio
Registro de Errores
```

Esto establece varias preguntas distintas:

``` text
¿El diseño es estructuralmente válido?

¿Esta situación local funciona?

¿La aventura funciona desde el principio?

¿Qué salió mal?
```

### Paralelo para LoF

La arquitectura de Compilador/Diagnósticos de LoF nos brinda un lugar natural para esto:

``` text
DocumentoEditado
      ↓
Validación / Compilador
      ↓
Diagnósticos
      ↓
Runtime
```

La lección de UX más importante es que los diagnósticos deben usar **vocabulario de creación**.

Mal:

``` text
Referencia nula en el manejador de eventos en runtime.
```

Mejor:

``` text
Este evento transfiere el grupo a un mapa que ya no existe.
```

------------------------------------------------------------------------

## 11. Vista previa no es prueba de juego

La distinción de FRUA/UAF entre probar localmente y probar desde el principio se asigna bien a una futura distinción en LoF.

### Iteración local

``` text
editar
  ↓
probar aquí
  ↓
observar
  ↓
regresar
  ↓
editar
```

Esto responde preguntas como:

> ¿Esta puerta se compila correctamente?

> ¿Esta apariencia se muestra correctamente?

> ¿Se activa este evento?

### Pruebas de integración

``` text
comenzar la aventura
  ↓
acumular estado
  ↓
viajar
  ↓
completar eventos anteriores
  ↓
llegar al objetivo
  ↓
verificar el comportamiento
```

Esto responde:

> ¿Esta puerta se abre después de completar la misión y llegar desde el mapa anterior?

### Implicación para LoF

``` text
DocumentoEditado
      ↓
Compilador
   ┌─┴───────────────┐
   ↓                 ↓
DRP / Vista Previa    Runtime Completo
prueba local          prueba de integración
```

> **Vista previa y prueba de juego son herramientas relacionadas pero distintas.**

------------------------------------------------------------------------

## 12. Grupo de Prueba / Contexto de Prueba

El RPG tiene un problema especial de prueba: el resultado a menudo depende del estado acumulado.

Un sistema de prueba útil eventualmente puede necesitar definir un contexto inicial reproducible:

``` text
Generar:
  mapa
  posición
  orientación

Grupo:
  preestablecido

Inventario:
  preestablecido

Estado del Mundo:
  preestablecido

Estado de Misiones:
  preestablecido
```

Esto evita que el diseñador tenga que jugar grandes secciones de la aventura solo para reproducir una condición.

### Semilla futura para LoF

Una capa futura de DRP o prueba podría soportar **Contextos de Prueba preestablecidos**.

No es un requisito inmediato.

------------------------------------------------------------------------

## 13. Módulo, Diseño y Campaña

Una distinción útil emerjo de FRUA.

### Módulo

Un módulo es esencialmente una unidad espacial jugable: mazmorra, zona de mundo, etc.

Los módulos pueden conectarse mediante transferencias.

``` text
Aldea
   ↓
Tierra Salvaje
   ↓
Castillo
   ↓
Mazmorra
```

### Diseño de Aventura

El diseño distribuido es la aventura completa escrita que contiene sus módulos y contenido de apoyo.

Conceptualmente:

``` text
DISEÑO DE AVENTURA
├── contenido global
├── misiones
├── PNJs
├── objetos
├── módulo 01
├── módulo 02
├── módulo 03
└── ...
```

### Campaña

FRUA podía representar una larga campaña **dentro de un único Diseño lo suficientemente grande**, pero no ofrecía una abstracción de campaña de orden superior particularmente rica que coordinara aventuras independientes.

Esto expone una pregunta útil para el futuro de LoF:

``` text
MOTOR
  ↓
PROYECTO / CAMPAÑA?
  ↓
AVENTURA?
  ↓
MAPA
  ↓
REGIÓN
  ↓
CELDA / ARISTA / INSTANCIA
  ↓
EVENTO
```

No necesitamos **crear** todos los niveles de esta jerarquía.

La pregunta es:

> **¿Cuál es la unidad editorial explícita más grande que LoF realmente necesita?**

`campaign.gd` permanece prohibido hasta que Elminster conceda permiso por escrito.

------------------------------------------------------------------------

## 14. ¿Qué frustró a los usuarios de FRUA?

Esto puede ser más informativo que estudiar lo que funcionó.

Las frustraciones pueden separarse en varias clases.

### 14.1 Límites cuantitativos

Ejemplos históricos incluían limitaciones tales como:

-   dimensiones del mapa;
-   eventos;
-   conjuntos de muros;
-   fondos;
-   imágenes;
-   entradas de monstruos;
-   capacidad de texto.

La lección es sencilla:

> **No convirtas accidentalmente un límite de implementación en una regla conceptual del modelo de creación.**

Si LoF actualmente prefiere un cierto tamaño de mapa por razones técnicas, eso no significa necesariamente que la definición conceptual de `Mapa` deba codificar permanentemente ese número.

------------------------------------------------------------------------

## 15. La frustración más importante: constantes que los usuarios consideraban contenido

FRUA permitía una extensa creación de aventuras pero mantenía muchos conceptos de juego comparativamente fijos.

La comunidad eventualmente quiso mayor control sobre:

``` text
clases
razas
objetos
hechizos
monstruos
reglas
presentación
```

Esto produjo hacks y herramientas externas.

La lección importante es:

> **La privacidad del Runtime no implica que los datos del juego deban ser privados para los autores.**

Hay una distinción fundamental entre:

``` text
CÓDIGO QUE HACE FUNCIONAR EL JUEGO
```

y:

``` text
DATOS QUE DEFINEN ESTE JUEGO/AVENTURA PARTICULAR
```

Las preguntas futuras de LoF incluyen, por tanto:

``` text
Clases            ¿creables por el autor?
Razas            ¿creables por el autor?
Objetos           ¿creables por el autor?
Hechizos          ¿creables por el autor?
Criaturas         ¿creables por el autor?
Efectos de Estado ¿creables por el autor?
Reglas            ¿creables por el autor?
```

Cada límite debería ser deliberado.

------------------------------------------------------------------------

## 16. La respuesta de Dungeon Craft: mover el límite

Dungeon Craft no descartó la filosofía fundamental de creación de FRUA.

En su lugar, hizo más conceptos creables y aumentó dramáticamente los viejos límites de implementación.

Su respuesta puede resumirse como:

``` text
FRUA
Mapas / Eventos / Contenido de Aventura
        ↓
muchas definiciones de juego fijas

Dungeon Craft
Mapas / Eventos / Contenido de Aventura
        +
Objetos / Monstruos / Hechizos
Clases / Razas / Habilidades
        +
mayor secuenciación/extensibilidad
```

Esto resolvió restricciones creativas reales.

Pero introdujo un nuevo costo.

------------------------------------------------------------------------

## 17. La extensibilidad tiene un precio

A medida que más del juego se vuelve configurable, el autor debe entender más del juego.

una progresión conceptual útil es:

### Capa 1 — Creación de Aventuras

``` text
mapas
eventos
misiones
encuentros
PNJs
tesoros
estado del mundo
```

### Capa 2 — Creación de Contenido de Juego

``` text
objetos
criaturas
clases
razas
hechizos
habilidades
```

### Capa 3 — Creación de Reglas / Modificación

``` text
fórmulas
secuencias
comportamiento
mecánicas del sistema
extensiones de runtime
```

Estas capas no necesitan exponerse simultáneamente.

### Lección para LoF

> **La extensibilidad debería crecer en capas en lugar de destruir la simple abstracción que encuentra un nuevo autor.**

Un creador debería poder hacer una aventura completa sin necesidad de entender primero cómo funciona la base de datos de hechizos o las fórmulas de combate.

------------------------------------------------------------------------

## 18. Editor Progresivo — semilla generada por este estudio

Esta investigación produjo un posible concepto de UX para LoF.

Un modo de fallo común de herramientas poderosas es la reacción al primer lanzamiento:

> "¿Qué diablos es este panel de control empresarial?"

Esto es especialmente relevante para usuarios más jóvenes o creadores no familiarizados con herramientas clásicas de construcción de RPG.

### Posible solución: divulgación progresiva a través del dominio

El Editor podría comenzar opcionalmente con una superficie de herramientas reducida:

``` text
PRINCIPIANTE

Celda
Muro
Puerta
Objeto
Probar
```

A medida que el autor usa y prueba con éxito conceptos, nuevas herramientas se hacen visibles:

``` text
Zonas
Eventos
Transferencias
Condiciones
Cadenas de Eventos
Creación de Criaturas
Creación de Objetos
...
```

La progresión podría comportarse casi como logros.

Sin embargo:

> **Esto nunca debe ser una barrera de capacidad.**

Configuraciones posibles:

``` text
Experiencia del Editor

Progresiva
Estándar
Completa / Experto
```

Un comando de consola o ajuste podría desbloquear todo inmediatamente.

### Propiedad arquitectónica importante

El formato del proyecto permanece idéntico.

``` text
Modo Progresivo
Modo Estándar
Modo Experto
       │
       ▼
mismo modelo de creación
mismos datos
mismo runtime
```

Solo cambia la superficie de UX visible.

### Dominio del autor persistente

La progresión probablemente debería pertenecer al **autor**, no a cada proyecto.

Una vez que alguien ha demostrado familiaridad con Eventos, un nuevo proyecto no debería ocultar Eventos nuevamente.

Esto convierte la progresión en una capa pedagógica sobre el editor más que en un molienda estilo juego.

------------------------------------------------------------------------

## 19. Los Recursos de Godot pueden eliminar la necesidad de bases de datos al estilo Dungeon Craft

Dungeon Craft necesitaba bases de datos explícitas para clases, monstruos, objetos, hechizos y definiciones relacionadas.

LoF se está construyendo sobre Godot y ya usa creación basada en Recursos.

Por lo tanto, no deberíamos copiar la implementación histórica cuando la plataforma moderna ya resuelve el problema subyacente.

Un posible modelo de LoF:

``` text
res://users/
├── creatures/
├── items/
├── spells/
├── classes/
└── ...
```

Cada definición creada puede ser un Recurso `.tres`.

El Recurso mismo puede permanecer como fuente de verdad.

### Evitar paralelos de verdad

Evitar crear innecesariamente:

``` text
bronze_sword.tres
        +
items.json
        +
fila de base de datos
```

a menos que un requisito futuro concreto lo justifique.

De lo contrario, el sistema crea inmediatamente la pregunta:

> ¿Qué representación es autoritativa?

### Mejor enfoque

``` text
Recursos
   ↓
escanear / indexar
   ↓
registro derivado
```

Los índices y cachés pueden existir por razones de rendimiento o búsqueda, pero no necesitan convertirse en una base de datos adicional mantenida por el usuario.

------------------------------------------------------------------------

## 20. El contenido central y el contenido del usuario pueden compartir los mismos contratos

Una organización útil futura podría distinguir conceptualmente:

``` text
core/
  contenido oficial/predeterminado de LoF

users/
  contenido creado por usuarios
```

Pero el Runtime debería consumir idealmente los mismos tipos de recurso semántico:

``` text
CreatureDefinition
ItemDefinition
SpellDefinition
...
```

La distinción es procedencia, no tipo.

Una criatura personalizada no debería requerir una ruta de runtime completamente separada solo porque fue creada por un usuario.

------------------------------------------------------------------------

## 21. La identidad estable importa más que la posición de la base de datos

Dungeon Craft también expone un problema de portabilidad: una vez que diferentes diseños pueden redefinir sus bases de datos, las IDs posicionales se vuelven peligrosas.

Conceptualmente:

``` text
Diseño A
Objeto #37 = Espada Larga

Diseño B
Objeto #37 = Rata Muerta
```

Una referencia transferida a `37` ya no es semánticamente estable.

LoF debería preferir eventualmente identidad de recurso estable sobre posición accidental.

Mecanismos posibles incluyen:

``` text
ID estable
nombre calificado
UUID
identidad de recurso
```

La solución exacta es trabajo futuro.

La pregunta importante es:

> **¿Cómo mantiene un recurso su identidad cuando se mueve, se reutiliza, se empaqueta o se transfiere entre proyectos?**

------------------------------------------------------------------------

## 22. Biblioteca de creación versus aventura distribuida

Una de las conclusiones más fuertes de la comparación con Dungeon Craft.

La biblioteca de trabajo del autor y la aventura distribuida del jugador no necesitan ser la misma cosa.

### Creación

``` text
users/
├── cientos de criaturas
├── cientos de objetos
├── biblioteca personal de arte
├── definiciones de hechizos
└── plantillas reutilizables
```

Una aventura puede usar solo un subconjunto.

### Empaquetado

Cuando se publica:

``` text
Aventura
   ↓
Exploración de Dependencias
   ↓
Recursos Requeridos
   ↓
Empaquetar
```

Resultado:

``` text
PAQUETE DE AVENTURA
├── manifiesto
├── mapas
├── eventos
├── recursos requeridos
└── assets requeridos
```

### Principio

> **Reutilizable durante la creación; autocontenida cuando se publica.**

Esto evita convertir la instalación del jugador en un ejercicio de gestión de dependencias.

------------------------------------------------------------------------

## 23. Evitar "npm para mazmorras"

La alternativa peligrosa es dependencias externas permanentes:

``` text
Aventura
  requiere:
    ItemLibrary 2.7
    SpellPack 4.2
    MetalCreatures 1.9
```

Esto eventualmente produce:

-   conflictos de versiones;
-   dependencias faltantes;
-   actualizaciones incompatibles;
-   cadenas de dependencias de Workshop;
-   aventuras no reproducibles.

LoF debería considerar seriamente que la aventura publicada sea un **instantánea de los recursos que realmente requiere**, en lugar de asumir que la biblioteca de creación del jugador coincide con la del creador.

Esto no prohibía bibliotecas reutilizables durante el desarrollo.

Separa **reutilización durante la creación** de **reproducibilidad en la distribución**.

------------------------------------------------------------------------

## 24. Implicaciones para Steam Workshop

Esta arquitectura se asigna de forma natural a un futuro modelo de Workshop.

``` text
BIBLIOTECA DE CREACIÓN
       ↓
La aventura usa recursos seleccionados
       ↓
EXPORTAR / EMPAQUETAR
       ↓
Paquete de Aventura
       ↓
Steam Workshop
       ↓
El jugador se suscribe
       ↓
El Runtime carga el paquete
```

El jugador no debería necesariamente:

-   la carpeta `users/` del autor;
-   bases de datos externas;
-   instalación manual de assets;
-   conocimiento de internos del editor.

Un pequeño manifiesto podría proporcionar metadatos de distribución como:

``` text
ID de Aventura
Versión
Autor
Versión mínima compatible de LoF
Hash del paquete
```

El manifiesto describiría el paquete; no necesitaría convertirse en la fuente de verdad para el contenido creado del juego.

------------------------------------------------------------------------

## 25. Una implicación multijugador muy distante

El multijugador no es explícitamente un requisito activo de LoF. Si alguna vez existe, sería una adición posterior al desarrollo dependiente de energía restante, audiencia y valor práctico.

Sin embargo, buenas decisiones de empaquetado hoy pueden evitar obstáculos innecesarios más adelante.

Una aventura autocontenida da a los pares multijugador una pregunta de compatibilidad simple:

``` text
ID de Aventura
Versión
Hash del Paquete
```

Antes de una sesión:

``` text
ANFITRIÓN
Aventura: crypt_of_berzo
Versión: 1.4
Hash: ABC123

CLIENTE
¿Tengo ABC123?

SÍ → unirse

NO → obtener paquete → verificar → unirse
```

Ambos lados ya poseen definiciones estáticas idénticas:

``` text
mapas
eventos
criaturas
objetos
assets
reglas expuestas por el paquete
```

El multijugador puede entonces enfocarse más en sincronizar **estado**.

------------------------------------------------------------------------

## 26. El estado del mundo discreto es amigable para multijugador

LoF ya favorece muchos estados del mundo discretos sobre una simulación física completa.

Ejemplos:

``` text
ABIERTO / CERRADO
VIVO / MUERTO
ACTIVO / INACTIVO
ILUMINADO / NO ILUMINADO
EN LLAMAS / APAGADO
ARRIBA / ABAJO
```

Un sistema multijugador hipotético podría sincronizar estados como:

``` text
posición del grupo
orientación del grupo
estado de la puerta
estado del enemigo
estado del botín
estado de la misión
estado de turno/acción
```

en lugar de intentar sincronizar una gran simulación física continua.

Esto no hace el multijugador trivial.

Solo significa que el modelo conceptual actual no es inherentemente hostil a un futuro enfoque de sincronización de estados.

------------------------------------------------------------------------

## 27. La cooperación lenta es un espacio de diseño futuro legítimo

Si el multijugador alguna vez se explorara, no tendría necesariamente imitar juegos de acción en tiempo real.

Hay audiencias establecidas para la toma de decisiones multijugador lenta en juegos de estrategia y experiencias similares a juegos de mesa.

Una crawler de mazmorra podría teóricamente explorar:

``` text
Acción del Jugador A
      ↓
el mundo resuelve
      ↓
Acción del Jugador B
      ↓
el mundo resuelve
```

u otros modelos por turnos o de barrera.

Esto no es una propuesta de característica.

Es simplemente un recordatorio:

> **"Multijugador" no implica "multijugador en tiempo real rápido".**

Si el modelo sistémico y basado en estados de LoF alguna vez hiciera atractiva la exploración colaborativa lenta de mazmorras, el espacio de diseño existe.

------------------------------------------------------------------------

## 28. Tres productos distintos

Una abstracción final útil es tratar el ecosistema futuro como tres responsabilidades:

``` text
HERRAMIENTA DE CREACIÓN
Editor de LoF

      ↓ produce

PRODUCTO DE CONTENIDO
Paquete de Aventura / Campaña

      ↓ consumido por

PRODUCTO DE EJECUCIÓN
Runtime de LoF
```

Pueden compartir código y tecnología, pero sirven a usuarios y responsabilidades distintas.

### Editor

Optimizado para creadores.

### Paquete de Aventura

Portátil, versionable, contenido creado reproducible.

### Runtime

Interpreta y ejecuta el paquete.

Esta distinción es especialmente valiosa para:

-   Workshop;
-   distribución independiente;
-   validación;
-   compatibilidad;
-   multijugador;
-   contenido comunitario futuro.

------------------------------------------------------------------------

# 29. Preguntas a hacer de nuevo cuando LoF alcance los recortes pertinentes

Este documento debería revisarse cuando cualquiera de los siguientes sistemas se convierta en trabajo activo.

### Creación de Eventos

-   ¿Cuál es el vocabulario de dominio más pequeño útil?
-   ¿Puede el autor expresar intención de RPG sin secuenciación genérica?
-   ¿Qué condiciones y salidas de eventos merecen conceptos de primer nivel?
-   ¿Dónde debe vivir el estado del evento?
-   ¿Cómo mantenemos la colocación separada del comportamiento?

### Regiones / Zonas

-   ¿Qué propiedades se repiten lo suficiente como para merecer herencia/contexto regional?
-   ¿Qué pertenece a Cell/Edge y qué pertenece encima de ellos?
-   ¿Son las zonas puramente editoriales, relevantes para el runtime, o ambas?

### Pruebas

-   ¿Qué es responsable Vista Previa?
-   ¿Qué requiere Runtime Completo?
-   ¿Necesitamos "Probar Desde Aquí" y "Probar Desde el Inicio"?
-   ¿Necesitamos contextos de prueba preestablecidos reproducibles?

### Creación de Contenido de Juego

-   ¿Qué definiciones deberían poder crear los usuarios?
-   ¿Objetos?
-   ¿Criaturas?
-   ¿Clases?
-   ¿Razas?
-   ¿Hechizos?
-   ¿Efectos de estado?
-   ¿Reglas?

### Complejidad de UX

-   ¿Deberían aparecer los sistemas avanzados progresivamente?
-   ¿Es útil un modo Progresivo / Estándar / Completo?
-   ¿Qué conceptos constituyen un dominio significativo del autor?

### Identidad de Recursos

-   ¿Cómo mantiene un recurso creado su identidad estable?
-   ¿Puede moverse de forma segura entre proyectos?
-   ¿Qué sucede cuando dos paquetes contienen recursos con el mismo nombre legible por humanos?
-   ¿Necesitamos IDs calificados o UUIDs?

### Empaquetado

-   ¿Cuál es la unidad de distribución?
-   ¿El exportado captura instantáneas de todos los Recursos requeridos?
-   ¿Cómo se descubren las dependencias?
-   ¿Cómo se diagnosticen los recursos faltantes?
-   ¿Qué pertenece al manifiesto?
-   ¿Cómo se versiona la compatibilidad del paquete?

### Workshop

-   ¿Puede suscribirse ser equivalente a "instalar y jugar"?
-   ¿Puede un paquete reproducirse independientemente de la carpeta `users/` del autor?
-   ¿Cómo se manejan las actualizaciones y guardados entre versiones del paquete?

### Campaña

-   ¿LoF eventualmente necesita una abstracción explícita de Campaña?
-   ¿Es Aventura suficiente?
-   ¿Qué estado, si alguno, persiste por encima de una Aventura?
-   ¿Pueden aventuras independientes compartir un grupo o estado del mundo sin acoplamiento frágil?

### Multijugador — solo si alguna vez es relevante

-   ¿Pueden ambos pares verificar un Paquete de Aventura idéntico?
-   ¿Qué estado es autoritativo?
-   ¿Qué estados deben sincronizarse?
-   ¿Puede el modelo discreto del mundo soportar colaboración lenta/por turnos de forma limpia?
-   ¿Requiere multijugador cambios en el contenido creado, o solo en la ejecución?

------------------------------------------------------------------------

# 30. Principios de trabajo extraídos del estudio

Estos no son decisiones arquitectónicas vinculantes de LoF. Son los principios de trabajo más fuertes producidos por la comparación arqueológica.

> **1. El Runtime pertenece a LoF; el autor debería expresar intención, no implementación.**

> **2. La privacidad del Runtime no implica que el contenido del juego deba estar cerrado para los autores.**

> **3. La colocación del mundo, el comportamiento de eventos y la ejecución del runtime son responsabilidades separadas.**

> **4. Habla el idioma del diseño de RPG en lugar de exponer conceptos de programación genéricos innecesariamente.**

> **5. Un vocabulario finito de eventos específicos de dominio puede ser extremadamente potente mediante composición.**

> **6. Vista previa y prueba completa resuelven problemas distintos.**

> **7. Probar un RPG eventualmente requiere estado del mundo/grupo reproducible.**

> **8. Los límites de implementación no deben convertirse silenciosamente en límites conceptuales permanentes.**

> **9. La extensibilidad debería estar en capas. Más potencia no debería forzar más complejidad a cada autor.**

> **10. La divulgación progresiva podría permitir que un único editor sirva a principiantes y expertos sin mantener modelos de datos separados.**

> **11. Los Recursos de Godot pueden servir como datos creados; no introduzcas paralelos JSON/bases de datos sin una razón concreta.**

> **12. Los registros e índices pueden derivarse de los Recursos en lugar de convertirse en fuentes de verdad rivales.**

> **13. La identidad estable importa cuando los recursos se vuelven reutilizables, portátiles o distribuibles.**

> **14. La biblioteca de creación del usuario y la aventura publicada deberían ser conceptos separados.**

> **15. Reutiliza durante la creación; empaqueta contenido autocontenida para la distribución.**

> **16. Workshop no debería convertirse en gestión de dependencias para el jugador.**

> **17. Un Paquete de Aventura reproducible y versionado también crea una base limpia para cualquier experimento de red muy distante.**

> **18. No implementes sistemas especulativos solo porque la arquitectura deja espacio para ellos. Preserva opciones sin pagar su costo de complejidad temprano.**

------------------------------------------------------------------------

# 31. Síntesis arqueológica final

El gran logro de FRUA no fue la flexibilidad ilimitada.

Fue **abstracción controlada**.

SSI permitió a un diseñador de aventuras pensar en términos de:

``` text
mapas
habitaciones
muros
eventos
misiones
encuentros
PNJs
tesoros
```

mientras mantenía el motor debajo de la mesa.

La comunidad eventualmente demostró la debilidad de ese modelo: algunos conceptos que SSI consideraba implementación fija eran conceptos que los autores consideraban parte de su lenguaje creativo.

Dungeon Craft respondió abriendo mucho más del sistema:

``` text
objetos
hechizos
clases
razas
habilidades
reglas
secuenciación
```

y, como consecuencia, descubrió el problema opuesto:

> **La libertad crea complejidad.**

Para LoF, el objetivo útil, por tanto, no es ni "FRUA pero cerrado" ni "todo expuesto como un SDK".

La dirección más prometedora es:

``` text
superficie de creación de RPG simple
          ↓
creación de contenido más profunda opcional
          ↓
creación avanzada/sistema opcional
══════════════════════════════════
        Runtime boundary
══════════════════════════════════
          ↓
      Ejecución de LoF
```

con los Recursos de Godot proporcionando datos creados, el Compilador proporcionando validación y traducción, y el empaquetado futuro convirtiendo el entorno de trabajo de un creador en un Paquete de Aventura autocontenido.

La pregunta central a preservar es:

> **¿Cuánta lenguaje creativo podemos dar al autor manteniendo la maquinaria que ejecuta ese lenguaje fuera de su camino?**

Esa es la pregunta que FRUA planteó en 1993.

Dungeon Craft muestra lo que sucedió cuando los usuarios respondieron:

> **Más.**

Y eso es exactamente por qué ambos pertenecen juntos en la Arqueología de RPG.