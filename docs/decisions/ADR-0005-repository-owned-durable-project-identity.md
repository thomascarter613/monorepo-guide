# ADR-0005: Keep durable project identity repository-owned

Status: **Accepted**  
Date: 2026-08-30  
Supersedes: —  
Superseded by: —

## Context

Article 1 defined `Project` independently of any tool workspace or package format. Final review of Article 3 caught that the first authority map draft bundled durable project identity with Moon's project graph, which would make replacing Moon an identity migration rather than only an orchestrator migration.

## Decision

Durable project identity and stable project metadata are repository-owned concerns. Moon may map that identity into its operational project model, and Nx/Bun/ecosystem tools may reference or transform related metadata, but no tool-specific project ID or package manifest may become the sole durable repository project identity by default.

The exact durable representation is deferred to the repository-kernel increment.

## Rationale

Stable project identity should outlive individual package managers, orchestrators, generators, and languages. Deferring the physical representation avoids freezing a schema before the repository kernel has enough implementation evidence.

## Consequences

### Positive

- orchestrator replacement does not redefine what projects are;
- non-JavaScript projects can participate without fake `package.json` identity;
- generators can target one repository-level identity contract;
- future machine-readable introspection has a stable conceptual owner.

### Negative / constraints accepted

- the project-identity representation remains an unresolved implementation obligation;
- Moon IDs and ecosystem package names may require explicit mapping;
- we must avoid duplicating metadata unnecessarily when the kernel is designed.

## Requirements

- `UMS-WSP-001`
- `UMS-PLY-001`
- `UMS-PLY-002`
- `REQ-003-02`
- `REQ-004-09`

## Evidence

- `docs/architecture/system-model.md`
- `docs/architecture/authority-map.md`
- `docs/journal/003-authority-boundaries.md`
- Article 3 final review commits `8b4384e810a5cedcb3091550d290ce4821cb9d19` and `18eac99295dad13cf8ccc907561579e785e32a6d`

## Assumptions

- a small repository-owned identity contract can be created without building a universal ontology;
- tool adapters/mappings will remain simpler than letting each tool redefine durable identity.

## Reconsideration criteria

Revisit this decision if:

- the repository-owned representation duplicates tool metadata without creating durable value;
- stable cross-tool identity can be derived reliably without persistent repository-owned metadata;
- the identity contract grows into a maintenance burden disproportionate to rename/migration/polyglot benefits.

## Relationships

- Supersedes: —
- Superseded by: —
- Related ADRs: `ADR-0002`, `ADR-0006`, `ADR-0007`
