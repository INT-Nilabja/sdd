# Architecture

## Style
Modular Monolith (Microservice Ready)

Local development runs as a single deployable application. Business modules must have clear boundaries and minimize direct coupling with other modules. Shared/infrastructure functionality is isolated in `shared/` areas. Modules are structured so any one of them can be extracted into an independent service in the future without a rewrite. Microservice deployment complexity is not introduced unless explicitly required by a future spec/ADR.

## Frontend Architecture
- **Framework:** Next.js
- **Styling:** Tailwind CSS
- **Structure:**
  ```
  src/frontend/
  ├── app/
  │   ├── routes/
  │   ├── providers/
  │   └── store/
  ├── modules/
  └── shared/
      ├── components/
      ├── hooks/
      ├── services/
      └── utils/
  ```

## Backend Architecture
- **Framework:** Node.js + Express
- **Structure:**
  ```
  src/backend/
  ├── app/
  │   ├── config/
  │   ├── middleware/
  │   ├── routes/
  │   └── server.js
  ├── modules/
  └── shared/
      ├── database/
      ├── logger/
      ├── errors/
      └── utils/
  ```

## Data Layer
- **Database:** PostgreSQL
- **ORM:** Sequelize
- Migrations and models live under module-specific boundaries; no cross-module direct table access — modules interact through their own service layer.

## Authentication & Security
- **Strategy:** JWT (Access + Refresh tokens)
- Access tokens short-lived; refresh tokens used to mint new access tokens. Full security posture (token storage, rotation, revocation) to be detailed via spec/ADR as auth is implemented.

## Deployment
- **Target:** Unknown / TBD — to be decided via ADR when known.

## Module Extraction Guidance
When a module's spec/plan indicates it is becoming a bottleneck for scaling or team ownership, propose extraction via an ADR in `.ai-context/decisions/`. Do not extract preemptively.

## Proposed Business Modules — Pending Gate 1 Architecture Approval
Derived from `.ai-context/BRD.md` (BRD-001–BRD-010, "Employee Internal Transfer Digital Journey"). **No module folders have been created under `src/frontend/modules/` or `src/backend/modules/` yet** — per `int-brd-ingestion`, business module directories are only generated after this proposal receives Gate 1 (Spec Peer Review / Architecture Review) approval.

### Candidate module: `transfer-requests`
Single bounded module owning the end-to-end Internal Transfer Request journey:
- Request capture: department/business unit, location, role, effective date, reason (BRD-001–BRD-007).
- Status & pending-action visibility for the employee (BRD-008, BRD-009).
- Orchestration of the downstream stakeholder steps — Manager confirmation, HR eligibility validation, org-info update, Payroll update, IT provisioning/deprovisioning, Facilities relocation, employee confirmation (BRD-010).

Stakeholder steps (Manager/HR/Payroll/IT/Facilities) are modeled as **actions/state within `transfer-requests`** for now rather than separate modules or external integrations — the BRD does not describe existing HR/Payroll/IT/Facilities systems or APIs to integrate with (see Open Questions BRD-OQ-006, BRD-OQ-007 in `BRD.md`). This keeps the module boundary aligned with the single BRD-described business capability and avoids inventing integration architecture the BRD doesn't support. If a later spec/ADR confirms real downstream systems of record for Payroll/IT/Facilities, those become candidates for extraction into their own modules or external service adapters, consistent with the Modular Monolith (Microservice Ready) style above.

- **Frontend module:** `src/frontend/modules/transfer-requests/` (request form, status view, pending-actions view).
- **Backend module:** `src/backend/modules/transfer-requests/` (request lifecycle, stakeholder action state, status API).
- **Data ownership:** Transfer request records and their stakeholder-action state; no direct table access from other modules.

This is a proposal only — awaiting Gate 1 approval before folder generation.
