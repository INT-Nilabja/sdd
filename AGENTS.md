# AGENTS.md — INT AI-First Engineering Policy

This repository follows the INT AI-First Spec-Driven Development (SDD) methodology. This file is vendor-agnostic and must remain readable and actionable by any AI coding assistant (Claude, Gemini, Cursor, Windsurf, Copilot, etc.), not just Claude Code.

## Authority Hierarchy

1. **`.agent/`** — INT Control Plane. Organization-wide standards, rules, and workflows. Authoritative and must not be altered by individual projects.
2. **`.ai-context/constitution.md`** — Project-specific constitution (testing discipline, security posture, architectural constraints, non-functional baselines, versioning rules). Derived from the project BRD; project-specific constraints take precedence over generic guidance when the two are in tension, and any conflict is flagged for human review rather than silently resolved.
3. **`.ai-context/architecture.md`** — Approved architecture style and technology stack for this project.
4. **`.ai-context/specs/*.spec.md`** — Feature-level source of truth once a spec reaches `Approved` status. Implementation must trace back to spec Acceptance Criteria.
5. **`.ai-context/plans/*.plan.md`** and **`.ai-context/tasks/*.tasks.md`** — Derived execution detail, subordinate to their source spec.

## Lifecycle Overview

```
BRD Ingestion → Spec (Draft → Gate 1 Peer Review → Approved)
             → Plan → Tasks → Test-First Implementation
             → Gate 2 Code Review → Released (vX.Y.Z)
```

Production issues re-enter the lifecycle through **Incident Management** (classified as Spec Gap / Implementation Defect / New Requirement) or **Hotfix Management** for urgent, test-first emergency fixes with mandatory post-hoc Gate 2 review.

## Core Governance Rules

- No feature code is written before an **Approved** spec exists (except emergency hotfixes, which get a compressed spec and mandatory post-hoc review).
- Every spec's Acceptance Criteria must map to unit/integration tests before implementation is considered complete (test-first).
- Gate 1 (Spec Peer Review) and Gate 2 (Code Review) approvals must be recorded in the relevant artifact's Gate Approvals & History table, including reviewer identity, timestamp, outcome, and comment.
- The INT Control Plane (`.agent/`) is never modified by project work — it is a synced copy of the organizational standard.
- All artifact cross-references use repository-relative paths (never local absolute OS paths).
- `.ai-context/prompt_history.md` and `.ai-context/brd-change-log.md` are append-only — never overwritten.

## Project Skills (`.agents/skills/`)

This repository carries its own copies of the INT SDD lifecycle skills so any AI assistant can operate without external configuration:

- `.agents/skills/int-project-setup/SKILL.md` — project initialization (this setup)
- `.agents/skills/int-sdd-lifecycle/SKILL.md` — feature spec → plan → tasks → TDD → Gate 2 → release lifecycle
- `.agents/skills/int-brd-ingestion/SKILL.md` — BRD ingestion and module structure generation
- `.agents/skills/int-incident-management/SKILL.md` — production incident triage and classification
- `.agents/skills/int-hotfix-management/SKILL.md` — emergency hotfix workflow
- `.agents/skills/int-release-management/SKILL.md` — release readiness and release notes
- `.agents/skills/int-session-continuation/SKILL.md` — resuming context across session restarts

## Project Baseline

See `.ai-context/project_context.md` and `.ai-context/architecture.md` for this project's confirmed technology stack, architecture style, and reviewer assignments.
