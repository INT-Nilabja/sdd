# Business Requirements Document (BRD)

## Status
Ingested — v1 (2026-09-16)

## Source Document
`docs/BRD.docx` — "SDD Developer Assessment: Employee Internal Transfer Digital Journey"

## Objective
Provide employees a single digital journey, through the existing **One-Point Employee Portal**, to request and track an **internal transfer** — replacing today's manual, multi-team coordination (Manager, HR, Payroll, IT, Facilities) with one portal-orchestrated flow that gives the employee a single view of progress.

## Scope
- Employee-initiated **Internal Transfer Request** capture (new department/business unit, location, role/job position, effective date, optional reason) within the One-Point Employee Portal.
- Portal-side **orchestration** of the downstream stakeholder activities triggered by a transfer request:
  - Manager confirmation of the transfer.
  - HR eligibility validation.
  - Update of the employee's organisational information.
  - Payroll update (where applicable).
  - IT access provisioning/deprovisioning (where applicable).
  - Facilities arrangement for the employee's new location (where applicable).
  - Confirmation back to the employee.
- Employee-facing **status visibility**: current overall request status, and which actions are currently pending with which stakeholders.

## Actors
| Actor | Role in Journey |
|---|---|
| Employee | Initiates the transfer request; supplies request details; views status and pending actions. |
| Manager | Confirms the transfer. |
| HR | Validates transfer eligibility; updates the employee's organisational information. |
| Payroll | Updates payroll records, where the transfer requires it. |
| IT | Provisions/removes system access, where the transfer requires it. |
| Facilities | Arranges the employee's new location, where the transfer requires it. |
| One-Point Employee Portal | Orchestrates the end-to-end journey and is the single system of engagement for the employee. |

## Functional Requirements
| ID | Requirement |
|---|---|
| BRD-001 | An employee shall be able to initiate an Internal Transfer Request from the One-Point Employee Portal. |
| BRD-002 | The employee shall be able to select the proposed new department/business unit for the transfer. |
| BRD-003 | The employee shall be able to select the proposed new location for the transfer. |
| BRD-004 | The employee shall be able to select the proposed new role/job position for the transfer. |
| BRD-005 | The employee shall be able to provide an effective date for the transfer. |
| BRD-006 | The employee shall be able to provide an optional reason for the transfer. |
| BRD-007 | The employee shall be able to submit the completed transfer request. |
| BRD-008 | The employee shall be able to view the current status of a submitted transfer request. |
| BRD-009 | The employee shall be able to view which actions are pending, and with which stakeholder(s), for a submitted transfer request. |
| BRD-010 | The portal shall orchestrate the downstream stakeholder activities triggered by a submitted transfer request (Manager confirmation, HR eligibility validation, org-info update, Payroll update, IT provisioning/deprovisioning, Facilities relocation, employee confirmation) rather than requiring the employee to coordinate them manually. |

## Non-Functional Requirements
_Not specified in the source document._ No performance, availability, security, or compliance targets were supplied for this journey. Any such targets used at spec/plan stage must be flagged as `[Assumption]` or raised as an Open Question rather than treated as BRD-sourced.

## Business Rules
_No explicit business rules (e.g. eligibility conditions, approval-sequencing rules, rejection handling) were stated in the source document._ The stakeholder sequence described in Business Context (Section 2 of the source) — Manager → HR → org-info update → Payroll → IT → Facilities → employee confirmation — describes the *current manual process*, not a confirmed required rule for the digital journey's own workflow/sequencing. Whether the digital journey must preserve this exact ordering, allow parallel stakeholder actions, or something else, is an open question (see below).

## Assumptions
_None stated in the source document._ Assumptions made during specification must be recorded explicitly in the relevant `.ai-context/specs/*.spec.md` (Context / Intent sections) and labelled as assumptions, not folded silently into requirements.

## Out of Scope
_Not explicitly stated in the source document._ Nothing in the source describes what is excluded (e.g. transfer types other than internal — promotions, cross-entity transfers; bulk/mass transfers; manager- or HR-initiated transfers). Scope boundaries beyond what is listed under Scope above must be confirmed before being assumed excluded.

## Open Questions
These are ambiguities/missing decisions identified directly from gaps in the source document (per its own Deliverable 1 ask to "identify ambiguity and missing decisions"), not requirements:
- BRD-OQ-001: What are HR's eligibility criteria for approving/rejecting a transfer?
- BRD-OQ-002: Must stakeholder actions (Manager, HR, Payroll, IT, Facilities) occur in a fixed sequence, or can any occur in parallel?
- BRD-OQ-003: Can an employee edit or cancel a transfer request after submission, and up to what point?
- BRD-OQ-004: What happens if the Manager or HR rejects the request — does the journey end, or is there a resubmission/appeal path?
- BRD-OQ-005: Who besides the employee can view a request's status (e.g. the employee's manager or HR)?
- BRD-OQ-006: Are Payroll, IT, and Facilities actions always required, or only conditionally (e.g. IT only if systems access changes, Facilities only if the location changes)?
- BRD-OQ-007: Are Payroll/IT/Facilities systems integrated automatically, or do those stakeholders act manually inside their own systems while the portal only tracks status?
- BRD-OQ-008: Is there an SLA/expected turnaround time for each stakeholder's action, and what happens if a stakeholder does not act?
- BRD-OQ-009: Does the effective date have any validation constraints (e.g. minimum notice period)?

## Acceptance Criteria
_Not defined at BRD level._ Per the source document's own deliverable breakdown, individually identifiable, testable Acceptance Criteria are produced at the **spec** stage (Deliverable 2), not the BRD stage. See `.ai-context/specs/` once the feature spec for this journey is authored via the `int-sdd-lifecycle` skill, and trace each AC back to a `BRD-NNN` entry above.

---

## Appendix — Source Document Framing (Non-Requirement Context)
The source document (`docs/BRD.docx`) is itself an **SDD methodology developer assessment brief**, not a conventional client BRD. Beyond the business requirement captured above, it also specifies the deliverables, evaluation weighting, and timeline the resulting SDD artifact chain is expected to satisfy. This context does not add or remove requirements, but should inform how downstream specs/plans/tasks are produced:

- **Required deliverable chain:** Discovery Analysis → Feature Spec (`.spec.md`) + Acceptance Criteria → Spec-Derived Test Cases → Technical Plan → Task Decomposition → AI Prompts → Security Assessment → Gate 1 Review → Gate 2 Evidence.
- **Milestones:** M1 Discovery & Specification, M2 Gate 1, M3 Plan → Tasks, M4 Implementation & Gate 2.
- **Evaluation emphasis (highest-weighted first):** SDD traceability (20%), Specification quality (20%), Ambiguity & discovery (15%), Acceptance criteria & testability (15%), Business journey understanding (10%), Task decomposition (10%), Test-first approach (5%), Security & failure handling (5%).

## Requirement Baseline
BRD-001 through BRD-010 above constitute the current authoritative requirement baseline for this project. Any revision must go through the BRD Change Management process (see `.ai-context/brd-change-log.md`) — existing IDs are never silently renumbered.
