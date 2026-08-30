# ADR-0006: Use Moon as operational project/task/affected/cache authority

Status: **Accepted**  
Date: 2026-08-30  
Supersedes: —  
Superseded by: —

## Context

The repository needs one operational authority for project/task relationships, dependency-aware execution, affected analysis, declared task inputs/outputs/environment influence, and task-result cache semantics. mise and Nx also have task graphs; CI can hand-write affected logic; Bun scripts can encode local commands.

## Decision

Use Moon as the operational repository project/task graph authority, including routine task dependency ordering, affected project/target analysis, task execution, task hashing/cache semantics, and graph introspection.

Moon does not own durable repository project identity, ecosystem package semantics, or executable version selection.

## Rationale

Moon matches the task-centric, polyglot-oriented shape established by the tool-neutral requirements while allowing project-local ecosystem commands to remain native. Its graph/affected/cache model can serve local and CI execution from one repository contract if later fixtures validate correctness.

## Consequences

### Positive

- one routine repository work DAG;
- root commands and CI can delegate to the same task model;
- affected/cache behavior has one correctness authority;
- ecosystem-native commands remain task leaves rather than being replaced.

### Negative / constraints accepted

- mise and Nx task capabilities must remain constrained;
- Moon's proto/dependency-install features must respect mise/Bun authorities;
- Python support and cross-language behavior remain real falsification points;
- cache correctness requires explicit input/output/environment evidence later.

## Requirements

- `UMS-EXE-001`
- `UMS-EXE-002`
- `UMS-EXE-003`
- `UMS-EXE-004`
- `UMS-OBS-001`
- `UMS-PLY-001`

## Evidence

- `docs/artifacts/003-authority-capability-evidence.md`
- `docs/architecture/authority-map.md`
- `docs/journal/003-authority-boundaries.md`
- `docs/verification/003-authority-boundaries.md`

## Assumptions

- Moon can consume mise-provided executables and Bun-owned package state without becoming a competing authority.
- affected/cache semantics can be made correct for global config, transitive dependencies, environment inputs, and outputs.
- non-JavaScript projects can participate without root architecture redesign.

## Reconsideration criteria

Revisit this decision if:

- affected analysis produces unacceptable false negatives/positives in representative fixtures;
- cache keys cannot account for material inputs correctly;
- Python/Rust/cross-language projects require substantial second-class workarounds;
- local/CI parity becomes materially harder than with alternatives;
- measured orchestration overhead is unacceptable;
- keeping Nx/mise from becoming shadow graphs creates excessive configuration duplication.

## Relationships

- Supersedes: —
- Superseded by: —
- Related ADRs: `ADR-0002`, `ADR-0003`, `ADR-0004`, `ADR-0005`, `ADR-0007`, `ADR-0008`, `ADR-0009`
