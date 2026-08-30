# ADR-0003: Use mise as executable/tool version authority

Status: **Accepted**  
Date: 2026-08-30  
Supersedes: —  
Superseded by: —

## Context

The starter needs repository-declared supported tool/runtime versions. Both mise and Moon/proto can manage executables, while CI setup actions and outer environments can also pin versions. Multiple independent pins would undermine reproducibility and make supported-version questions ambiguous.

## Decision

Use mise as the repository authority for supported executable/tool version declaration, resolution, and installation. Moon may consume those executables for ecosystem/project/task semantics but must not independently pin/install the same tools as a second authority.

## Rationale

mise is purpose-built around repository tool/environment declaration and currently provides a project tool lockfile. Keeping executable authority outside the task orchestrator also reduces coupling between environment setup and the chosen task graph implementation.

## Consequences

### Positive

- one supported executable-version contract for local/CI use;
- Moon remains replaceable as an orchestrator;
- outer environments such as Nix/Dev Containers can later be evaluated around a clear inner authority.

### Negative / constraints accepted

- Moon/proto convenience may need to be disabled/constrained;
- some tool backends may have different lock/integrity guarantees;
- exact integration remains unproved until Arc III/V implementation.

## Requirements

- `UMS-ENV-001`
- `UMS-ENV-002`
- `UMS-ENV-003`
- `REQ-003-03`

## Evidence

- `docs/artifacts/003-authority-capability-evidence.md`
- `docs/architecture/authority-map.md`
- `docs/journal/003-authority-boundaries.md`

## Assumptions

- Moon can consume environment/PATH-provided executables without losing required graph/task behavior.
- mise can provide sufficiently reproducible installs across supported environments.

## Reconsideration criteria

Revisit this decision if:

- mise cannot reproduce required tools across supported platforms reliably;
- Moon ecosystem correctness requires proto-managed versions that cannot safely consume mise-provided tools;
- Nix/Dev Container integration creates irreconcilable double-pinning complexity;
- the combined boundary is materially more fragile than a single end-to-end toolchain authority.

## Relationships

- Supersedes: —
- Superseded by: —
- Related ADRs: `ADR-0002`, `ADR-0006`
