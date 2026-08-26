# **Bloodwych (1989) — RPG Archaeology**

**Design, systems, and tooling archaeology report**

Objective: Preserve a second reusable source of information for future design and architecture decisions in *Lands of Folklore* (LoF), especially in dungeon authoring, party, inventory, combat, magic, systemic interaction, multiplayer, and tooling.

This report does not aim to fully reconstruct the original code or document every aspect of the game. It focuses on verifiable design decisions and structures that may serve as a comparative reference.

## **1. Executive Summary**

*Bloodwych* is particularly interesting for combining, already in 1989:

> * first-person, real-time dungeon exploration;  
> * a party of individualized champions;  
> * simultaneous split-screen cooperative play;  
> * individual inventories and stats;  
> * tactical formation within a cell;  
> * conversation/trade via a compact interface;  
> * layered magic and progression;  
> * monsters physically present in the dungeon;  
> * a mutable world through switches and triggers;  
> * extremely compact spatial representation;  
> * an authoring model that, although not originally designed as a modern editor, was reconstructed by the community.

The main conclusion is that *Bloodwych* does not attempt to simulate a world through continuous physics. Its expressivity comes from:
**few discrete states + consistent rules + shared references + concrete actions on the dungeon.**

This makes it especially useful as a reference for LoF, whose world also relies on grid, discrete states, and separation between editorial structure and runtime execution.

## **2. Primary Sources Consulted**

### **Code / Resourcing**

> * Resourcing repository:  
>   https://github.com/HoraceAndTheSpider/Bloodwych-68k  
> * Reconstructed editor documentation:  
>   docs/map-editor.md  
>   docs/ui-layout.md  
>   docs/dungeon-graphics.md  
>   docs/resource-layouts.md  
> * Reconstruction tools:  
>   tools/map\_editor/model.py  
>   tools/map\_editor/semantics.py  
>   tools/monster\_view.py  
>   tools/champion\_stats\_scroll.py  
>   tools/spellbook.py  
> * Community AMOS editor archived in repo:  
>   \_archive/AMOS code/BloodwychEditor2-7\_025.txt  
>   \_archive/AMOS code/BloodwychEditor2-7\_026.txt

### **Community / Historical Documentation**

> * Ultimate Amiga:  
>   Hacking / data structure guides  
>   Bloodwych editor and modifications  
>   source/disassembly notes  
> * Lemon Amiga / Lemon64:  
>   manuals and reviews  
> * CRPG Addict:  
>   design and gameplay analysis  
> * Amiga Reviews:  
>   contemporary and retrospective reviews

## **3. Confidence Status**

For this report, three levels are used:

> * **CONFIRMED** — backed by resourcing, reconstructed tools, or explicitly interpreted binary data.  
> * **STRONG** — consistent with multiple sources or observed behavior, but not yet traced to the specific routine.  
> * **OPEN / UNKNOWN** — interesting claim not yet closed with sufficient evidence.

# **PART 0 — THE HUMAN FACTOR AND PRODUCTION**

## **3.1. The Authors: "Trazere's Trinity"**

**CONFIRMED**
*Bloodwych* was not the product of a large studio, but the central effort of three individuals operating under strong technical and memory constraints.

> * **Anthony "Tag" Taglione (16-bit Programming and Design):** Technical engine of the game. Wrote the engine in 68000 assembly for Amiga and Atari ST. His original motivation was to bring his tabletop *Dungeons & Dragons* games to computer to play with his friend Pete James. After seeing *Dungeon Master*, they decided the only way to surpass it was to build an engine capable of rendering two instances of the world simultaneously.  
> * **Pete James (Art and Level Design):** University friend of Anthony. Responsible for extreme visual optimization. Designed a tileset modular and lightweight enough for the engine to draw two first-person views without collapsing the RAM of machines at the time.  
> * **Philip Taglione (8-bit Programming):** Anthony's younger brother. Faced with the publisher's demand to launch the game on lesser systems, he rewrote and adapted all logic and architecture to Z80 assembly (ZX Spectrum, Amstrad CPC).

### **Conceptual Lesson**

The architecture of *Bloodwych* (ultra-compressed data, discrete cell types) was not a theoretical whim, but a **direct response to the need to maintain two player states and two simultaneous renders on machines with 512KB of RAM.**

## **3.2. Production Lesson A: Loss of Runtime Control**

**CONFIRMED**
The core team internally developed versions for Amiga, Atari ST, Spectrum, Amstrad, and Commodore 64. However, the publisher outsourced the MS-DOS (PC) port to an external studio called Walking Circles.
The result was a technical disaster: the PC version launched with critical bugs that corrupted saves, broke switches, and made the game literally impossible to complete.

### **Principle Extracted for LoF**

**Runtime is sacred. Core logic cannot be delegated without absolute oversight.**  
If LoF ever requires ports or integrations with external systems (APIs, platforms, consoles), the original team must audit the state logic. Delegating engine responsibility without clear architectural contracts destroys the player experience, regardless of how good the level design is.

## **3.3. Production Lesson B: Flexible but Verified Scope**

**STRONG**
The game cover was commissioned to illustrator Chris Achilleos with full creative freedom. Achilleos delivered a painting featuring a massive crystal demon. The team liked the art so much that, instead of ignoring it, they returned to the code and adapted the final game design to include that exact creature as the final boss: *The Lord of Entropy*.

### **Principle Extracted for LoF**

**Controlled asset permeability.**  
Although the general rule in LoF is *"I don't expand scope without permission,"* the design must be modular enough that, if an exceptional asset emerges (a 3D model, a musical piece, concept art), the Data Model allows integration (creating a new *Monster Record* or *Event Trigger*) without rewriting the codebase. The engine consumes data; if the data is good, the engine must be able to absorb it without friction.

# **PART I — THE DUNGEON AS DATA MODEL**

## **4. Map Resource Per Tower**

**CONFIRMED**
Each *Bloodwych* tower has a fixed resource of:

> * 0x1000 bytes per tower map;  
> * 0x38-byte header;  
> * up to 8 floors;  
> * the remainder (0xFC8) contains cells.

Each location occupies exactly:

2 bytes

The header stores, among other things:

> * 8 widths;  
> * 8 heights;  
> * 8 data offsets;  
> * 8 X alignment offsets;  
> * 8 Y alignment offsets;  
> * special floor information;  
> * upper floor number.

This allows different floors to:

> * have different dimensions;  
> * be offset relative to each other;  
> * relate vertically without needing a continuous 3D representation.

### **Lesson**

A multi-level dungeon does not need to be a complete 3D volume. It can be:

Floor 0 \-> grid 2D + alignment  
Floor 1 \-> grid 2D + alignment  
Floor 2 \-> grid 2D + alignment  
...

and vertical connections are resolved through the relationship between grids.

## **5. Anatomy of a Cell**

**CONFIRMED**
The two bytes of each cell are exposed in the editor as four nibbles:

Byte 0  
  A = bits 7..4  
  B = bits 3..0

Byte 1  
  C = bits 7..4  
  D = bits 3..0

The three lowest bits determine:

map\_type = second\_byte & 0x07

Therefore there are exactly **8 fundamental cell types**.  
The semantics of remaining bits depend on the type. This is equivalent to an extremely compact variant/union.

# **PART II — THE 8 CELL TYPES**

## **6. Type 0 — Space**

**CONFIRMED**
Represents free/transitable space.  
Default semantics:

00 00

Its main function is to establish that a dungeon position can exist without containing any special structure.

### **Conceptual Reading**

EMPTY / WALKABLE SPACE

## **7. Type 1 — Stone Wall + Feature**

**CONFIRMED**
Does not represent only a stone wall.
It can store:

> * side/orientation;  
> * special state;  
> * one of several wall feature types.

Documented families:

SHELF  
SIGN  
SWITCH  
SOCKET

Switches can have:

> * reference;  
> * visual state (LIT / DIM).

Sockets can have:

> * gem family;  
> * state FULL / EMPTY.

Signs can represent:

> * thematic signs;  
> * scrolls/references.

There is also an occlusion/hiding state.

### **Conceptual Reading**

STRUCTURAL MASS  
      +  
WALL-MOUNTED FEATURE

### **Comparison with LoF**

Bloodwych compresses within the cell what LoF tends to separate conceptually as:

StructuralEdge  
+  
Placed / Interactive Feature

## **8. Type 2 — Wooden Structure**

**CONFIRMED**
A single cell can independently represent the four sides:

N  
E  
S  
W

Each side uses two bits and accepts:

NONE  
WALL  
OPEN  
CLOSED

Additionally, a lock state can exist. Conceptual example:

N = WALL  
E = OPEN  
S = NONE  
W = CLOSED

### **Conceptual Reading**

It is an ultra-compressed form of:

Cell  
  Edge N  
  Edge E  
  Edge S  
  Edge W

### **Relevance for LoF**

It's a historical direct reference to the same problem LoF solves via StructuralEdge.  
Bloodwych integrates edges into the cell out of compression necessity. LoF extracts them as explicit editorial entities for clarity and extensibility.

## **9. Type 3 — Misc Solid**

**CONFIRMED**
Groups at least:

BED  
PILLAR

This classification may seem strange from a modern ontology, but likely reflects a functional decision:  
both are special elements occupying the cell and processed similarly by the engine.

### **Archaeological Lesson**

Historical data categories don't always describe "what something is" in the world. Often they describe:
**what engine routine needs to process it.**

## **10. Type 4 — Stairs**

**CONFIRMED**
Encodes:

Elevation: UP / DOWN  
Direction: N / E / S / W

It doesn't need to store a complete destination in the cell itself. Vertical relationships depend on the floor's layout/alignment.

### **Conceptual Reading**

VERTICAL CONNECTION

## **11. Type 5 — Metal Door**

**CONFIRMED**
Metal doors are their own category. Can define:

REGULAR / PORTCULLIS  
NORTH-SOUTH / EAST-WEST  
OPEN / CLOSED  
LOCK TYPE

Documented lock families by reconstructed editor:

UNLOCKED  
MAGE  
BRONZE  
IRON  
SERPENT  
CHAOS  
DRAGON  
MOON  
CHROMATIC  
VOID

### **Design Lesson**

A lock doesn't necessarily represent requires\_key = true. It can be a connection point with other systems: progression, magic, faction, school, object, global state.

### **Comparison with LoF**

Bloodwych: Door = Cell Type  
LoF: Door = StructuralEdge(kind = DOOR)

## **12. Type 6 — Floor / Event Surface**

**CONFIRMED**
Can represent:

FIZZLE  
FLOOR HOLE  
GREEN PAD  
INVISIBLE PAD

Additionally, independently:

CEILING HOLE  
NO CEILING HOLE

Pads can contain a reference to a trigger table. Example:

Cell:  
  invisible pad  
  trigger\_ref = 17  
  ceiling\_hole = true

### **Conceptual Reading**

FLOOR / SURFACE STATE  
      +  
EVENT REFERENCE

### **Relevance for LoF**

Clear example of reactive world without continuous physics. A cell can have discrete states and references to behavior.

## **13. Type 7 — Magic Space**

**CONFIRMED**
Documented states:

SPACE  
FIREPATH  
MINDROCK  
FORMWALL

More:

POWER = 0..63

### **Design Lesson**

Certain magical effects are modeled as **space properties**, not necessarily as separate actors.  
Relevant for future LoF ideas like: burning, flooded, frozen, poisoned, corrupted, anti\_magic, smoke.  
The key point isn't to copy the model, but to remember that: **a cell's state can be an active part of the simulation.**

# **PART III — THE SPATIAL VOCABULARY**

## **14. Abstract Classification**

If we strip the specific game names, the 8 types roughly represent:

0  EMPTY SPACE  
1  STRUCTURAL MASS + FEATURE  
2  PER-SIDE STRUCTURE  
3  CELL OCCUPANT / SOLID FEATURE  
4  VERTICAL CONNECTION  
5  PASSAGE BARRIER  
6  FLOOR / EVENT SURFACE  
7  ENVIRONMENTAL / MAGIC STATE

This abstraction is more important than the specific names. Bloodwych had to answer, under extreme constraint: "What are the fundamental ways a dungeon position can participate in the game?"  
The 3-bit limit forced definition of a very small, very explicit spatial vocabulary.

### **Possible Future Exercise for LoF**

Without imposing an eight type limit: What are the fundamental responsibilities of StructuralCell, StructuralEdge, and PlacedInstance?  
Not to create huge enums, but to detect responsibility mixing.

# **PART IV — SWITCHES**

## **15. Switch Structure**

**CONFIRMED**
A Type 1 cell can contain a switch. The cell doesn't store its behavior directly. It stores a reference to a shared definition.

STONE WALL CELL  
      |  
      +-- SWITCH  
             |  
             +-- reference  
                  |  
                  v  
              SwitchRecord  
              \------------  
              action  
              target X  
              target Y

Each tower has:

16 switch definitions  
4 bytes each

## **16. Switch Actions**

**CONFIRMED**
The reconstructed editor identifies these actions:

0x00  NO EFFECT  
0x02  REMOVE  
0x04  TOGGLE WALL  
0x06  OPEN METAL DOOR  
0x08  ROTATE WALL  
0x0A  TOGGLE PILLAR  
0x0C  PLACE PILLAR  
0x0E  ROTATE WOODEN WALL

There's no general scripting language. There's a reduced vocabulary of **known verbs operating on the world.**

## **17. Conceptual Interaction Model**

ACTIVATOR  
   |  
   v  
REFERENCE  
   |  
   v  
ACTION + TARGET  
   |  
   v  
WORLD STATE TRANSITION

Example:

Switch #3  
  action = OPEN METAL DOOR  
  target = (14,8)

## **18. Dungeon Verbs**

The switch language reduces to: OPEN, REMOVE, TOGGLE, ROTATE, PLACE.  
This simplicity allows building remote doors, secret walls, moving pillars, rotating mechanisms, connectivity changes, spatial puzzles.

### **Main Lesson**

Before introducing general scripting: **check how much dungeon can be built with a small set of Commands on known states.** Especially relevant for LoF, which already uses Commands in its editorial architecture.

## **19. Reference Reuse**

**CONFIRMED**
Multiple cells can share the same SwitchRecord.

Switch A -\  
Switch B -----> SwitchRecord #7  
Switch C -_/

### **Lesson for LoF**

When an Inspector modifies a shared resource: **the UI must communicate that the modification affects more than one instance.** Applicable to materials, interaction profiles, actor definitions, shared behaviours, reusable resources, event definitions.

# **PART V — TRIGGERS**

## **20. Conceptual Difference Between Switch and Trigger**

**CONFIRMED**
Switch:

action  
x  
y

Trigger:

action  
floor  
x  
y

The switch works as a relatively local spatial mechanism. The trigger is a more general event tool and can explicitly affect another floor.

## **21. Known Trigger Actions**

The resourcing documents a much wider vocabulary:

NO EVENT, SPINNER 1, SPINNER 2, OPEN METAL DOOR, VIVIFY, WOOD DOOR TRAP, TRADER DOOR, TOWER ENTRANCE, REMOVE, CLOSE METAL DOOR, TOGGLE PILLAR, CREATE PAD, CREATE WALL... (among others)

### **Conceptual Reading**

Triggers cross the border between MECHANISM and EVENT SYSTEM. They allow modifying geometry, moving/creating elements, teleporting, changing data, entering towers, special conditions, etc.

# **PART VI — PARTY AND CHAMPIONS**

## **22. Party as Operational Container**

**CONFIRMED in general structure.**
Bloodwych doesn't destroy champion individuality. Each champion maintains identity, profession, stats, HP, Vitality, Spell Points, spells, inventory, equipment, position, orientation, states.  
But the player operates them from a common party layer.

PLAYER --> PARTY --> [CHAMPION, CHAMPION, ...]

## **23. Subpositions Within a Cell**

**CONFIRMED**
There are five subpositions: 0 near right, 1 near left, 2 rear left, 3 rear right, 4 centre

### **Lesson**

A grid-based system can have **tactical micro-space** without abandoning the grid.

## **24. Champion Record**

**CONFIRMED in already reconstructed fields**
Clear separation of Level, Attributes, Resource Pools, Knowledge, Equipment, Position. Not everything reduces to "class + level".

# **PART VII — INVENTORY**

## **25. Individual Inventory**

**CONFIRMED**
Each champion maintains its own inventory (12 visible slots).

Party control = shared operational layer  
Inventory = individual ownership

## **26. Derived Stats**

**CONFIRMED**
Final protection combines base value + equipment + states + hand armor + shield.

### **Lesson for LoF**

Prefer, when it makes sense, derived stats from explicit sources over storing the same final result in multiple places.

# **PART VIII — MAGIC AND PROGRESSION**

## **27-30. Magic and Level-up**

**CONFIRMED**
The 32 spells are represented via individual flags. Knowledge is stored per individual. There's also a separate runtime spell practice matrix.  
The level's contribution to casting varies by profession. The same Level is mechanically interpreted differently depending on the character.

# **PART IX — COMMUNICATION AND AGENCY**

## **31. Real-time Communication**

**STRONG**
Bloodwych integrates communication, trade, and interaction within the same game interface (trade, threaten, bribe, praise, etc.). It's compact and compatible with real-time play.

## **32. Companion Agency**

**STRONG**
Social interactions influence companion autonomy. A companion could maintain a small set of tendencies (loyalty, confidence, aggression) as modifiers for micro-decisions.

# **PART X — MULTIPLAYER**

## **33. Multiplayer as Structural Requirement**

**CONFIRMED at design level**
Bloodwych supports two simultaneous players. Each side maintains its own player state and independent UI region.

## **34. What This Does NOT Mean for LoF**

The reasonable policy for LoF is single-player product, but avoid gratuitous assumptions that make multiple players impossible. Networking, if it ever exists, must be considered post-development.

# **PART XI — THE COMMUNITY EDITOR**

## **35. Not Just a Map Editor**

**CONFIRMED**
The community AMOS editor was able to handle references and data for towers, switches, monsters, stats, etc. Classic example of scope creep in tooling.

## **36. Modern Editor Modes**

Different world responsibilities need **different authoring modes**. Not everything should be edited through the same panel or tool.

## **37. Raw View + Semantic View**

An advanced tool can offer semantic authoring + raw diagnostics without forcing the user to constantly work at the binary level.

## **38. Valid Defaults When Changing Type**

An editorial transformation should produce a valid state by construction rather than accumulating invisible semantic residue. Particularly relevant for Inspectors in LoF.

## **39. Original vs Modified**

The modern editor never overwrites the clean resource. Clearly separate: source, working state, generated/modified output.

## **40. Dirty Resources**

The editor tracks dirty towers/resources. Only modified ones are written.

## **41. Editorial Overlays**

Switches and triggers can be displayed as overlays. The idea for LoF is: **multiple lenses over the same editorial document.**

## **42. First-person Preview**

The authoring preview doesn't respect game collision rules, because authoring navigation and runtime navigation are distinct responsibilities.

# **PART XII — PRINCIPLES EXTRACTED FOR LOF**

## **43. Discrete State as Alternative to Physics**

A dungeon can be systemic without being a continuous physics immersive sim. Philosophy for LoF: **Systemic dungeon crawler.**

## **44. Problem, Not Mandatory Solution**

A class or ability shouldn't necessarily be a single key (e.g., LOCKED DOOR can be opened with lockpick, break, spell, key).

## **45. Small Vocabulary Before Scripting**

Evaluate how much content can be expressed through semantic Commands and well-defined states before introducing general scripting.

## **46. Reference vs Instance**

The PLACEMENT -> REFERENCE -> SHARED DEFINITION pattern is very useful, but requires explicit UX when modifying the shared resource.

## **47. Party as Operational Unit Without Erasing Individuals**

Combine group-level control with individual character state.

## **48. Grid + Subpositions**

Architectural option: world movement = grid, local tactical occupancy = sub-grid / slots.

## **49. Authoring Navigation != Runtime Navigation**

A preview must declare what it intends to validate. There's no single correct definition of "preview".

## **50. Editor as Collection of Lenses**

The same document may need different views depending on the author's question (Structure View, Navigation View, Interaction View, etc.).

# **PART XIII — OPEN ITEMS**

## **51. Open Questions**

Combat, AI, Progression, Social. Design return decreased. These remain as OPEN / OPTIONAL FUTURE EXCAVATION.

# **PART XIV — CONCLUSION**

## **52. What Bloodwych Represents Within RPG Archaeology**

Conceptual formula:

SMALL DATA MODEL  
      +  
CLEAR STATE  
      +  
REUSABLE REFERENCES  
      +  
WORLD COMMANDS  
      +  
CONSISTENT RULES  
      =  
SYSTEMIC DUNGEON

# **PART XV — FUTURE USE IN LOF**

## **53. How to Reuse This Report**

Retrieve as comparative context for future features. Recommended prompt:
**"Take a look at the Bloodwych report from RPG Archaeology and refresh context. Compare the documented decisions there with LoF's current cut/feature. Don't propose changes by automatic analogy: identify only useful patterns, risks, alternatives, and structural differences."**

## **54. Methodological Reminder**

The function of RPG Archaeology is not to copy old solutions, but to discover what problems have already been solved, under what constraints, at what cost, and which principles survived.

**STATUS:** Bloodwych excavation — **CLOSED**  
**Possible future revisit:** only for targeted questions with clear value.  
**Next comparative target:** *Forgotten Realms: Unlimited Adventures*.