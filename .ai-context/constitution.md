# Project Constitution

> No BRD has been ingested yet. This file holds placeholder baselines only.
> Once a BRD is provided, run the `int-brd-ingestion` skill — if the BRD contains a Constitution / Engineering Constitution / Technical Constitution section, it becomes the authoritative source and MUST replace the placeholders below without dilution of any measurable constraint.

## Testing Discipline
_Not yet defined by BRD. Default INT expectation: test-first (TDD) development, unit tests derived from spec Acceptance Criteria, no merge without green test suite._

## Security Posture
_Not yet defined by BRD. Baseline: JWT (Access + Refresh tokens) per `.ai-context/project_context.md`; no secrets committed to the repository; `.env*` excluded via `.gitignore`._

## Architectural Constraints
_Not yet defined by BRD. Baseline: Modular Monolith (Microservice Ready) per `.ai-context/architecture.md`; module boundaries must remain independent and extractable._

## Non-Functional Baselines
_Not yet defined by BRD (performance, availability, recovery, compliance targets)._

## Versioning Rules
_Not yet defined by BRD. Baseline: Semantic Versioning (vMAJOR.MINOR.PATCH) tracked via `.ai-context/releases/`._
