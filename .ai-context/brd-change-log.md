# BRD Change Log

Chronological, append-only log of BRD ingestions and amendments. Each entry records what changed in the requirement baseline and why.

## 2026-09-16 — Project Initialized
- No BRD provided at project setup time.
- Baseline `.ai-context/BRD.md` created empty, pending BRD ingestion via `int-brd-ingestion`.

## 2026-09-16 — BRD v1 Ingested
- **Source:** `docs/BRD.docx` — "SDD Developer Assessment: Employee Internal Transfer Digital Journey".
- **Change type:** Initial ingestion (Added).
- **Added:** BRD-001 through BRD-010 (Employee Internal Transfer request capture, submission, status/pending-action visibility, and portal-side orchestration of Manager/HR/Payroll/IT/Facilities activities).
- **Non-Functional Requirements / Business Rules / Assumptions / Out of Scope:** none supplied by the source document — left explicitly unpopulated in `BRD.md` rather than invented.
- **Open Questions raised:** BRD-OQ-001 through BRD-OQ-009 (eligibility criteria, stakeholder sequencing, edit/cancel, rejection handling, status visibility for non-employee actors, conditional stakeholder steps, integration vs. manual tracking, SLAs, effective-date validation).
- **Constitution impact:** None — source document contains no explicit Constitution/Engineering Constitution section. `.ai-context/constitution.md` (baseline set at project setup) is left unchanged.
- **Architecture impact:** Candidate business module(s) proposed in `.ai-context/architecture.md` pending Gate 1 architecture approval; no module folders created yet.
- **Impact on existing specs:** None — no specs existed prior to this ingestion.
