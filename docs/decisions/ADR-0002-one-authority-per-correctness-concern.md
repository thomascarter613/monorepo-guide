# ADR-0002: Assign one authority per repository correctness concern

Status: **Accepted**  
Date: 2026-08-30  
Supersedes: —  
Superseded by: —

## Context

The selected tools intentionally overlap: mise and Moon can both manage tools/tasks; Moon and Nx can both model project/task graphs and caches; Bun has runtime/package/test/build capabilities; GitHub Actions has its own job graph. Without explicit authority boundaries, the same correctness decision can acquire multiple sources of truth.

## Decision

For each repository concern that materially affects correctness, designate one canonical authority. Other tools may consume, inspect, invoke, host, or transform authoritative state but must not silently become another required source of truth for the same concern.

## Rationale

This allows us to combine specialized tools without forcing one product to own the entire developer platform and without accepting ambiguous overlapping state.

## Consequences

### Positive

- overlapping capabilities can coexist under explicit boundaries;
- authority can be replaced concern-by-concern;
- divergence is easier to detect/reason about;
- the architecture remains more portable than a single-tool worldview.

### Negative / constraints accepted

- some convenient automatic features must be disabled or constrained;
- integrations require care to distinguish participation from ownership;
- authority boundaries must be documented/tested as implementation appears.

## Requirements

- `UMS-EXE-001`
- `UMS-DEL-001`
- `REQ-003-02`
- `REQ-003-04`
- `REQ-004-09`

## Evidence

- `docs/architecture/authority-map.md`
- `docs/artifacts/003-authority-capability-evidence.md`
- `docs/journal/003-authority-boundaries.md`
- `docs/verification/003-authority-boundaries.md`

## Assumptions

- concern boundaries can remain clear enough that ownership is meaningful;
- the integration cost of constraining overlap remains lower than the ambiguity created by competing authorities.

## Reconsideration criteria

Revisit this decision if:

- real implementation proves certain concerns cannot be separated without brittle duplicated configuration;
- one integrated authority demonstrably provides a simpler and more correct model without unacceptable lock-in;
- the concern decomposition itself proves wrong under polyglot or CI/runtime evidence.

## Relationships

- Supersedes: —
- Superseded by: —
- Related ADRs: `ADR-0003` through `ADR-0009`
