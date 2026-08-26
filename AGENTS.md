# AGENTS.md — RPG Archaeology

## 1. Project Purpose

RPG Archaeology is a historical and technical research project about RPGs, CRPGs, tabletop role-playing games, authoring tools, engines and related systems.

The project exists to preserve:

- historical evidence;
- technical discoveries;
- design questions;
- implementation patterns;
- failed approaches;
- community solutions;
- lessons that may later inform Lands of Folklore or other projects.

This repository is primarily a **research corpus and MkDocs publication site**.

It is NOT a software product whose architecture should be expanded unnecessarily.

---

## 2. Working Principle

Do not optimize for producing more text.

Optimize for:

1. accuracy;
2. traceability;
3. clarity;
4. preservation of evidence;
5. usefulness as a future reference.

The goal is to create documents that can be revisited months or years later and still make clear:

- what is known;
- what is inferred;
- what remains uncertain;
- why the finding matters.

---

## 3. Evidence Discipline

Never silently convert inference into fact.

Preserve and use evidence labels when appropriate:

- `CONFIRMED`
- `DOCUMENTED FACT`
- `STRONG`
- `STRONG INFERENCE`
- `ARCHAEOLOGICAL READING`
- `OBSERVABLE BEHAVIOR`
- `NOT DEMONSTRATED`
- `OPEN QUESTION`

If a statement cannot be supported by the available sources, either:

- weaken the wording;
- label it as inference;
- or leave it unresolved.

Do not fabricate technical details because they appear plausible.

When source material conflicts, preserve the disagreement instead of silently choosing one version.

---

## 4. Primary vs Secondary Sources

Prefer evidence in roughly this order when available:

1. original manuals and documentation;
2. original source code / reconstructed source / binaries;
3. contemporary interviews and developer statements;
4. contemporary reviews and magazines;
5. community reverse engineering;
6. long-running specialist communities;
7. modern retrospectives;
8. general web summaries.

A lower-level source may still be useful, but its evidentiary weight must be reflected in the wording.

---

## 5. Historical Interpretation

Keep historical fact separate from project interpretation.

Example:

### Confirmed

FRUA provided an event-based authoring system.

### Archaeological Reading

A domain-specific event vocabulary can allow non-programmers to express complex RPG behaviour without exposing runtime implementation.

Do not present the second statement as if SSI explicitly documented that design philosophy unless a source proves it.

---

## 6. Lessons for Lands of Folklore

RPG Archaeology may extract ideas relevant to Lands of Folklore.

These must remain clearly identified as:

- lessons;
- comparisons;
- questions;
- future seeds.

They are NOT automatically LoF architecture decisions.

Never convert an archaeological observation into a LoF requirement or ADR.

Preferred framing:

> Future LoF question

or:

> Lesson potentially relevant to LoF

Avoid:

> LoF must implement...

unless the repository already documents such a decision elsewhere.

---

## 7. Document Language

The site is bilingual.

Spanish is the default language.

File convention:

```text
page.md
page.en.md

Where:

page.md      = Spanish
page.en.md   = English

Both versions must represent the same research document.

Translation must preserve:

section hierarchy;
evidence labels;
technical terminology;
code examples;
diagrams;
source references;
distinctions between fact and interpretation.

Do not independently rewrite one language version into a substantially different article.

When modifying shared factual content, check whether the corresponding translation also requires an update.

8. File Naming

Use simple filesystem-safe names.

Preferred:

snake_case.md
snake_case.en.md

Avoid:

spaces;
accented characters;
long punctuation;
em dashes;
decorative filenames.

Human-readable titles belong in Markdown headings and MkDocs navigation, not filenames.

9. MkDocs

The project currently uses:

MkDocs 1.x
Material for MkDocs
mkdocs-static-i18n

Do NOT migrate to MkDocs 2.x.

Do NOT replace the current theme or i18n solution unless explicitly requested.

Spanish is the default locale.

The i18n structure uses:

docs_structure: suffix

Internal Markdown links should normally target the base .md filename.

Example:

[Blackmoor](blackmoor.md)

Do not manually link to .en.md unless there is a specific reason.

10. Build Validation

After changes that affect:

filenames;
navigation;
links;
MkDocs configuration;
language pairing;
theme configuration;

run:

mkdocs build --strict

A task affecting MkDocs is not complete until the strict build succeeds.

Do not silence warnings using fake files or hacks.

Investigate the cause.

11. Generated Site

Never manually edit:

site/

site/ is generated output.

Source content belongs under:

docs/

Images and presentation assets belong under:

docs/assets/
12. Assets

Current intended structure:

docs/assets/
├── images/
│   ├── common/
│   ├── origins/
│   └── games/
└── stylesheets/

Use reusable common assets only when they genuinely apply globally.

Game-specific material should remain grouped by game.

Example:

docs/assets/images/games/frua/
docs/assets/images/games/bloodwych/
docs/assets/images/games/uukrul/

Avoid putting manually managed assets in site/assets.

13. Visual Changes

RPG Archaeology should remain primarily readable documentation.

When working on presentation:

Prefer:

Material configuration;
small CSS additions;
consistent headers;
readable typography;
evidence callouts;
restrained use of images.

Avoid:

unnecessary JavaScript;
frontend frameworks;
animated backgrounds;
complex custom themes;
dependencies without demonstrated value.

Do not turn MkDocs styling into a separate application.

14. Scope Control

Before making a change, determine whether it belongs to the requested task.

Do not opportunistically:

reorganize unrelated documents;
rewrite historical prose;
refactor the entire navigation;
add plugins;
rename large groups of files;
change evidence labels;
alter source interpretation.

If a useful unrelated improvement is discovered, report it instead.

15. Research Work

When explicitly asked to investigate a subject:

identify the question being answered;
inspect available primary evidence first;
distinguish original evidence from community reconstruction;
compare sources where useful;
record uncertainties;
extract design lessons only after establishing the facts.

Do not begin with a desired conclusion and search only for evidence supporting it.

16. Reverse Engineering / Reconstructed Projects

Repositories such as OpenUA, Dungeon Craft/UAF, community editors or disassemblies are valuable but must be described precisely.

Distinguish:

original implementation

from:

community reconstruction

from:

later reimplementation / evolution

A behaviour found in Dungeon Craft does not automatically prove FRUA implemented it identically.

A behaviour found in OpenUA may provide strong evidence about FRUA, but document the provenance.

17. Existing Research Must Be Preserved

Do not aggressively "clean up" historical documents.

The corpus intentionally preserves:

research trails;
competing hypotheses;
confidence levels;
archaeological reasoning;
technical detail.

Editing for clarity is acceptable when requested.

Removing nuance because a shorter explanation looks cleaner is not.

18. Delivery

At the end of a task, report:

what was inspected;
what was changed;
what was not changed;
validation performed;
unresolved questions;
relevant findings outside scope.

For MkDocs changes include the result of:

mkdocs build --strict

For research tasks distinguish:

CONFIRMED FINDINGS
INFERENCES
OPEN QUESTIONS

when appropriate.

19. Git Policy

Do not create commits unless explicitly instructed.

Leave repository changes in the working tree for review.

Do not push.

Do not rewrite history.

20. Core Rule

RPG Archaeology is not a collection of trivia.

It exists to answer:

What problem were these designers trying to solve?

What solution did they choose?

What constraints shaped that solution?

What failed or frustrated users?

What can we learn from it today?

Preserve those questions throughout the work.