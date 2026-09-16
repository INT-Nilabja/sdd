# Spec: Employee Internal Transfer Request

## Spec ID
transfer-requests

## Status
In Peer Review

## Roles & Assignments
- **Developer:** Subhajit Mukherjee (subhajit.mukherjee@intglobal.com)
- **Gate 1 Reviewer(s):** Shamik Bhattacharya (shamik.bhattacharya@intglobal.com)
- **Gate 2 Reviewer(s):** Subhajit Mukherjee (subhajit.mukherjee@intglobal.com)

## Linked BRD
.ai-context/BRD.md#BRD-001 through .ai-context/BRD.md#BRD-010

## Gate Approvals & History
| Gate | Approver Name | Approver Email/ID | Date/Time | Outcome | Approval Comment / Summary |
|---|---|---|---|---|---|
| Gate 1 (Spec Review) | _pending_ | _pending_ | _pending_ | _pending_ | _Awaiting Gate 1 Spec Peer Review by the assigned reviewer._ |
| Gate 2 (Code Review) | _pending_ | _pending_ | _pending_ | _pending_ | _Not started — requires Gate 1 approval first._ |

## Intent
Enable an employee to initiate, submit, and track an **Internal Transfer Request** from the One-Point Employee Portal, with the portal orchestrating the downstream stakeholder steps (Manager confirmation, HR eligibility validation, Payroll update, IT access update, Facilities relocation) so the employee sees a single, always-current view of overall status and which action is pending with which stakeholder — replacing today's manual, multi-team coordination described in `BRD.md` Business Context.

## Context
- Builds on: `.ai-context/architecture.md` → "Proposed Business Modules — `transfer-requests`" (Modular Monolith module; pending Gate 1 architecture approval alongside this spec).
- Related specs: none yet — this is the first feature spec for this project.
- BRD source: `.ai-context/BRD.md` (BRD-001–BRD-010).

### Assumptions Made to Resolve BRD Open Questions
`BRD.md` raised nine open questions (BRD-OQ-001–BRD-OQ-009) that the source document left undecided. To keep this spec's scope precise and testable, the following **v1 assumptions** are proposed for Gate 1 confirmation — each is a business decision, not a technical one, and should be explicitly accepted, rejected, or amended by the reviewer:

| Ref | Open Question | v1 Assumption Proposed by This Spec |
|---|---|---|
| BRD-OQ-002 | Fixed sequence vs. parallel stakeholder actions? | **Fixed sequence**: Manager → HR → Payroll → IT → Facilities. Each stakeholder's action only becomes actionable after the previous one is approved. |
| BRD-OQ-006 | Are Payroll/IT/Facilities steps always required? | **Always required** in v1 (no conditional skipping based on transfer type). Conditional logic is deferred to a future spec/CR once real rules are known. |
| BRD-OQ-007 | Automated integration vs. manual tracking for Payroll/IT/Facilities? | **Manual tracking only** in v1 — an authorized stakeholder marks their own step Approved/Rejected via the portal; no external Payroll/IT/Facilities system integration exists yet. |
| BRD-OQ-001 | HR eligibility criteria? | Not defined by BRD; modeled as a **binary HR decision** (Approve/Reject) with an optional comment, not a rules engine. |
| BRD-OQ-003 | Can an employee edit/cancel after submission? | Employee **may cancel** a request any time before it reaches a terminal status (Completed/Rejected/Cancelled); **editing is not supported** — cancel and resubmit instead. |
| BRD-OQ-004 | What happens on rejection? | Any stakeholder rejection moves the request to a **terminal `Rejected` status**; no appeal/resubmission path in v1 (employee submits a new request instead). |
| BRD-OQ-005 | Who else can view status? | Only the **requesting employee** and the **stakeholder currently holding the pending action** can view a request's status/detail in v1. |
| BRD-OQ-008 | SLA/escalation for slow stakeholders? | **Out of scope for v1** — no timers, reminders, or escalation. |
| BRD-OQ-009 | Effective-date validation? | Effective date **must be strictly after the submission date** (no same-day/past-dated transfers); no additional minimum notice period enforced in v1. |

## API Contract

### transfer-requests.API01 — POST /api/v1/transfer-requests
Employee submits a new transfer request.

**Request payload:**
```json
{
  "proposedDepartmentId": "string",
  "proposedLocationId": "string",
  "proposedRoleId": "string",
  "effectiveDate": "YYYY-MM-DD",
  "reason": "string | null"
}
```

**Success response (`201`):**
```json
{
  "id": "string",
  "employeeId": "string",
  "proposedDepartmentId": "string",
  "proposedLocationId": "string",
  "proposedRoleId": "string",
  "effectiveDate": "YYYY-MM-DD",
  "reason": "string | null",
  "status": "SUBMITTED",
  "createdAt": "ISO-8601"
}
```

**Exceptions:**
| Code | Condition | Response body |
|---|---|---|
| 400 | Missing required field, or `effectiveDate` is today or in the past | `{ "error": "VALIDATION_ERROR", "fields": { "<field>": "<message>" } }` |
| 401 | No/invalid auth token | `{ "error": "UNAUTHORIZED" }` |

### transfer-requests.API02 — GET /api/v1/transfer-requests/:id
Returns a single transfer request with its full stakeholder-action timeline.

**Success response (`200`):**
```json
{
  "id": "string",
  "employeeId": "string",
  "proposedDepartmentId": "string",
  "proposedLocationId": "string",
  "proposedRoleId": "string",
  "effectiveDate": "YYYY-MM-DD",
  "reason": "string | null",
  "status": "SUBMITTED | IN_PROGRESS | COMPLETED | REJECTED | CANCELLED",
  "stakeholderActions": [
    {
      "id": "string",
      "stakeholderType": "MANAGER | HR | PAYROLL | IT | FACILITIES",
      "status": "NOT_STARTED | PENDING | APPROVED | REJECTED | NOT_REQUIRED",
      "actedBy": "string | null",
      "actedAt": "ISO-8601 | null",
      "comment": "string | null"
    }
  ]
}
```

**Exceptions:**
| Code | Condition | Response body |
|---|---|---|
| 403 | Caller is neither the owning employee nor the stakeholder holding the currently `PENDING` action | `{ "error": "FORBIDDEN" }` |
| 404 | No such transfer request | `{ "error": "NOT_FOUND" }` |

### transfer-requests.API03 — GET /api/v1/transfer-requests
Lists the authenticated employee's own transfer requests (summary view, for status/pending-action visibility).

**Success response (`200`):**
```json
{
  "items": [
    {
      "id": "string",
      "status": "SUBMITTED | IN_PROGRESS | COMPLETED | REJECTED | CANCELLED",
      "effectiveDate": "YYYY-MM-DD",
      "pendingStakeholderType": "MANAGER | HR | PAYROLL | IT | FACILITIES | null"
    }
  ]
}
```

### transfer-requests.API04 — POST /api/v1/transfer-requests/:id/actions/:actionId/decision
The stakeholder currently holding the `PENDING` action approves or rejects it.

**Request payload:**
```json
{
  "decision": "APPROVE | REJECT",
  "comment": "string | null"
}
```

**Success response (`200`):**
```json
{
  "id": "string",
  "status": "IN_PROGRESS | COMPLETED | REJECTED",
  "stakeholderActions": [ "...same shape as API02..." ]
}
```

**Exceptions:**
| Code | Condition | Response body |
|---|---|---|
| 403 | Caller is not authorized for this `stakeholderType`, or the action is not the currently `PENDING` one | `{ "error": "FORBIDDEN" }` |
| 404 | No such transfer request or action | `{ "error": "NOT_FOUND" }` |
| 409 | Action is not in `PENDING` status (already decided) | `{ "error": "INVALID_STATE" }` |

### transfer-requests.API05 — POST /api/v1/transfer-requests/:id/cancel
The owning employee cancels a request that has not yet reached a terminal status.

**Success response (`200`):**
```json
{ "id": "string", "status": "CANCELLED" }
```

**Exceptions:**
| Code | Condition | Response body |
|---|---|---|
| 403 | Caller is not the owning employee | `{ "error": "FORBIDDEN" }` |
| 409 | Request is already `COMPLETED`, `REJECTED`, or `CANCELLED` | `{ "error": "INVALID_STATE" }` |

## Acceptance Criteria

1. transfer-requests.AC1 — Given an authenticated employee on the New Transfer Request page with all required fields (department, location, role, effective date) filled in, when they submit the form, then a new request is created and the employee is navigated to a status view showing status "Submitted".
2. transfer-requests.AC2 — Given an authenticated employee leaves a required field empty or picks an effective date that is today or earlier, when they attempt to submit, then the form displays inline validation errors per field and no request is created.
3. transfer-requests.AC3 — Given an employee has one or more transfer requests, when they open their transfer-request status view, then each request shows its current overall status and, if not yet in a terminal status, which stakeholder the next action is pending with.
4. transfer-requests.AC4 — Given the employee fills in the optional reason field, when they submit, then the reason is stored and shown on the status view; given they leave it blank, when they submit, then the request is created successfully without a reason.
5. transfer-requests.AC5 — Given a valid payload from an authenticated employee, when `POST /api/v1/transfer-requests` is called, then a transfer request is created with status `SUBMITTED`, exactly one `MANAGER` stakeholder action is created with status `PENDING` (all other stakeholder actions `NOT_STARTED`), and the API returns `201` with the created resource.
6. transfer-requests.AC6 — Given an invalid payload (missing required field, or `effectiveDate` not strictly in the future), when `POST /api/v1/transfer-requests` is called, then the API returns `400` with field-level validation errors and no record is created.
7. transfer-requests.AC7 — Given a transfer request owned by the authenticated employee, when `GET /api/v1/transfer-requests/:id` is called, then the response includes the overall status and the full ordered list of stakeholder actions with each one's status.
8. transfer-requests.AC8 — Given a transfer request neither owned by, nor currently pending on, the authenticated caller, when they call `GET /api/v1/transfer-requests/:id`, then the API returns `403`.
9. transfer-requests.AC9 — Given a stakeholder action in status `PENDING` assigned to the authenticated caller's stakeholder role, when they call the decision endpoint with `APPROVE`, then that action's status becomes `APPROVED` and either the next stakeholder action in sequence transitions from `NOT_STARTED` to `PENDING`, or — if it was the last stakeholder action (Facilities) — the overall request status becomes `COMPLETED`.
10. transfer-requests.AC10 — Given a stakeholder action in status `PENDING`, when the assigned stakeholder calls the decision endpoint with `REJECT`, then that action's status becomes `REJECTED`, the overall request status becomes `REJECTED`, and no further stakeholder action transitions to `PENDING`.
11. transfer-requests.AC11 — Given a transfer request in `SUBMITTED` or `IN_PROGRESS` status owned by the authenticated employee, when they call the cancel endpoint, then the request status becomes `CANCELLED` and every stakeholder action still `NOT_STARTED` or `PENDING` becomes `NOT_REQUIRED`; given the request is already `COMPLETED`, `REJECTED`, or `CANCELLED`, when cancel is called, then the API returns `409`.

## Unit Test Cases (spec-derived)

| Test ID | Maps to AC | Scenario | Expected |
|---|---|---|---|
| transfer-requests.UT01 | AC1 | Frontend: submit form with all required fields | Request created, status view shows "Submitted" |
| transfer-requests.UT02 | AC2 | Frontend: submit with missing department | Inline validation error shown, no API call made |
| transfer-requests.UT03 | AC3 | Frontend: status view with a request pending on HR | Shows overall status + "Pending: HR" |
| transfer-requests.UT04 | AC5 | Backend: POST valid payload | 201, status SUBMITTED, MANAGER action PENDING |
| transfer-requests.UT05 | AC6 | Backend: POST with past effective date | 400 VALIDATION_ERROR, no record persisted |
| transfer-requests.UT06 | AC7 | Backend: GET by owning employee | 200, full stakeholder-action timeline returned |
| transfer-requests.UT07 | AC8 | Backend: GET by unrelated employee | 403 FORBIDDEN |
| transfer-requests.UT08 | AC9 | Backend: Manager approves PENDING action | Manager action APPROVED, HR action becomes PENDING |
| transfer-requests.UT09 | AC9 | Backend: Facilities (last step) approves | Facilities action APPROVED, request status COMPLETED |
| transfer-requests.UT10 | AC10 | Backend: HR rejects PENDING action | HR action REJECTED, request status REJECTED, no further PENDING transitions |
| transfer-requests.UT11 | AC11 | Backend: employee cancels IN_PROGRESS request | Request CANCELLED, remaining actions NOT_REQUIRED |
| transfer-requests.UT12 | AC11 | Backend: cancel an already COMPLETED request | 409 INVALID_STATE |

## Explicitly Out of Scope
- Real Payroll, IT, and Facilities system integrations — v1 uses manual stakeholder confirmation only (Assumption: BRD-OQ-007).
- Conditional skipping of a stakeholder step based on transfer type/impact — all four downstream actions always run in v1 (Assumption: BRD-OQ-006).
- SLA timers, reminders, or escalation for slow stakeholder response (Assumption: BRD-OQ-008).
- Employee editing a submitted request — cancel-and-resubmit only (Assumption: BRD-OQ-003).
- Appeal or resubmission workflow after a rejection (Assumption: BRD-OQ-004).
- Visibility of a request to any actor other than the owning employee and the stakeholder currently holding the pending action (Assumption: BRD-OQ-005).
- Determining/maintaining the employee's Manager identity or org-directory data — assumed to already exist in an upstream employee/identity service this feature reads from, not built here.
- Portal-wide authentication/account provisioning itself — covered by existing portal auth (JWT), out of scope for this feature spec.
- HR-defined eligibility rules engine — HR decision is a manual Approve/Reject in v1 (Assumption: BRD-OQ-001).

## Non-Functional Constraints (from constitution.md)
- Test-first (TDD RED → GREEN) is mandatory for every endpoint in this spec (baseline constitution, pending BRD-specific constitution values).
- Authentication via JWT (Access + Refresh tokens) per `.ai-context/architecture.md`; all endpoints above require a valid access token.
- `transfer-requests` module owns its own data exclusively (no direct cross-module table access), per the Modular Monolith architectural constraint.
- No secrets or credentials committed to the repository; environment-specific configuration via `.env` (git-ignored).
