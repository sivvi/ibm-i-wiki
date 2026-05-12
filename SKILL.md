---
title: SKILL.md
description: Development skills and workflows for the IBM i RPG Wiki
type: meta
tags: [meta, skills]
---

# Development Skills

Skills available when working with this wiki. Invoke with the Skill tool.

## ingest

Process a new raw source and generate wiki pages.

**When to use:** After adding a new file to `raw/papers/`.

**Workflow:**
1. Read the raw source.
2. Identify concepts/entities meeting page thresholds (see CLAUDE.md).
3. Create pages in `concepts/` using the concept template.
4. Add entries to `index.md` under the appropriate section.
5. Update `updated` dates on all modified pages.

## update

Update existing wiki pages with new or corrected information.

**When to use:** When a raw source contains information that revises existing wiki content, or when fixing errors.

**Workflow:**
1. Read the relevant raw source and existing wiki page.
2. Apply updates following the Update Policy in CLAUDE.md.
3. Update `updated` in frontmatter.
4. Verify wikilinks still resolve.

## review

Cross-reference wiki pages for consistency.

**When to use:** After major changes, or when suspicious of stale/contradictory content.

**Workflow:**
1. Check that all pages referenced in `index.md` exist.
2. Verify every page has at least 2 outbound wikilinks.
3. Check for broken wikilinks (target page doesn't exist).
4. Verify frontmatter completeness (title, created, updated, type, tags, sources).
5. Report orphan pages (not linked from any other page).

## query

Answer a question using the wiki and raw sources.

**When to use:** When asked a question about IBM i, RPG, DDS, or related topics.

**Workflow:**
1. Check `index.md` for relevant pages.
2. Read matching wiki pages.
3. If wiki content is insufficient, consult raw sources.
4. Answer from wiki content; cite raw source lines as authority.

## refactor

Restructure wiki pages: split, merge, rename, or reorganize.

**When to use:** When a page exceeds 200 lines, when a concept is better served as multiple pages, or when directory structure needs reorganization.

**Workflow:**
1. Identify the pages to refactor.
2. Plan the new structure.
3. Create/rename/move files.
4. Update all wikilinks and `index.md`.
5. Verify no broken references.
