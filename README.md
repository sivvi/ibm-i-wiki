# IBM i RPG Wiki

An [Obsidian](https://obsidian.md)-compatible knowledge base for IBM i RPG programming. Covers RPG IV, ILE, DDS, IBM i APIs, and the IBM i platform.

## Architecture

The wiki uses a three-layer architecture:

```
ibm-i-wiki/
├── CLAUDE.md          ← Layer 3: Schema & constraints
├── SKILL.md           ← Layer 3: Development skills
├── README.md
├── index.md           ← Layer 2: Map of Content (start here)
├── concepts/          ← Layer 2: Wiki pages (LLM-generated)
├── templates/         ← Layer 2: Note templates
└── raw/               ← Layer 1: Immutable raw sources
    └── papers/        ← Original IBM documentation
```

### Layer 1 — Raw Sources (`raw/`)

Immutable original IBM documentation (PDFs and extracted text). These are the source of truth. The LLM reads them but never modifies them.

### Layer 2 — Wiki (`concepts/`, `index.md`, `templates/`)

LLM-generated Markdown files — concept pages, entity pages, comparisons, and reference sheets. The LLM owns this layer: it creates, updates, cross-references, and maintains consistency.

### Layer 3 — Schema (`CLAUDE.md`, `SKILL.md`)

Constraint files that tell the LLM how the wiki is structured, what conventions to follow, and what workflows to use when ingesting sources, answering questions, or maintaining pages.

## Quick Start

1. Open this folder as an [Obsidian](https://obsidian.md) vault.
2. Start at `index.md` — the Map of Content.
3. Use `[[wikilinks]]` to navigate between pages.
4. Graph view shows the full knowledge graph.

## Conventions

- **File names:** lowercase-hyphenated (`rpg-iv-basics.md`)
- **Links:** `[[wikilinks]]` (no directory path)
- **Frontmatter:** YAML with `title`, `created`, `updated`, `type`, `tags`, `sources`
- **Tags:** Flat taxonomy (see CLAUDE.md for full list)

## Contents

| Area | Pages | Description |
|------|-------|-------------|
| RPG IV | 70+ | Opcodes, BIFs, data types, free format, subprocedures |
| DDS | 8 | Physical/logical/display/printer files, keywords |
| IBM i Platform | 4 | Platform overview, APIs, User Space, ILE |
| Comparisons | 1 | Version comparisons (7.5 vs earlier) |

## Sources

All content is derived from IBM i 7.5 official documentation:

- [ILE RPG Reference](https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/sc092508.pdf) (SC09-2509-09)
- [ILE RPG Programmer's Guide](https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/sc092507.pdf) (SC09-2507-12)
- [DDS for Physical and Logical Files](https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/rzakbpdf.pdf)
- [DDS for Display Files](https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/rzakcpdf.pdf)
- [DDS for Printer Files](https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/rzakdpdf.pdf)
- [IBM i API Overview](https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/apipdf.pdf)
