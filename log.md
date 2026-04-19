# Wiki Log

> Chronological record of all wiki actions. Append-only.
> Format: `## [YYYY-MM-DD] action | subject`
> Actions: ingest, update, query, lint, create, archive, delete

## [2026-04-19] create | Wiki initialized
- Domain: IBM iSeries RPG 编程语言
- Structure created with SCHEMA.md, index.md, log.md

## [2026-04-19] ingest | IBM ILE RPG Reference for IBM i 7.5 (sc092508.pdf)
- Source: https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/sc092508.pdf
- Raw: raw/papers/rpg-reference-7.5.txt (2.9MB, 1104 pages)
- Created: concepts/rpg-iv-basics.md, concepts/rpg-iv-bifs.md, concepts/rpg-iv-data-types.md, concepts/rpg-free-format.md, concepts/rpg-subprocedures.md, concepts/rpg-file-processing.md, concepts/rpg-error-handling.md, concepts/ile-concepts.md
- Created: entities/ibm-i-platform.md
- Created: comparisons/whats-new-7-5.md

## [2026-04-19] ingest | IBM ILE RPG Programmer's Guide for IBM i 7.5 (sc092507.pdf)
- Source: https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/sc092507.pdf
- Raw: raw/papers/rpg-programmers-guide-7.5.txt (1.6MB, 562 pages)
- Created: concepts/rpg-binding-strategies.md, concepts/service-programs.md, concepts/rpg-parameter-passing.md, concepts/rpg-java-interop.md, concepts/rpg-debugging.md, concepts/rpg-program-execution.md, concepts/rpg-development-workflow.md

## [2026-04-19] ingest | DDS for Physical and Logical Files (rzakb.pdf)
- Source: https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/rzakbpdf.pdf
- Raw: raw/papers/dds-physical-logical-7.5.txt (278KB, 102 pages, v7.2)
- Created: concepts/dds-physical-files.md, concepts/dds-logical-files.md, concepts/dds-keywords.md

## [2026-04-19] ingest | DDS for Display Files (rzakcpdf.pdf)
- Source: https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/rzakcpdf.pdf
- Raw: raw/papers/dds-display-files.txt (822KB, 288 pages, v7.2)
- Created: concepts/dds-display-files.md, concepts/dds-display-file-keywords.md

## [2026-04-19] ingest | DDS for Printer Files (rzakdpdf.pdf)
- Source: https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/rzakdpdf.pdf
- Raw: raw/papers/dds-printer-files.txt (416KB, 160 pages, v7.2)
- Created: concepts/dds-printer-files.md, concepts/dds-printer-file-keywords.md

## [2026-04-19] update | Wiki expansion: 3 new concept pages from RPG Programmer's Guide
- Created: concepts/rpg-commitment-control.md — Commitment Control (COMMIT/ROLBK/LCKLVL)
- Created: concepts/rpg-xml-json.md — XML/JSON processing (XML-INTO/XML-SAX/DATA-INTO/DATA-GEN)
- Created: concepts/rpg-storage-heap.md — Dynamic storage/heap management (%ALLOC/%REALLOC/linked list)
- Total pages: 26 → 29

## [2026-04-19] ingest | IBM i 7.5 API Overview and Concepts (apipdf.pdf)
- Source: https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/apipdf.pdf
- Raw: raw/papers/apipdf.txt (1.5MB, 476 pages, v7.2)
- Created: concepts/ibm-i-api-concepts.md, concepts/ibm-i-api-reference.md, concepts/ibm-i-user-space.md
