# The Dark Heart of Uukrul

## Anatomy of an Impossible City — Archaeological Chronicles of Eriosthe

> **RPG Archaeology — Uukrul Expedition, August 2026**
> A reading of the game, its authors, and what we found when we opened
> the digital corpse.

------------------------------------------------------------------------

## Prologue --- We Entered Looking for a Map

There are excavations that begin with a great question and others that begin because someone looks at a 1024-byte file and thinks: **"what the hell is in here?"**

Ours was the second kind.

*The Dark Heart of Uukrul* didn't come to the dissection table as just any game. It had already appeared strange even from the start of 2026 research: a world seemingly enormous without needing enormous maps, first-person exploration leading to overhead tactical combat, magic divided between reliable spells and capricious prayers, temples that were simultaneously refuges, progression milestones, and part of the fiction, and a design that seemed determined to make the player **learn the world rather than simply traverse it**.

The initial question was much smaller:

> **Can we reconstruct the maps of Eriosthe from the original data?**

We could.

The problem was that, once the first door was opened, behind it was another. And another. And another.

We ended up reconstructing level geometry, chasing transitions and teleporters, dissecting save files, building a small safe editor, discovering that the game rejected perfectly legitimate modifications, following a validation path through the executable and overlays, configuring DOSBox-X as a debugging laboratory, and finally experimentally patching a 1990 executable to try to extract a register value right before a mysterious comparison.

At some point we stopped investigating a dungeon crawler.

**We went inside Eriosthe.**

------------------------------------------------------------------------

# I. Two Students, Too Much Coffee, and No Obligation to Obey D&D

Ian Boswell and Martin Buis studied Computer Science at the University of Auckland in the early 1980s. They had played *Wizardry I* and *II* on Apple II and reached a dangerously appealing conclusion for any young programmer:

**"We could do this ourselves."**

But the major influence wasn't just *Wizardry*. Both enjoyed text adventures and their intellectual component. Boswell would recall decades later that from the start they wanted something with the flavor of a narrative adventure and puzzles, not just another monster-fighting RPG. Neither was especially interested in obeying an inherited ruleset. Buis had played wargames and some D&D; Boswell had barely experienced tabletop RPGs. They decided to follow conventions only when they seemed useful.

That detail explains much of Uukrul.

They didn't design a computer adaptation of a prior system.
**They designed rules for the problem they wanted to address.**

The project started as spare-time work. Boswell ended up specializing in programming and Buis in planning and design, although both describe the important ideas as the result of joint sessions and abundant coffee. They also had to fabricate much of their own tools and libraries: at that time there was no ecosystem of engines, middleware, and utilities we take for granted today.

The result was a game built from constraints.

Not despite them.

------------------------------------------------------------------------

# II. Eriosthe Is Not a Collection of Levels

The manual contains an extraordinarily useful statement of intent for understanding the game:

> Eriosthe was designed to feel like **a vast and real place**.

The authors even warn the player not to interpret stairs as the traditional progression of "level 1, level 2, level 3". Difficulty advances mainly **between Sanctuaries**, not vertically.

Decades later, Boswell explained that the maze was designed precisely this way. The practical authoring unit was the region between two consecutive Sanctuaries. Usually one of the two designers was responsible for a region and then the other played and adjusted it. This helps explain why different parts of Eriosthe have their own identity.

It's a surprisingly modern idea:

```text
Sanctuary
    ↓
region with identity
    ↓
problems / encounters / rules
    ↓
new Sanctuary
```

The Sanctuary is not just a checkpoint.

It is **spatial scoring**.

It divides the adventure into sentences without turning the city into a sequence of artificial floors.

And the binary archaeology ended up showing how economical the physical support of that illusion was.

------------------------------------------------------------------------

# III. 1024 Bytes to Build a World

The studied `MZ*.LEV` files are exactly **1024 bytes** each.

Geometry uses the first `0x320` bytes:

```text
25 rows
× 16 columns
× 2 bytes per cell
= 800 bytes
```

The 224 remaining bytes form a structured block associated with level events/scripting.

The cell is therefore the fundamental unit.

And in its low byte we find the cardinal topology:

```text
0x01 = EAST
0x04 = SOUTH
0x10 = WEST
0x40 = NORTH
```

Bit set means **wall / blocked direction**.

We found no evidence of two independent large edge arrays. Connectivity is encoded per cell. The runtime queries this information for movement and line of sight.

The city that seemed enormous from the outside could be expressed with brutal economy:

```text
cell
→ walls
→ orientation
→ feature
→ flags
→ event
```

That was one of the most interesting findings of the entire excavation.

**Uukrul does not need vast amounts of space to produce density. It needs each position to matter.**

The manual says it from the other end of the system: monsters, objects, and maze elements were designed to function together as **interconnected puzzles**.

Thirty-six years later, the bytes tell exactly the same story.

------------------------------------------------------------------------

# IV. The Player Does Not Walk the Dungeon: They Learn Its Grammar

A secret door in Uukrul doesn't necessarily have one solution.

It can require strength. It can hide a mechanism. It can respond to a word. It can yield to magic, prayers, or objects. Some need to be discovered first. In advanced regions, even conventional searching is insufficient and `TALIS` comes into play.

Teleporters aren't mere elevators. There are named destinations, button devices, links that must first be discovered, and mechanisms whose logic is part of the puzzle.

The automap seems a friendly concession until the game itself starts lying.

There are regions where it doesn't work. Places where space twists. Spinners. Passages that don't obey intuitive representation. In CHAOS, even walls can behave unilaterally and shift in one direction without allowing the reverse.

The system teaches a rule.

Then teaches exceptions.

Finally asks whether the player truly understood the rule or simply got accustomed to its appearance.

Buis explained it in a way that could serve as a design manifesto:

> Much of the joy of games lies in learning their rules and then discovering how to exploit them.

That's why he spoke of puzzles at different scales: a region can be a puzzle, the functioning of the priests can be another, and the full story can be another more.

There's no clear boundary between **mechanic** and **puzzle**.

The system is the puzzle.

------------------------------------------------------------------------

# V. Four Aspirants, Four Ways of Relating to the World

Uukrul's party is fixed around four disciplines:

- Fighter
- Paladin
- Priest
- Magician

Fighter and Paladin occupy familiar roles: physical strength, armor, weapons, and in the Paladin's case, the ability to impose or restore life via *lay on hands*.

But Priest and Magician reveal the game's own character.

The Magician works with five Arcana:

```text
Fire
Frost
Protection
Healing
Knowledge
```

Each has its own ring, progressing from iron to crystal. Spells cost Psychic Points, require the appropriate ring, and if cast correctly, **function**.

The Priest lives in a different epistemological universe.

His four gods are:

```text
Ufthu   — War
Drutho  — Underworld
Golthur — Physical Self
Fshofth — Spiritual Self
```

He also has rings and Virtue Points.

But a prayer is not a command.

It's a request.

The manual insists that gods can ignore the priest and that an overly ambitious aspirant can even be punished. It also does something even more interesting: **the manual deliberately refuses to fully explain what prayers do.** The priest must acquire that knowledge through experience.

The player learns religion exactly as the character:

**by testing.**

------------------------------------------------------------------------

# VI. Divine Favor: When an Interface Can Be a Theology

The prayer system is one of Uukrul's best examples because it combines fiction, uncertainty, and mechanics without needing a "reputation with the gods" screen.

The manual provides names, ritual texts, and warnings.

It doesn't deliver a transparent percentage table.

Lee Beng Hai's guide, written from player experience, does precisely the opposite: converting years of discovery into practical knowledge. `RALKOR` attacks, `DROM` strengthens, `MORPETH` reduces encounters, `TULAR` provides food under specific conditions, `KUURAOTH` grants vision, `LAIRIAN` belongs to the ending.

Between the two documents exists a precious distance:

```text
Manual
→ what the world allows you to know

Player experience
→ what you eventually discover

Code
→ what really happens
```

That's the layer a future Ghidra excavation can try to close.

During this research, secondary material claimed divine favor was completely reversed and reduced to exact formulas. We didn't accept that as evidence: those claims weren't accompanied by a verifiable technical chain equivalent to what we required during the autopsy.

For now, the most interesting archaeological conclusion doesn't need to know each percentage yet:

**Uukrul deliberately transforms a mathematical function into an uncertain relationship with a deity.**

The player doesn't press "Heal III --- 73%".

They utter a prayer and wait for someone to listen.

------------------------------------------------------------------------

# VII. Death Had to Hurt

Boswell was uncomfortable with a convention we still debate today: dying, loading a save, and acting as if nothing happened.

His original idea was much more radical: **save/resume, but not save/restore.**

A character dies, stays dead.

Broderbund rejected the proposal.

And probably correctly.

The solution was better: Sanctuaries.

A dead character retains their soul temporarily through their Soul Amulet. They can be taken to the Mausoleum to attempt resurrection, but the corpse ages and chances diminish. Resurrection costs gold, vitality, and experience. If recovery is no longer possible, the amulet goes to the Guild and another character of the same class can take their place.

Death stops being:

```text
GAME OVER
→ LOAD
```

and becomes:

```text
death
→ loss
→ retreat
→ cost
→ recovery
→ possible replacement
→ continuation of story
```

The manual even explicitly asks the player not to abuse backups because the game is better when death means something.

Buis explained the goal: they wanted this risk to make both combat and exploration emotionally charged and to produce moments where the player would be aggressive and others where they'd act cautiously.

The mechanic isn't about punishing through sadism.

It's about producing **behavior**.

------------------------------------------------------------------------

# VIII. Mara: The Ghost of a Party Who Played Before Us

Before we arrived, another party had already tried to defeat Uukrul.

Mara left her diary.

At first it's the account of an extremely powerful group that believes it has understood the situation. They advance quickly. They defeat enemies. They interpret Uukrul's retreat as fear.

Even when they encounter resistance, they read it through their own superiority.

Then they discover they were being led.

Uukrul wasn't fleeing.

**He was placing them.**

The entry on day eighteen of summer destroys the illusion. The hammer meant to defeat him breaks. The party is trapped on the other side of the Palace. Losses begin. Nalusi disappears. Kiriya falls. Esal dies. Mara finally understands that Uukrul wants to keep her alive as a trophy.

The diary does something magnificent: it teaches the player how **not** to interpret the game.

Mara is powerful and fails because she believes power equals understanding.

The protagonists, in contrast, begin as Aspirants precisely because Uukrul doesn't consider them a threat.

Player progression is not just about getting stronger.

It's about reconstructing the knowledge that destroyed the previous expedition.

Buis and Boswell also needed a way to keep Mara present without her physically accompanying the group. The apparitions solved the problem: she could become guide, clue, and emotional link without breaking the dungeon's structure.

A technical limitation produced a narrative resource.

Again.

------------------------------------------------------------------------

# IX. The Heart That Wasn't in the Body

The entire campaign turns out to be a vast preparation operation.

Uukrul can't be defeated by simply hitting harder.

His heart and soul are protected.

The player must gather stone hearts, obtain the dark heart, recover the obsidian hammer, understand the Palace's mechanisms, arm the Beacon, fix Uukrul to this plane, and only then destroy him.

The guide converts this sequence into instructions.

The game turns it into internal archeology:

```text
rumor
→ inscription
→ object
→ prophecy
→ region
→ mechanism
→ revelation
```

The final confrontation isn't proof that the group has enough hit points.

It's proof that the player **understood Eriosthe**.

That's why the manual says the game isn't about counting monsters or measuring maze size.

Uukrul's true size is in the number of relationships between its systems.

------------------------------------------------------------------------

# X. And Then We Opened the Corpse

Our 2026 expedition started much further down.

We didn't want to solve the puzzles.

We wanted to know how they were built.

The first results were clean. The `.LEV` files yielded. The 16×25 grid appeared. The two bytes per cell appeared. The four cardinal bits ended up consistently identified. The final event block showed structure.

Then we chased the teleporters.

We found tables, relationships, and a runtime chain capable of ending by loading a new level:

```text
event / port
→ selection
→ destination data
→ X / Y / Level
→ SetLevel
→ new MZ
```

We didn't close the entire world graph, but we demonstrated its architecture.

Then we made the world's most natural mistake:

**"Since we know where the world is... what if we touch a save?"**

------------------------------------------------------------------------

# XI. GMI and IMG: Two Halves of a Memory

Controlled experiments with saves revealed an elegant separation.

`.GMI` contains primarily structured party/character state.

We found four character records, with identifiable fields for status, current HP, max HP, name, and inventory, carefully preserving what semantic meaning we still didn't know.

`.IMG`, on the other hand, behaves like a snapshot of the world's runtime state.

The A/B/C tests were especially revealing.

A base save.

The same save after turning.

The same save after moving.

The GMI remained identical while IMG changed.

We ended up locating:

```text
+0x0A → Y
+0x0C → X
+0x0E → LEVEL
+0x14 → FACING
```

With enough confidence to build a small save archaeology tool.

The parser did byte-perfect round-trip.

Unknown bytes remained intact.

We could change HP.

We could change orientation.

The resulting binary was coherent.

And Uukrul said:

**No.**

------------------------------------------------------------------------

# XII. The Door That Shouldn't Exist

We tested a ridiculously innocent modification:

```text
HP 42 → 40
```

Rejected.

We tested another:

```text
FACING 0 → 1
```

Rejected.

We weren't trying to give the Fighter nine million hit points or teleport the party outside the map.

We were writing states the game itself could legally produce.

And yet the loader detected something didn't add up.

That's when the expedition changed.

What had started as map reconstruction became an autopsy of the integrity system.

We located a path:

```text
save loader
→ validation / dispatch
→ dynamic handler
→ AX
→ CMP AX,0x0097
→ decision
```

An indirect jump appeared:

```text
ljmp cs:[0x05DE]
```

The destination was configured at runtime via a pointer chain.

Static dissection started fighting against overlays, segments, dynamic addresses, and the decisions of a DOS executable from over three decades ago.

And then the cat and mouse arrived.

------------------------------------------------------------------------

# XIII. The Day We Almost Made Uukrul Brew PBR

 DOSBox-X could run the game.

We could prepare the debugger.

The agent could launch the process.

What couldn't be done reliably was something technologically revolutionary:
**pressing P when the game expected a human to press P.**

We tried automating window interaction. SendKeys. PowerShell. Focus. Logging. Debug configurations.

Each solution opened another small problem.

The lesson turned out more valuable than the key:

> If a human can resolve in five seconds an interaction that takes an agent twenty minutes to automate, the correct flow is not to keep automating.

From there came a rule for future excavations:

```text
HUMAN_ACTION_REQUIRED
```

should be a legitimate state of the agent.

Not a failure.

A tool.

The agent prepares the breakpoint.

Metal presses P.

The agent continues.

End of drama.

No need to build Skynet just to get through a DOS menu.

------------------------------------------------------------------------

# XIV. Laguna vs. the Corpse

The last stretch of the session was almost comical.

Unable to comfortably obtain the `AX` value from the debugger, the research escalated towards executable instrumentation.

The idea was to intercept execution just before:

```text
CMP AX,0x0097
```

capture the register, emit its bytes via I/O, and return control to the original program.

Backups were preserved.

Previous interpretations about the load segment were corrected.

Relative jumps independent of `load_seg` were calculated.

And the patch was applied.

At that point we decided to halt the excavation before the agent ended up adding physically-based lighting to Adron's Palace.

The final result was perfectly delimited:

```text
INSTRUMENTATION IMPLEMENTED
FINAL MEASUREMENT PENDING
```

We don't yet know exactly what `AX` means.

We don't know what `0x97` represents.

We don't know the final integrity algorithm.

And that's okay.

Because an autopsy should also be able to say:

**UNKNOWN.**

------------------------------------------------------------------------

# XV. What We Do Know About the Corpse

The expedition left considerable solid ground.

## World

```text
LEV size                 1024 bytes
Grid                     16 × 25
Cell                     2 bytes
East wall                0x01
South wall               0x04
West wall                0x10
North wall               0x40
Event/script tail        0x320..0x3FF (strong structure)
```

## Saves

```text
GMI
→ party / character state

IMG
→ runtime / spatial world state
```

With position, orientation, and level experimentally identified.

## Integrity

We know that:

- minimal changes that are valid are rejected;
- there's an additional invariant;
- it's not enough to keep semantically plausible fields;
- `0x24E` doesn't explain validation alone;
- initial sum/XOR simple checksum hypotheses didn't solve it;
- the loader path reaches up to a dynamic handler and a comparison with `0x97`.

That's not "not finding the checksum."

It's demonstrating that the question was more complicated than **"where is the checksum?"**

------------------------------------------------------------------------

# XVI. Hypotheses Also Die

One of the best small stories of the investigation was `30 E4`.

It appeared repeatedly in the overlay.

For a moment it seemed like a clue to XOR operations related to the supposed checksum.

Then we looked at it properly:

```asm
XOR AH, AH
```

It was simply clearing `AH`.

Hypothesis dead.

Marked as **RETRACTED**.

And we continued.

This detail summarizes the philosophy that ended up imposing itself during the excavation:

```text
CONFIRMED
STRONG
SPECULATIVE
UNKNOWN
RETRACTED
```

It's not enough to save discoveries.

You must save **how confident we are in them**.

Digital archaeology becomes dangerous when a provisional interpretation is copied three times, ends up in a summary, and reappears an hour later disguised as fact.

------------------------------------------------------------------------

# XVII. Uukrul's Real Puzzle

After opening maps, saves, and code, it's tempting to think the game's great secret is a specific technique.

It isn't.

Its greatest achievement seems to be how many decisions point in the same direction.

The authors wanted the player to learn rules.

That's why:

- the city is divided into regions with personality;
- the automap is helpful until it stops being reliable;
- doors accept different interaction languages;
- objects can have non-obvious functions;
- priests must discover what their prayers mean;
- death produces consequences rather than a simple failure screen;
- Mara teaches through the failure of a previous expedition;
- Uukrul can't be defeated merely through statistics;
- the ending demands gathering knowledge scattered throughout the campaign.

Even the technology reflects this philosophy.

A tiny map in bytes produces an enormous space of decisions.

**Density isn't in geometry. It's in relationships.**

------------------------------------------------------------------------

# XVIII. Why It's Still Worth Excavating

The visible game is extraordinarily well-documented. The manual is generous. Complete walkthroughs exist. The main puzzles have been solved for decades. The 2012 retrospective interview also allows hearing Boswell and Buis directly explain many of their intentions.

But implementation archaeology still has interesting areas.

Our own autopsy left open:

- full semantics of cell special flags;
- final LEV event bytecode;
- complete world transition graph;
- semantics of the dynamic validation handler;
- exact meaning of `AX` and `0x97`;
- integrity algorithm;
- systematic Apple II ↔ DOS comparison.

The next working session will be different.

Instead of trying to turn DOSBox-X into a robotic pianist, the next excavation can combine **Ghidra + MCP + Codex**, using the decompiler as a navigable map of functions, cross-references, and structures.

The goal won't be "reopen Uukrul entirely."

It will be to choose a question and drill.

As any proper excavation should.

------------------------------------------------------------------------

# XIX. What Uukrul Taught Us About Working with Agents

The corpse also ended up dissecting our own workflow.

When the task was bounded:

```text
build parser
preserve unknown bytes
add tests
do round-trip
```

the agent was extraordinarily effective.

When the command became:

```text
figure out what the hell this does
```

context started growing.

Parallel hypotheses appeared.

Some were proven.

Others died.

Summaries needed compressing.

The agent could lose the hierarchy between a three-hour conjecture and a newly confirmed experiment.

The solution isn't just "a bigger model."

It's a method.

```text
OBJECTIVE

CONFIRMED
STRONG
SPECULATIVE
RETRACTED
BLOCKED

ARTIFACTS

NEXT_QUESTION
```

And a second rule:

> **Each cut must reduce one concrete uncertainty.**

When static analysis stops advancing:

```text
static → runtime
```

When automating interaction is absurd:

```text
agent → human-in-the-loop
```

When it takes hours of stone-breaking:

```text
strong model → directs
fast/local model → executes
```

Uukrul ended up being a concept test for how we want to do AI-assisted archaeology.

Not bad for a 1989 corpse.

------------------------------------------------------------------------

# XX. The Game That Arrived Late

There's certain irony in *The Dark Heart of Uukrul*'s story.

Boswell and Buis worked on it for years as a spare-time project. When it appeared, visual technology was advancing rapidly. Boswell himself would recognize that, finished a year earlier, its appearance would have been far more competitive.

It wasn't a big commercial success.

Boswell estimated retroactively that the time invested might equate to about **two dollars per hour**.

There was no sequel.

Both continued their technological careers outside the video game industry.

And yet, here we are.

Decades later, opening its files.

Reading its manual.

Following Mara's traces.

Wondering why a byte of orientation makes a DOS executable angry.

Reconstructing a map that two Auckland students designed between classes, work, and coffee.

The market decided one thing.

Time decided another.

------------------------------------------------------------------------

# XXI. Epitaph for Eriosthe

The authors' letter ends wishing the player enjoys exploring a new universe.

Thirty-six years later, that remains a remarkably precise description of what happens when you open Uukrul.

Only now exploration can continue beneath the floor.

First we walked through Eriosthe.

Then we looked at the automap.

Then we opened the `.LEV` files.

Then the saves.

Then the executable.

And in the end we discovered the city still had doors.

One of them still says:

```text
CMP AX, 0x0097
```

We don't know what's behind it.

Still.

And perhaps that's the correct ending for this expedition.

Because *The Dark Heart of Uukrul* was designed around a very concrete idea: pleasure isn't in receiving all the answers.

It's in seeing the pieces.

Testing.

Failing.

Trying again.

And suddenly understanding the drawing.

------------------------------------------------------------------------

## Expedition Status

```text
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

# Working Sources and Materials

## Primary Sources

**Boswell, Ian; Buis, Martin. *The Dark Heart of Uukrul — Player's Guide*. Broderbund / Digital Studios Ltd., 1989.**
Original manual. Used for rules, class structure, magic, prayers, exploration, combat, death, Sanctuaries, Mara's diary, and the authors' letter.

**Boswell, Ian; Buis, Martin. Retrospective interview by RPG Codex, July 26, 2012.**
Used for development history, influences, design process, dungeon regional structure, puzzle philosophy, death, Sanctuaries, copy protection, and personal retrospectives of the authors.

## Community Source

**Lee Beng Hai (Volume). *The Dark Heart of Uukrul FAQ/Walkthrough*, v1.1, May 24, 2002.**
Used as a record of empirical player knowledge: practical effects of prayers, progression, puzzles, Sanctuaries, hearts, Palace of Adron, CHAOS, and final sequence.

## RPG Archaeology Excavation — August 2026

***The Dark Heart of Uukrul — Technical Autopsy Report.***
Results of analysis of `MZ*.LEV`, `UUKRUL.EXE`, `UUKRUL.OVR`, `UUKRUL.LIB`, auxiliary tables, controlled saves, save archaeology tool, and runtime experiments with DOSBox-X.

### Editorial Principle

Technical claims in this chronicle respect the autopsy taxonomy:

- **CONFIRMED** — demonstrated through data, code, or experiment.
- **STRONG** — well-supported inference.
- **SPECULATIVE** — pending hypothesis.
- **UNKNOWN** — unresolved.
- **RETRACTED** — discarded interpretation.

External claims not accompanied by verifiable evidence have not become technical facts merely by appearing in a search or secondary summary.

------------------------------------------------------------------------

> **RPG Archaeology**
> *We don't just play old worlds. Sometimes we lift the tables off the floor to see what was holding them up.*