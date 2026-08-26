# The Dark Heart of Uukrul — Informe de autopsia técnica
## Eriosthé, formato de mapas, transiciones, saves y mecanismo de integridad

**Proyecto:** RPG Archaeology  
**Fecha de cierre provisional:** 23-08-2026  
**Estado:** investigación cerrada provisionalmente con varios subsistemas confirmados y un último mecanismo de validación pendiente de resolución exacta.

---

<figure markdown>
![The Dark Heart of Uukrul — Informe técnico — RPG Archaeology](../assets/images/games/uukrul/Uukrul_RPG_Archaeology_YouTube_Thumbnail.png)
</figure>

[▶ Ver episodio en YouTube](YOUTUBE_VIDEO_URL)

## 1. Resumen ejecutivo

La investigación empezó con una pregunta relativamente modesta: **¿podemos reconstruir los mapas de Eriosthé a partir de los datos originales de The Dark Heart of Uukrul?**

La respuesta es sí.

A partir de los archivos `MZ*.LEV`, del ejecutable, de `UUKRUL.LIB`, `UUKRUL.OVR`, tablas auxiliares, saves controlados y sesiones de depuración en DOSBox-X, se reconstruyó con bastante precisión:

- el formato físico de los niveles;
- la topología exacta de paredes;
- la representación de posición y orientación en runtime;
- la separación entre estado de grupo (`.GMI`) y snapshot del mundo (`.IMG`);
- parte de la arquitectura de teleports/transiciones;
- un parser/editor seguro de saves;
- y la existencia de un mecanismo real de integridad/anti-tamper que rechaza modificaciones aparentemente válidas.

No se consiguió cerrar por completo:

- la enumeración exacta de todas las transiciones MZ→MZ;
- la semántica completa del bytecode de eventos de los `.LEV`;
- ni el algoritmo final que hace que un save modificado sea rechazado.

La investigación sí consiguió localizar el último tramo relevante de ese validador y llegó a instrumentar el ejecutable para intentar observar el valor devuelto en runtime.

---

## 2. Filosofía de la autopsia

La regla operativa fue distinguir siempre entre:

- **CONFIRMED** — demostrado por código, datos o runtime;
- **STRONG** — inferencia muy apoyada;
- **SPECULATIVE** — hipótesis útil pero no cerrada;
- **UNKNOWN** — no resuelto;
- **RETRACTED** — interpretación anterior corregida por nueva evidencia.

Este criterio resultó especialmente importante porque varias hipótesis tempranas fueron descartadas más tarde. La investigación no debe leerse como una cadena lineal de descubrimientos perfectos, sino como una sucesión de hipótesis, pruebas, correcciones y cortes.

---

# PARTE I — ERIOSTHÉ

## 3. Formato de los archivos MZ*.LEV

Los niveles estudiados usan archivos `.LEV` de **1024 bytes exactos**.

Estructura reconstruida:

| Offset | Tamaño | Significado | Confianza |
|---|---:|---|---|
| `0x000` | `0x320` | geometría | CONFIRMED |
| `0x320` | `0xE0` | bloque de eventos/script | STRONG |
| total | `0x400` | archivo completo | CONFIRMED |

La geometría ocupa:

- 25 filas;
- 16 columnas;
- 2 bytes por celda.

Fórmula de acceso:

```text
cell = DS:0x3816 + Y*0x20 + X*2
```

con:

```text
Y = 0..24
X = 0..15
```

El loader copia el archivo completo al buffer de nivel en runtime.

---

## 4. Topología bit-exact de paredes

El byte bajo de cada celda contiene los cuatro muros cardinales:

```text
0x01 = EAST
0x04 = SOUTH
0x10 = WEST
0x40 = NORTH
```

Semántica:

```text
bit set = muro / bloqueado
```

Los cuatro bits restantes del byte bajo:

```text
0x02
0x08
0x20
0x80
```

son flags especiales cuya semántica completa no quedó resuelta.

El byte alto funciona como tipo/feature adicional, también preservado como desconocido cuando no existía evidencia suficiente.

La asignación cardinal se sostuvo mediante varias señales independientes:

- redundancia entre lados opuestos de celdas vecinas;
- simetría estadística;
- conectividad global;
- y correspondencia con el código de LOS/movimiento.

El runtime usa direcciones:

```text
dir0 = +X = EAST
dir1 = +Y = SOUTH
dir2 = -X = WEST
dir3 = -Y = NORTH
```

---

## 5. Filosofía espacial del motor

No existe evidencia de arrays independientes de edges N/S y E/W.

La topología está codificada **por celda**.

Movimiento y LOS consultan el estado direccional de una celda mediante helpers del motor. El resultado no-cero equivale a dirección bloqueada.

Esto produce una representación muy compacta:

```text
2 bytes/celda
→ conectividad
→ tipo/feature
→ flags especiales
```

Una conclusión interesante para diseño: los mapas son pequeños en dimensiones absolutas, pero cada celda puede contener bastante información jugable.

---

## 6. Bloque de eventos de los LEV

Los últimos 224 bytes muestran comportamiento estructurado y no parecen relleno.

Se observaron tokens recurrentes como:

```text
01 00
3E 00
81 B8 66
```

La interpretación más fuerte es:

```text
0x320..0x3FF = stream compacto de eventos/comandos por nivel
```

La semántica exacta de todos los opcodes quedó abierta.

---

## 7. MZREG2

`MZREG2.LEV` aparece referenciado en tablas/nombres del programa pero el archivo no fue localizado en los datos de juego usados durante la investigación.

Esto apunta a un slot previsto y no distribuido o contenido retirado/inacabado.

No se debe inferir automáticamente qué habría contenido ese nivel.

---

# PARTE II — TRANSICIONES Y TELEPORTS

## 8. TPORTS.TBL

`TPORTS.TBL` mostró estructura interna reconocible.

Se identificaron registros de cuatro bytes con fuerte regularidad y grupos de zonas con etiquetas como:

```text
A B C D
X Y Z
1 2 3 4 5
! * ?
```

Los registros forman relaciones topológicas entre zonas y coordenadas, pero el binding completo:

```text
source MZ → port → destination MZ/X/Y
```

no llegó a cerrarse estáticamente.

---

## 9. Cadena de teleport confirmada

La mecánica general sí pudo reconstruirse:

```text
evento / port
→ selección mediante [0x3C1E]
→ fila de 12 caracteres
→ decodificación de destino
→ dstX
→ dstY
→ dstLevel
→ SetLevel
→ carga del nuevo MZ
```

Se localizó además un `SetLevel` invocable en runtime mediante una llamada far literal, lo que permitió establecer un breakpoint fiable durante las pruebas.

El obstáculo final fue localizar de forma estática el corpus exacto de filas de teleport generado/consultado mediante helpers residentes/overlay.

---

## 10. Resultado del world graph

La arquitectura de transición quedó demostrada.

La enumeración completa de edges directos MZ→MZ quedó **PARTIAL**.

Por tanto, cualquier grafo final debe distinguir:

- nodos confirmados;
- relaciones de zona;
- edges directos realmente demostrados;
- edges todavía inferidos.

---

# PARTE III — ARQUITECTURA DE SAVES

## 11. Dos archivos, dos responsabilidades

La investigación con saves controlados mostró una separación clara.

### GMI

Contiene principalmente estado estructurado del grupo.

Hallazgos:

```text
party base = 0x80
stride = 0x60
4 registros
```

Campos identificados:

```text
status
current_hp
max_hp
name
inventory_count
inventory_raw
```

El parser construido conserva los bytes desconocidos.

### IMG

Contiene un snapshot de estado runtime/mundo.

Mapeo confirmado durante los experimentos:

```text
IMG +0x0A → Y
IMG +0x0C → X
IMG +0x0E → LEVEL
IMG +0x14 → FACING
```

Esto fue demostrado con saves controlados:

- A = estado base;
- B = giro;
- C = movimiento.

Los `.GMI` de esos controles permanecían byte-idénticos mientras `.IMG` reflejaba movimiento/orientación.

---

## 12. CONTROL_A / B / C

Los experimentos controlados fueron decisivos.

### Giro

`CONTROL_A_BASE → CONTROL_B_TURN`

cambios observados en IMG:

```text
0x14   FACING
0x24E  estado irregular
```

### Movimiento

`CONTROL_A_BASE → CONTROL_C_MOVE`

cambios observados:

```text
0x0A   Y
0x14   FACING
0x24E  estado irregular
0x655  estado de mapa/paso aún no cerrado
```

El word en `0x24E` cambia con acciones legítimas y dejó de considerarse un checksum simple.

Interpretación final prudente:

```text
0x24E = RNG/action/runtime state
```

No se demostró que sea el mecanismo de integridad.

---

# PARTE IV — SAVE ARCHAEOLOGY TOOL

## 13. Phase I — core headless

Se construyó una herramienta de parsing/edición segura.

Capacidades:

### GMI

Editable:

```text
name
status
current_hp
max_hp
```

### IMG

Lectura:

```text
level
x
y
facing
```

Se preservan bytes desconocidos.

No-op roundtrip:

```text
GMI: byte-identical
IMG: byte-identical
```

La herramienta usa copy-on-write y evita sobrescribir el original.

---

## 14. Phase II — world editing

Se añadió soporte experimental para edición de:

```text
LEVEL
X
Y
FACING
```

Los tests verificaban que un cambio aislado sólo modificaba los bytes esperados y mantenía intacto el resto del archivo.

Se generó un laboratorio con:

```text
FACING 0 → 1
```

Los tests pasaban.

A nivel binario, el save parecía perfectamente coherente.

A nivel runtime, Uukrul lo rechazó.

---

# PARTE V — EL HALLAZGO DEL ANTI-TAMPER

## 15. Experimento runtime

Dos manipulaciones independientes fueron probadas.

### A — GMI

Cambio:

```text
HP 42 → 40
```

Estado perfectamente válido desde el punto de vista jugable.

Resultado:

```text
save rechazado
```

### B — IMG

Cambio:

```text
FACING 0 → 1
```

También un estado perfectamente válido.

Resultado:

```text
save rechazado
```

El juego mostró un mensaje equivalente a:

> The game saved on this disk is faulty. Use another saved game or another disk.

La frase exacta vista en runtime relacionada con *tampering* no apareció como un literal completo en los binarios investigados; el mensaje almacenado localizado en el EXE fue la variante de "faulty".

Conclusión fuerte:

```text
Uukrul valida algo más que la plausibilidad semántica de los campos editados.
```

Existe una invariante adicional de integridad/consistencia.

---

## 16. Qué NO se demostró

No quedó probado que el mecanismo sea:

- DRM;
- anti-cheat deliberado;
- un checksum convencional;
- una suma/XOR simple;
- o exclusivamente protección contra corrupción.

La terminología recomendable es:

```text
save-integrity / anti-tamper mechanism
```

La interpretación histórica de anti-cheat deliberado es plausible, pero requiere más evidencia sobre intención y algoritmo.

---

# PARTE VI — VALIDACIÓN DEL LOADER

## 17. Ruta de carga localizada

La investigación estática localizó una función de carga y un tramo de validación.

El patrón relevante terminó reducido a:

```text
save loader
→ validation/dispatch
→ handler dinámico
→ retorno en AX
→ CMP AX, 0x0097
→ rama posterior
```

Una pieza especialmente relevante es:

```text
ljmp cs:[0x05DE]
```

El destino de `cs:[0x05DE]` se configura en runtime mediante una cadena de punteros vinculada a memoria baja.

Esto explica buena parte de la dificultad del análisis puramente estático.

---

## 18. Corrección importante: XOR AH,AH

Durante la investigación apareció muchas veces:

```text
30 E4
```

Inicialmente se interpretó erróneamente como una operación XOR entre bytes relacionada con checksum.

La interpretación fue corregida:

```text
XOR AH,AH
```

es decir, limpiar `AH` / zero-extension.

Esta hipótesis de checksum quedó **RETRACTED**.

El caso es un buen ejemplo de por qué las conclusiones intermedias deben conservar su estado epistemológico.

---

## 19. CMP AX,0x0097

El loader contiene una comparación:

```text
CMP AX,0x0097
```

después de la ruta de validación.

Lo que no quedó demostrado de forma definitiva:

- qué representa `AX`;
- qué representa `0x97`;
- qué valor produce exactamente un save bueno;
- qué valor produce el save modificado;
- y cuál es la primera divergencia causal.

Por tanto:

```text
0x97 NO debe llamarse checksum.
```

Es sólo una constante relevante dentro del gate observado.

---

# PARTE VII — INTENTO DE RUNTIME TRACING

## 20. DOSBox-X

Se configuró DOSBox-X con debugger y logging.

El problema práctico fue que el agente no consiguió controlar de forma fiable la ventana interactiva del debugger mediante SendKeys/PowerShell.

Esto llevó a una escalada instrumental.

---

## 21. Instrumentación del EXE

El último agente propuso —y llegó a aplicar— una instrumentación experimental del ejecutable.

Idea:

```text
interceptar justo antes del CMP AX,0x97
→ capturar AX
→ emitir AL/AH por puertos de I/O
→ continuar por el flujo original
```

Se conservaron backups del ejecutable original antes de la modificación.

Se calcularon saltos relativos para que la instrumentación no dependiera del load segment del proceso.

El experimento no llegó a producir una captura final documentada de:

```text
AX_good
AX_corrupted
```

antes de cerrar la investigación.

Por ello esta rama debe tratarse como:

```text
INSTRUMENTATION IMPLEMENTED / FINAL MEASUREMENT PENDING
```

y no como algoritmo de integridad resuelto.

---

# PARTE VIII — ESTADO FINAL DE LA AUTOPSIA

## 22. Confirmado

### Mundo

- `.LEV` de 1024 bytes.
- grid 16×25.
- 2 bytes/celda.
- muro cardinal bit-exact:
  - E `0x01`
  - S `0x04`
  - W `0x10`
  - N `0x40`
- set = bloqueado.
- buffer runtime y coordenadas identificadas.
- eventos en tail separado.

### Saves

- `.GMI` y `.IMG` tienen responsabilidades distintas.
- party records en GMI.
- estado espacial en IMG.
- offsets X/Y/LEVEL/FACING identificados.
- parser seguro y round-trip byte-perfect.
- edición binaria controlada funcional.

### Integridad

- modificaciones mínimas y semánticamente válidas son rechazadas por el juego original.
- existe un mecanismo adicional de validación/consistencia.
- ruta del loader reducida hasta un handler dinámico y un `CMP AX,0x97`.
- `0x24E` no es suficiente para explicar el rechazo.
- las búsquedas de checksum suma/XOR simple no resolvieron el mecanismo.

---

## 23. Abierto

- significado completo de flags especiales de celda.
- bytecode completo de eventos LEV.
- tabla final de transiciones MZ→MZ.
- semántica completa del handler dinámico de validación.
- significado de AX y `0x97`.
- algoritmo exacto de integridad.
- generación de un save modificado aceptado por el ejecutable original.

---

# PARTE IX — CONCLUSIÓN ARQUEOLÓGICA

## 24. Sobre Uukrul

La imagen obtenida es la de un RPG extremadamente compacto pero muy estructurado.

Sus mapas no necesitan grandes dimensiones para producir densidad:

```text
celda
→ geometría
→ orientación
→ feature
→ eventos
→ estado de exploración
```

El juego emplea una representación espacial pequeña y económica, acompañada por sistemas auxiliares que convierten esas pocas celdas en contenido significativo.

La arquitectura de saves también resulta más sofisticada de lo que aparenta:

```text
GMI = party / estado estructurado
IMG = snapshot runtime / mundo
```

y las modificaciones externas no son aceptadas simplemente por mantener valores plausibles.

---

## 25. Sobre el mecanismo de integridad

La conclusión prudente es:

> The Dark Heart of Uukrul contiene un mecanismo real de validación de partidas capaz de detectar modificaciones manuales de campos perfectamente legítimos.

No se ha demostrado todavía si su motivación primaria era:

- corrupción de disco;
- anti-cheat;
- o ambas.

La frase de error y el comportamiento observado hacen especialmente interesante esa cuestión histórica.

---

# PARTE X — LO QUE APRENDIMOS DEL PROCESO

## 26. Investigación con agentes

La autopsia acabó siendo también un experimento de trabajo con agentes.

Se observaron dos clases de tarea muy distintas:

### Implementación acotada

Cuando el objetivo era:

```text
implementa parser
añade tests
preserva bytes
```

los agentes trabajaron de manera eficiente.

### Investigación abierta

Cuando el objetivo era:

```text
averigua qué significa esto
```

aparecieron:

- crecimiento masivo de contexto;
- hipótesis simultáneas;
- necesidad de retractar conclusiones;
- compactaciones repetidas;
- degradación del estado global;
- y finalmente agotamiento del contexto.

---

## 27. Research Loop útil

La práctica más efectiva fue externalizar el estado.

Formato recomendado:

```text
OBJECTIVE

CONFIRMED
STRONG
SPECULATIVE
REJECTED / RETRACTED
BLOCKED

ARTIFACTS

NEXT QUESTION
```

Cada corte debe reducir una incertidumbre concreta.

Cuando el análisis estático deja de producir información, debe cambiarse de método:

```text
static → runtime
analysis → controlled experiment
agent automation → human-in-the-loop
```

---

## 28. Human-in-the-loop

Un aprendizaje especialmente claro:

No merece la pena gastar decenas de minutos intentando automatizar una interacción que el humano puede realizar en cinco segundos.

Para futuras investigaciones:

```text
HUMAN_ACTION_REQUIRED
```

debería ser un estado operativo legítimo.

Ejemplo:

```text
Agent:
"Breakpoint preparado. Metal: pulsa P y confirma."

Human:
"Hecho."

Agent:
"Capturo registros y continúo."
```

---

## 29. División de modelos

La investigación sugiere una separación práctica:

### Modelo fuerte

Útil para:

- reducir incertidumbre;
- comparar hipótesis;
- decidir el siguiente experimento;
- mantener arquitectura conceptual.

### Modelo rápido/local/peleón

Útil para:

- búsquedas;
- scripts;
- diffs;
- xrefs;
- desensamblado;
- tests;
- experimentos concretos.

El recurso caro no tiene por qué ser toda la ejecución: puede ser la **dirección intelectual del siguiente corte**.

---

# PARTE XI — CIERRE

## 30. Estado

```text
ERIOSTHÉ MAP FORMAT        CONFIRMED
CARDINAL WALL TOPOLOGY     CONFIRMED
SAVE SPLIT GMI/IMG         CONFIRMED
SAVE TOOL CORE             COMPLETE
WORLD EDIT BINARY          IMPLEMENTED
WORLD EDIT ORIGINAL GAME   REJECTED BY INTEGRITY
SAVE INTEGRITY EXISTENCE   CONFIRMED
INTEGRITY ALGORITHM        UNRESOLVED
FULL WORLD GRAPH           PARTIAL
```

---

## 31. Futuro corte opcional

Si se decide volver:

**Save Integrity Final Cut**

Único objetivo:

```text
capturar AX_good y AX_corrupted
en el gate CMP AX,0x0097
```

Después:

```text
seguir hacia atrás sólo hasta la primera divergencia demostrable
```

No volver a abrir toda la investigación.

---

## 32. Conclusión final

La autopsia no necesita declararse incompleta por no haber vencido el último gate.

Se respondió con evidencia sólida a varias preguntas más importantes que la broma original del save editor:

- cómo representa Uukrul sus mapas;
- cómo almacena paredes y features;
- cómo conserva mundo y party;
- cómo restaura posición;
- y que el juego posee una invariante de integridad adicional que detecta modificaciones externas.

La última puerta permanece cerrada:

```text
¿qué calcula exactamente el validador antes de CMP AX,0x0097?
```

Y queda perfectamente localizada para una futura excavación.

---

## Fuentes de sesión utilizadas

- `SES2.md` — sesión larga previa: mapas, teleports, save archaeology tool, Phase I/II.
- `laguna_round2_fight.md` — investigación estática del save-integrity gate y dispatch dinámico.
- `Pegado text.txt` — último estado de runtime/instrumentación y parche experimental.

Las conclusiones anteriores corregidas durante las sesiones se han tratado como retractadas y no como hechos finales.
