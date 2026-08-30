# ADR-0008: Use GitHub Actions for hosted CI orchestration only

Status: **Accepted**  
Date: 2026-08-30  
Supersedes: —  
Superseded by: —

## Context

CI must handle hosted-only concerns such as events, runners, permissions, secrets, matrices, concurrency, artifacts, and release/deployment gates. But workflow YAML can also reimplement repository task dependencies, affected calculations, and validation commands, creating divergence from local execution.

## Decision

Use GitHub Actions as the hosted CI orchestration authority. It owns platform event/runners/security/job concerns, while equivalent repository build/test/lint/typecheck/affected work delegates to the repository command interface and Moon task definitions.

CI job dependencies may model hosted security/delivery boundaries but must not independently become the canonical repository project/task dependency graph.

## Rationale

This keeps local and CI engineering semantics aligned while allowing the hosted platform to own concerns only it can express safely. The architectural boundary remains portable even if the CI provider later changes.

## Consequences

### Positive

- CI reuses repository policy instead of duplicating it;
- local reproduction remains possible for equivalent work;
- privileged hosted concerns stay explicit;
- provider replacement can preserve the same boundary.

### Negative / constraints accepted

- workflow authors must resist convenient duplicated task/affected logic;
- some hosted-only behavior will necessarily remain non-local;
- real local/CI parity remains unproved until workflows exist.

## Requirements

- `UMS-DEL-001`
- `UMS-QLT-001`
- `UMS-EXE-001`
- `UMS-SEC-003`

## Evidence

- `docs/artifacts/003-authority-capability-evidence.md`
- `docs/architecture/authority-map.md`
- `docs/journal/003-authority-boundaries.md`

## Assumptions

- repository commands can express the engineering work that should be reproducible locally.
- GitHub Actions remains an acceptable hosting/security surface for the repository.

## Reconsideration criteria

Revisit this decision if:

- critical validation cannot be represented through shared repository commands safely;
- CI-only task semantics become unavoidable and cannot be separated as hosted concerns;
- another CI platform becomes preferable while preserving or improving the same authority boundary.

## Relationships

- Supersedes: —
- Superseded by: —
- Related ADRs: `ADR-0002`, `ADR-0006`, `ADR-0009`
