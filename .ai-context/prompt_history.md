# Prompt History

Append-only chronological log of user prompts, change requests, and AI execution turns. Never overwrite — always append below the last entry.

## 2026-09-16 — Project Initialization
- **User:** Invoked `/int-project-setup`.
- **Discovery Gate Q&A:**
  - Project root: existing `sdd/` folder (workspace root itself is not a git repo)
  - Project Name: sdd
  - Project Type: Full Stack
  - Architecture Style: Modular Monolith (Microservice Ready)
  - Frontend: Next.js + Tailwind CSS
  - Backend: Node.js + Express
  - Database/ORM: PostgreSQL + Sequelize
  - Auth: JWT (Access + Refresh tokens)
  - Deployment Target: Unknown / TBD
  - Gate 1 Reviewer(s): Shamik Bhattacharya (shamik.bhattacharya@intglobal.com)
  - Gate 2 Reviewer(s): Subhajit Mukherjee (subhajit.mukherjee@intglobal.com)
  - BRD: To be attached later; not ingested at setup time.
- **AI Action:** Copied INT Control Plane (`.agent/`) verbatim from skill resources; created `AGENTS.md` and `.agents/skills/` at project root; generated `.ai-context/` knowledge base (base context files, 12 templates, 10 mandatory subdirectories with `.gitkeep`); generated `.gitignore` additions; created Full Stack execution layer (`src/frontend`, `src/backend`, `tests/frontend`, `tests/backend`, `docs/`).

## 2026-09-16 — BRD Ingestion (int-brd-ingestion)
- **User:** Provided `docs/BRD.docx` ("SDD Developer Assessment: Employee Internal Transfer Digital Journey") and asked it to be ingested.
- **AI Action:** Extracted text from the `.docx` (binary, not directly readable — unzipped and stripped the Office Open XML via PowerShell regex). Authored `.ai-context/BRD.md` v1 with Objective, Scope, Actors, Functional Requirements BRD-001–BRD-010, explicit "not specified" markers for NFRs/Business Rules/Assumptions/Out of Scope (none supplied by source), Open Questions BRD-OQ-001–BRD-OQ-009, and an appendix capturing the source document's own deliverable/milestone/evaluation framing. Appended the ingestion event to `.ai-context/brd-change-log.md`. Left `.ai-context/constitution.md` unchanged (source document has no Constitution section). Added a proposed `transfer-requests` business module to `.ai-context/architecture.md`, explicitly pending Gate 1 architecture approval — no module folders created yet. Updated `.ai-context/status.md` to reflect ingestion and the proposed next step (author the feature spec via `int-sdd-lifecycle`).

## 2026-09-16 — Spec Authoring: transfer-requests (int-sdd-lifecycle)
- **User:** Asked to proceed with the feature spec.
- **AI Action:** Authored `.ai-context/specs/transfer-requests.spec.md` (Status: `In Peer Review`) from BRD-001–BRD-010: Intent, 5 API contracts (transfer-requests.API01–API05), 11 Acceptance Criteria (transfer-requests.AC1–AC11, both frontend and backend per the Full Stack mandate), 12 spec-derived unit test rows (UT01–UT12), Explicitly Out of Scope, and Non-Functional Constraints from the current baseline constitution. Proposed 9 explicit v1 assumptions to resolve BRD open questions BRD-OQ-001–BRD-OQ-009 (fixed stakeholder sequence, no conditional skipping, manual-only stakeholder tracking, binary HR decision, cancel-not-edit, terminal rejection, restricted visibility, no SLA/escalation, strictly-future effective date), flagged in the spec for Gate 1 reviewer confirmation/challenge. Roles assigned: Developer — Subhajit Mukherjee; Gate 1 Reviewer — Shamik Bhattacharya; Gate 2 Reviewer — Subhajit Mukherjee. Restructured `.ai-context/status.md` to the `int-sdd-lifecycle`-mandated Active Specs / Daily Execution Log format (retaining the existing Setup and Reviewer Roster sections). Per the Gate 1 HALT rule, halted here — no `.plan.md`, `.tasks.md`, `.test_cases.md`, or `src/` code will be created until Gate 1 approval is recorded.
