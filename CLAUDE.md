---
title: CLAUDE.md
description: Schema and constraint layer for the IBM i RPG Wiki
type: meta
tags: [meta, schema]
---

# IBM i RPG Wiki — Schema & Constraints

## Three-Layer Architecture

```
┌─────────────────────────────────────────────┐
│  Layer 3: Schema (this file + SKILL.md)     │
│  You and Claude co-maintain this layer.     │
│  Tells Claude the rules of the wiki.        │
├─────────────────────────────────────────────┤
│  Layer 2: Wiki (concepts/*.md, index.md)    │
│  Claude owns this layer.                    │
│  Create, update, cross-reference, maintain. │
├─────────────────────────────────────────────┤
│  Layer 1: Raw Sources (raw/papers/*)        │
│  Immutable. Read-only for Claude.           │
│  Source of truth for all wiki content.      │
└─────────────────────────────────────────────┘
```

## Layer 1 — Raw Sources

- `raw/papers/` — Original IBM documentation (PDFs + extracted text).
- **Immutable.** Claude may read but never modify these files.
- New sources are added by the user only.
- When ingesting a new source, Claude creates wiki pages in Layer 2 and records the action in `index.md`.

## Layer 2 — Wiki

- `concepts/` — All wiki pages: concept pages, entity pages, comparisons.
- `index.md` — Map of Content (MOC), the entry point for navigation.
- `templates/` — Note templates for new pages.

### File Naming

- Lowercase, hyphen-separated: `rpg-iv-basics.md`, `dds-physical-files.md`.
- Opcode pages: `rpg-opcode-<MNEMONIC>.md` (e.g., `rpg-opcode-CHAIN.md`).
- No spaces, no underscores (except in opcode names that require them, e.g., `Z-ADD`).

### Frontmatter

Every wiki page must have YAML frontmatter:

```yaml
---
title: <Human-readable title>
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: concept | entity | comparison | meta
tags: [tag1, tag2, tag3]
sources: [raw/papers/source-file.txt]
---
```

Update `updated` on every meaningful edit. Keep `created` unchanged.

### Wikilinks

- Use `[[page-name]]` syntax (no directory path).
- Every page must have at least 2 outbound wikilinks.
- The index.md (MOC) links to every wiki page with a one-line summary.

### Page Thresholds

| Condition | Action |
|-----------|--------|
| Concept/feature appears in 2+ sources, OR is core content in 1 source | Create new page |
| Source mentions content already covered | Append to existing page |
| Minor detail mentioned only once | Do not create page |
| Page exceeds 200 lines | Split into sub-topics |

## Layer 3 — Schema

- `CLAUDE.md` (this file) — Structural rules, conventions, constraints.
- `SKILL.md` — Development skills and workflows.

## Domain

IBM i (AS/400, iSeries) platform — RPG programming language (RPG ILE, RPG IV, RPG III), DDS, ILE architecture, IBM i APIs, DB2 for i, CL programming.

## Tag Taxonomy

### Language & Technology
- `rpg` — RPG language fundamentals
- `rpg-iv` — RPG IV / ILE features
- `rpg-free` — Free-format RPG
- `built-in-functions` — BIFs
- `data-types` — Data types
- `file-processing` — File handling
- `subprocedures` — Subprocedures and functions
- `error-handling` — Error handling
- `opcode` — Operation codes

### IBM i Platform
- `ibm-i` — IBM i operating system
- `i-series` — iSeries / AS/400 hardware
- `ile` — Integrated Language Environment
- `service-programs` — Service programs
- `dds` — Data Description Specifications

### Meta
- `meta` — Wiki infrastructure pages
- `comparison` — Version/feature comparison pages
- `reference` — Reference/cheatsheet pages

## Update Policy

When new information conflicts with existing content:
1. Prefer the newer source.
2. If genuinely contradictory, present both views with dates and sources.
3. Mark the frontmatter with `contradictions: [page-name]`.

## Workflows

### Ingest (adding a new raw source)
1. Read the raw source file.
2. Identify concepts/entities that meet page thresholds.
3. Create wiki pages using the concept template.
4. Add entries to `index.md`.
5. Do NOT modify the raw source.

### Query (answering a question)
1. Check `index.md` for relevant pages.
2. Read the wiki pages.
3. If insufficient, consult raw sources.
4. Answer from wiki content; cite raw sources as authority.

### Maintain (updating the wiki)
1. When new information arrives, update affected pages.
2. Update `updated` in frontmatter.
3. Ensure wikilinks remain valid.
4. Keep `index.md` summaries current.
