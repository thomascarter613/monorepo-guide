# ADR-0007: Restrict Nx authority to repository transformations

Status: **Accepted**  
Date: 2026-08-30  
Supersedes: —  
Superseded by: —

## Context

The starter needs deterministic generators, codemods, and versioned migrations. Nx has mature facilities for those concerns but also provides its own project/task graph, affected execution, executors, and caching. Using those routine execution capabilities alongside Moon would create a shadow task authority.

## Decision

Authorize Nx for repository transformations only: project generators, scaffolding, codemods, and versioned migrations/upgrades. Nx may inspect and modify repository state during a transformation, but normal build/test/lint/typecheck/affected/cache workflows must not depend on Nx execution state.

Generated state must target the repository's actual authorities, including Bun-owned package metadata, repository-owned project identity, and Moon-owned operational task metadata.

## Rationale

This reuses mature transformation/migration machinery without coupling routine repository execution to a second orchestrator. It also leaves room to replace Nx with a smaller custom layer if the actual generator contracts later prove simple enough.

## Consequences

### Positive

- generation/migration infrastructure is available without surrendering task authority;
- generated repositories can operate normally without Nx task execution;
- transformation tooling can evolve independently of the work DAG.

### Negative / constraints accepted

- Nx must be configured/invoked carefully to avoid inferred task authority becoming required state;
- some plugin conveniences may be intentionally unused;
- the value of Nx in this constrained role remains unproved until real generators/migrations exist.

## Requirements

- `UMS-GEN-001`
- `UMS-GEN-002`
- `UMS-GEN-003`
- `UMS-EXE-001`

## Evidence

- `docs/artifacts/003-authority-capability-evidence.md`
- `docs/architecture/authority-map.md`
- `docs/journal/003-authority-boundaries.md`

## Assumptions

- Nx generators/migrations can remain useful without making Nx routine task execution mandatory.
- Nx project metadata needed for transformations can be kept small enough to avoid a second durable project model.

## Reconsideration criteria

Revisit this decision if:

- generator/migration use requires routine `nx run`/`nx affected`/Nx cache semantics;
- synchronizing Nx metadata creates an unacceptable second project model;
- deterministic noninteractive generation is difficult to test independently;
- migrations are tightly coupled to Nx executors rather than repository-state transformations;
- a smaller custom transformation layer becomes clearly simpler after real contracts stabilize.

## Relationships

- Supersedes: —
- Superseded by: —
- Related ADRs: `ADR-0002`, `ADR-0005`, `ADR-0006`, `ADR-0009`
