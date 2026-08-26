# From Tabletop RPG to CRPG — When the referee became software

## 1. Purpose of this document

The complete history of the *Computer Role-Playing Game* is too extensive for the purpose of RPG Archaeology.

There are already works dedicated specifically to reconstructing it with much greater depth.

Our objective here is more limited.

We want to answer a question:

> What happens when the structures of a tabletop RPG have to become software?

The transition is especially interesting because *Dungeons & Dragons* had formalized a good deal of the gaming experience through:

- attributes;
- hit points;
- classes;
- levels;
- experience;
- tables;
- monsters;
- items;
- spells;
- maps;
- combat procedures.

But it still retained a fundamental piece that could not easily be transferred to the computer:

the referee.

The birth of the CRPG can be interpreted, in part, as the attempt to replace the referee's functions with algorithms.

## 2. D&D as a formalizable system

A tabletop RPG contains narrative elements that are difficult to represent through software.

But it also contains highly formal structures.

A *D&D* character could reduce part of its state to numbers:

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

A monster could be described through another collection of values.

A combat could be resolved through tables, dice, and procedures.

A dungeon could be represented through a map.

Therefore, a good deal of the game already had characteristics very close to a computational system:

```text
STATE
  +
RULES
  +
CHANCE
  +
PLAYER INPUT
=
NEW STATE
```

The computer was extraordinarily well suited to execute this part of the RPG.

## 3. The problem of the referee

The human referee did much more than consulting tables.

He could also interpret intentions.

A player could say:

> I try to distract the guard by rolling a coin down the hallway.

Although no ruleset explicitly described that situation, the referee could:

- understand the intention;
- consider the context;
- choose an appropriate rule;
- improvise a resolution;
- modify the world.

The computer could not do that.

It could only recognize actions previously represented by the programmer.

Therefore:

```text
TABLETOP RPG


"What do you want to do?"
        │
        ▼
open possibilities
        │
        ▼
      REFEREE

had to become something like:

CRPG


"What do you want to do?"
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
    ALGORITHM
```

This constitutes one of the fundamental differences between tabletop RPGs and CRPGs.

## 4. From intention to command

The transformation can be expressed another way.

In the tabletop:

```text
INTENT
    ↓
INTERPRETATION
    ↓
RULE / ADJUDICATION
    ↓
RESULT
```

In the computer:

```text
COMMAND
    ↓
PROCEDURE
    ↓
CALCULATION
    ↓
RESULT
```

The computer eliminates much of the ambiguity.

In exchange, it obtains enormous advantages.

It can run rules quickly.

It can maintain thousands of state values.

It can remember exactly what happened.

It can perform calculations without human intervention.

It can hide information from the player.

It can generate chance.

It can graphically represent space.

It can automatically run enemies.

The CRPG would not simply be a limited version of the tabletop RPG.

It would progressively become another medium with its own strengths.

## 5. The computer inherits part of the Dungeon Master's work

We can divide some traditional referee functions:

```text
DUNGEON MASTER
│
├── maintains world state
├── knows the map
├── hides information
├── moves enemies
├── applies rules
├── calculates results
├── generates encounters
├── manages treasures
├── interprets actions
├── interprets language
└── improvises
```

Early computers could absorb quite well:

- ✓ maintain state
- ✓ store maps
- ✓ hide information
- ✓ run enemies
- ✓ apply rules
- ✓ perform calculations
- ✓ generate chance
- ✓ manage treasures

But they had enormous difficulties with:

- ✗ interpret intentions
- ✗ understand open-ended language
- ✗ improvise
- ✗ respond creatively

The CRPG arose around that boundary.

## 6. The dungeon was almost a data structure waiting to happen

Within *D&D*, there was also an environment especially easy to formalize:

the dungeon.

A dungeon was usually composed of discrete elements:

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

This could easily be transformed into data.

For example:

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

Or through other equivalent representations.

The dungeon also provided natural limits.

A wall prevents movement.

A door connects spaces.

A room contains entities.

A corridor leads to another position.

The world could be reduced to a finite structure that the computer could store and consult.

## 7. The dungeon also limited actions

The very nature of the dungeon also reduced the number of interactions needed.

Much of the experience could be represented through:

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

This was extremely important.

The computer didn't need to understand:

any imaginable action.

It only needed to implement a sufficiently interesting set of verbs.

Therefore:

> The dungeon was not only an appropriate setting for early CRPGs.

> It was a space of problems especially compatible with early software limitations.

## 8. The grid as a natural abstraction

The grid used for decades by wargames and tabletop games also found a perfect ally in the computer.

A space could be represented through coordinates:

**X, Y**

or through indices:

**MAP[12][8]**

Each position could contain information.

- WALL
- FLOOR
- DOOR
- MONSTER
- TREASURE
- STAIRS
- PLAYER

Movement could become a mathematical operation.

```text
NORTH → y - 1
SOUTH → y + 1
WEST  → x - 1
EAST  → x + 1
```

Complex real-world geometry could be reduced to discrete topology.

This abstraction would turn out to be extraordinarily fruitful.

Decades later it would continue appearing in different ways in numerous dungeon crawlers.

## 9. The first experiments

During the 1970s, students and programmers with access to university systems began to transfer *D&D* ideas to the computer.

Here appear titles associated with systems like PLATO and other mainframes.

Among the names usually cited at this stage are:

- pedit5;
- dnd;
- *Dungeon*;
- other partially lost or incompletely documented university experiments.

It's not necessary for our purpose to determine here exactly which one deserves the title of:

> "first CRPG in history".

The question of priority depends partly on which RPG features we require and on the irregular survival of those programs.

What is important is the phenomenon.

Shortly after *D&D* appeared, different programmers began to wonder:

> Can we make the computer run this?

## 10. PLATO and university worlds

University systems offered extraordinary capabilities for the time.

Especially important was PLATO, which provided terminals connected to central systems and allowed developing relatively sophisticated interactive software.

Within this environment appeared experiments that incorporated recognizable RPG features:

- character creation;
- statistics;
- experience;
- levels;
- exploration;
- monsters;
- combat;
- treasures;
- visual representation of dungeons.

Some experiments also tested multiplayer capabilities.

The RPG was beginning to separate physically from the table.

## 11. From mainframes to personal computers

The next transformation was technological.

CRPGs gradually stopped depending exclusively on university facilities.

The appearance and expansion of microcomputers allowed taking these experiences into individual homes.

Among the early important titles we find *Beneath Apple Manor*, originally published in 1978.

A little later appeared games that would prove fundamental for the commercial consolidation of the genre.

Among them:

- *Akalabeth*;
- *Ultima*;
- *Wizardry*.

They were not simply digital copies of *D&D*.

Each one began to explore what the computer could do differently.

## 12. The first big fork

Very soon, different ways of representing the RPG began to appear.

We can simplify them into two early trends.

**World represented from above**

```text
   @
  ###
 #...#
 #.M.#
 #...#
  ###
```

The player observes space as a map.

This line will be especially important for games like *Ultima*.

**World represented from the character's eyes**

```text
┌─────────────────┐
│                 │
│     HALLWAY     │
│                 │
└─────────────────┘


PARTY / COMMANDS
```

This line will be especially important for *Wizardry* and later for the first-person dungeon crawler.

Both proceeded from the same problem:

> How do we represent an RPG world on a screen?

But they arrived at different solutions.

## 13. Wizardry and the abstraction of the dungeon

*Wizardry: Proving Grounds of the Mad Overlord*, published in 1981, constitutes one of the great milestones of this genealogy.

The player controlled a party exploring a dungeon represented in first person.

Space continued to be essentially discrete.

```text
TURN LEFT
TURN RIGHT
MOVE FORWARD
```

The screen provided the illusion of being inside the dungeon.

But under that representation lay a much simpler logical structure.

This principle will be fundamental for much of the later dungeon crawler:

> The representation can suggest a continuous space while the simulation remains discretized.

## 14. Ultima and another direction

The *Ultima* series explored a different direction.

Instead of focusing exclusively on the dungeon as a closed space, it progressively developed:

- outdoor worlds;
- cities;
- exploration;
- characters;
- conversation;
- social systems;
- narrative;
- simulation.

The CRPG genealogy began to branch.

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

This division is deliberately simplified.

Both branches will constantly influence each other.

But it is useful for our research.

## 15. From the board to the screen

We can now observe a complete transformation.

The tabletop used:

- PAPER
- PENCIL
- DICE
- MAP
- MINIATURES
- REFEREE

The CRPG began absorbing those functions:

```text
PAPER          → memory
PENCIL          → input
DICE          → RNG
MAP           → data
MINIATURES     → graphic representation
TABLES         → algorithms
REFEREE        → software
```

Not all equivalences are perfect.

But they show the direction of the transformation.

## 16. What the CRPG gained

Computerization provided capabilities that would profoundly modify the genre.

**Automatic calculation**

The player no longer needed to constantly consult tables.

**Exact persistence**

State could be saved and restored.

**Hidden information**

The computer could know information the player didn't.

**Automatic simulation**

Monsters and systems could run without human intervention.

**Visual representation**

The world could be shown directly.

**Speed**

Numerous procedures could be resolved instantly.

**Consistency**

The same rule could execute exactly the same way every time.

## 17. What it lost

The transformation also had a cost.

The tabletop RPG allowed declaring practically any intention the referee was willing to interpret.

The early CRPG could only respond to situations foreseen by its programmers.

We can represent it thus:

```text
TABLETOP RPG


narrative possibilities
        ↓
      enormous
        ↓
     referee

initially became:

COMPUTER RPG


programmed actions
        ↓
   limited set
        ↓
     algorithm
```

The CRPG gained:

- speed;
- calculation;
- automatic persistence;
- representation;
- constant simulation.

But it lost a huge part of interpretive freedom.

## 18. Restriction becomes design

But this limitation also produced a new discipline.

If the computer can only execute what is represented, the designer must decide:

- What matters?
- What states exist?
- What can the player do?
- What constitutes an entity?
- How is space represented?
- How is a door stored?
- What does it mean for a monster to be alive?
- How does it know where it can move?

These are deeply technical questions.

And they constitute precisely the territory that RPG Archaeology intends to study.

## 19. The CRPG as a system

A tabletop RPG could depend on interpretation.

The CRPG needed explicit structures.

For example:

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

The imaginary world had to become a data model.

Then that model had to become image, sound, and interaction.

Here begins our technical archaeology.

## 20. From genre to artifacts

From this point on, RPG Archaeology will stop trying to systematically narrate the entire CRPG history.

Existing bibliography already does that task much better.

Our focus changes.

Each game will be treated as a technical and design artifact.

The question will no longer be mainly:

> Which game appeared after?

But:

> What problem did this game have and how did its developers solve it?

## 21. Dissection methodology

For each artifact, when sufficient evidence exists, we can investigate:

- **World architecture**
  - How is space internally represented?
- **Renderer**
  - How is that representation turned into an image?
- **Movement**
  - Continuous, discrete, grid-based?
- **Navigation**
  - How do player and creatures know where they can move?
- **Interaction**
  - How are doors, switches, objects, and mechanisms represented?
- **Simulation**
  - What changes even if the player doesn't act directly?
- **Combat**
  - How do statistics, space, time, and representation relate?
- **AI**
  - How do enemies perceive, decide, and navigate?
- **Party**
  - How is a group represented within space?
- **Authoring**
  - How were maps, creatures, and content built?
- **Constraints**
  - What did CPU, memory, storage, and platform impose?
- **Problem → Solution**
  - What technical decision allowed overcoming each limitation?

## 22. Archaeological evidence

We won't always have source code.

Therefore, it is useful to distinguish different levels of evidence.

**DOCUMENTED**

Direct evidence exists:

- source code;
- technical documentation;
- manuals;
- tools;
- contemporary statements;
- explicit developer testimonies.

**RECONSTRUCTED**

Behavior can be determined through:

- reverse engineering;
- file formats;
- emulation;
- community tools;
- systematic observation.

**HYPOTHESIS**

A technically plausible explanation exists, but we still lack sufficient evidence.

This distinction will be especially important to avoid turning our own deductions into historical facts.

## 23. Innovate, popularize, and refine

We must also avoid another common problem in video game history.

A game being famous for a characteristic doesn't necessarily mean it invented it.

Therefore we'll try to distinguish:

**INTRODUCED**

> Reasonable evidence exists that it constitutes an original or extremely early innovation.

**POPULARIZED**

> Precedents existed, but the game widely spread the solution.

**REFINED**

> The technique already existed, but it was developed in an especially influential way.

**INHERITED**

> The solution clearly comes from earlier games.

This classification will be provisional and may change as new evidence appears.

## 24. A technical genealogy

This will allow building another type of history.

Instead of:

```text
GAME A
  ↓
GAME B
  ↓
GAME C
  ↓
GAME D
```

we can study:

```text
PROBLEM
   │
   ├── Game A → solution 1
   │
   ├── Game B → solution 2
   │
   └── Game C → solution 3
```

For example:

> How to represent a three-dimensional dungeon?

or:

> How to move a party within a grid?

or:

> How to represent creatures within a discrete space?

or:

> How to simulate doors, projectiles, and objects?

Then games separated by decades can be compared directly.

## 25. The dungeon crawler line

Within this investigation, there will be a branch of special interest:

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
EYE OF THE BEHOLDER    others
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

It should not be interpreted as an exclusive or complete line of influence.

It simply provides us with a route of excavation.

Other titles may enter when they prove relevant to solving specific questions.

## 26. Other paths

It will also be necessary to occasionally abandon that line.

*Ultima Underworld*, for example, raises completely different questions about:

- continuous space;
- geometry;
- physics;
- interaction;
- simulation;
- three-dimensional representation.

Other games may provide relevant solutions even if they don't belong strictly to the traditional dungeon crawler.

The criterion will be:

> Is there something interesting to disassemble here?

Not to complete an encyclopedia.

## 27. Archaeological synthesis

After tracing from *Kriegsspiel* to *D&D*, we can formulate a provisional conclusion.

*Dungeons & Dragons* did not appear as an isolated invention.

It was the result of decades of evolution and the convergence of different communities.

In the Twin Cities, Wesley and other wargamers experimented with individual characters, asymmetric information, and referee adjudication.

Arneson turned part of that tradition into *Blackmoor*, where characters and world gained persistence.

In Lake Geneva, Perren and Gygax worked on formalizing medieval and fantasy combat through *Chainmail*.

Arneson and Gygax connected both trajectories and collaborated to produce *D&D*.

The great historical innovation of the product can be stated as:

> An experience that depended on specific communities and specific referees became a set of documented abstractions that strangers could use to build their own campaigns.

*D&D* did not eliminate the referee.

But it formalized enough of the world surrounding the referee to make the game transmittable.

## 28. Provisional genealogy

Our foundational journey can be traced as:

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
      RPG AS SYSTEM
              │
              ▼
      UNIVERSITIES +
          MAINFRAMES
              │
              ▼
              ?
```

That question mark will be our next layer.

## 29. For continuing the excavation

This document does not pretend to offer an exhaustive history of the CRPG.

Its function within RPG Archaeology is to establish the bridge between the tabletop RPG and the video game and explain some of the fundamental problems that appeared during that transformation.

For a much more extensive history of the genre there are works specifically dedicated to it.

### Matt Barton — *Dungeons and Desktops*

The work of Matt Barton offers an extensive history of computer role-playing games, from their antecedents to much more recent stages.

It is especially useful to study chronologically the evolution of the genre, its developers, companies, and main traditions.

The first edition of *Dungeons and Desktops: The History of Computer Role-Playing Games* appeared in 2008.

A second expanded edition was later published together with Shane Stacks.

### Felipe Pepe — *The CRPG Book*

[*The CRPG Book: A Guide to Computer Role-Playing Games*](https://www.thecrpgbook.com/), edited by Felipe Pepe, adopts a different approach.

It is a collective work that covers the genre mainly through specific games.

Its structure makes it an excellent tool for:

- discovering titles;
- locating influences;
- comparing different traditions;
- following the genre's evolution;
- finding new starting points for specific research.

### Scope of RPG Archaeology

These works can provide a much more complete history of the CRPG than we need to reproduce here.

RPG Archaeology will follow another direction.

We will not attempt to document all RPGs.

We will select certain games as artifacts and disassemble them to understand:

> what problem they had, what constraints they had, what solution they found, and what they left for later games.

Chronology will remain important.

But from this point on it will be mainly the context of the excavation.

## 30. The next stratum

So far we have asked:

> Where does the RPG come from?

Then:

> How did it become a reproducible product?

And finally:

> How did it begin to become software?

From now on the question changes.

We take a game.

We open its map.

We observe its data.

We study its renderer.

We follow the movement of its creatures.

We search for interviews, tools, formats, and code when they exist.

We compare what appears to do with what it really does.

And we ask:

> How on earth did they manage to do this with that machine?

There begins properly RPG Archaeology.
