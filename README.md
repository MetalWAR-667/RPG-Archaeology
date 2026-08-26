# RPG Archaeology


> **RPG Archaeology** — a research project tracking the historical, mechanical, and technical evolution of role-playing games, from wargaming origins to CRPGs and beyond.

This is a documentation site built with [MkDocs](https://www.mkdocs.org/) and [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/). It is automatically published to GitHub Pages via the `gh-pages` branch.

- **Live site:** https://metalwar-667.github.io/RPG-Archaeology/

---

## Project purpose

RPG Archaeology is a historical and technical research project about RPGs, CRPGs, tabletop role-playing games, authoring tools, engines and related systems.

The project exists to preserve:

- historical evidence;
- technical discoveries;
- design questions;
- implementation patterns;
- failed approaches;
- community solutions;
- lessons that may later inform future projects.

This repository is primarily a **research corpus and MkDocs publication site**. It is NOT a software product.

### Working principle

Do not optimize for producing more text. Optimize for:

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

## Evidence discipline

Never silently convert inference into fact. Evidence is labeled using the following levels:

- `CONFIRMED`
- `DOCUMENTED FACT`
- `STRONG`
- `STRONG INFERENCE`
- `ARCHAEOLOGICAL READING`
- `OBSERVABLE BEHAVIOR`
- `NOT DEMONSTRATED`
- `OPEN QUESTION`

When source material conflicts, the disagreement is preserved rather than silently resolving it.

---

## Primary vs secondary sources

Sources are valued in this order when available:

1. original manuals and documentation;
2. original source code / reconstructed source / binaries;
3. contemporary interviews and developer statements;
4. contemporary reviews and magazines;
5. community reverse engineering;
6. long-running specialist communities;
7. modern retrospectives;
8. general web summaries.

A lower-level source may still be useful, but its evidentiary weight is reflected in the wording.

---

## Research scope

The research answers three recurring questions:

1. **What appeared?**
2. **What problem was it trying to solve?**
3. **What influence did it have afterward?**

### Provisional lineage

```text
Chess / Strategy Games
        ↓
Kriegsspiel
        ↓
Free Kriegsspiel
        ↓
Recreational Wargaming
        ↓
Braunstein
        ↓
Blackmoor / Chainmail
        ↓
Dungeons & Dragons
        ↓
Tabletop RPG
        ↓
Computer RPG
        ↓
Dungeon Crawlers
```

The lineage is open and may change as new sources appear.

---

## Site structure

The documentation is bilingual (Spanish by default, English via `mkdocs-static-i18n`).

- `docs/index.md` — main page (Spanish)
- `docs/index.en.md` — main page (English)
- Internal Markdown links target the base `.md` filename; the i18n plugin handles language switching.

### Navigation

| Section | Description |
|---------|-------------|
| **Inicio** | Introduction and philosophy |
| **Orígenes** | Foundational influences: Kriegsspiel, wargaming, Braunstein, Blackmoor, D&D |
| **Autopsias** | In-depth technical retrospectives on specific games (Wizardry, Uukrul, Bloodwych, FRUA) |

---

## Building locally

Ensure you have Python and pip installed, then:

```bash
pip install mkdocs mkdocs-material mkdocs-static-i18n mkdocs-paginate-plugin
mkdocs serve
```

Open `http://localhost:8000` in your browser.

### Strict build validation

After changes affecting filenames, navigation, links, MkDocs configuration, language pairing, or theme configuration, run:

```bash
mkdocs build --strict
```

A MkDocs task is not complete until the strict build succeeds.

---

## Repository layout

```
RPG-Archaeology/
├── docs/
│   ├── index.md
│   ├── index.en.md
│   ├── influences.md
│   ├── twin_cities_school.md
│   ├── blackmoor.md
│   ├── chainmail.md
│   ├── dnd_history.md
│   ├── crpg_origins.md
│   └── games/
│       ├── wizardry_1.md
│       ├── uukrul_archaeological_chronicle.md
│       ├── uukrul_technical_sheet.md
│       ├── bloodwych.md
│       └── frua_dungeon_craft_authoring_archaeology.md
├── docs/assets/
│   ├── images/
│   │   ├── common/
│   │   ├── origins/
│   │   └── games/
│   └── stylesheets/
├── mkdocs.yml
└── README.md
```

---

## Contributing

This is a private research project. Contributions are not currently solicited, but feel free to open an issue with corrections, citations, or newly discovered primary sources.

Please respect the **evidence discipline**: do not present inference as fact.

---

## License

Content is released under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/), unless otherwise noted for third-party primary sources.

Third-party source material (manuals, code, etc.) is preserved at its original license and clearly attributed.

---

## Project status

The investigation remains in its foundational phase. The site structure and scope may evolve as the corpus grows.

[![MkDocs](https://img.shields.io/badge/built%20with-MkDocs-lightgrey?logo=markdownlint)](https://www.mkdocs.org/)
[![GitHub Pages](https://img.shields.io/badge/published%20on-GitHub%20Pages-blue?logo=githubpages)](https://metalwar-667.github.io/RPG-Archaeology/)
