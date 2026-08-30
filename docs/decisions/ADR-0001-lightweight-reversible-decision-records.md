# ADR-0001: Adopt lightweight reversible architecture decision records

Status: **Accepted**  
Date: 2026-08-30  
Supersedes: —  
Superseded by: —

## Context

The repository already preserves requirements, journals, artifacts, verification, and current-state architecture, but it lacked a complete lifecycle for consequential architecture decisions. Article 3 introduced decisions with explicit exit criteria, making the gap concrete.

## Decision

Use lightweight Markdown ADRs under `docs/decisions/` for consequential architecture decisions. ADRs remain historical records; current-state architecture remains separate. Decisions use the lifecycle and supersession rules in `docs/architecture/decision-system.md`.

## Rationale

A file-based ADR system matches the repository's docs-as-code engineering record, costs little to operate, works before any custom tooling exists, and preserves rationale without forcing the architecture into a database or bespoke governance system.

## Consequences

### Positive

- consequential decisions receive stable identities and durable rationale;
- architecture can change through explicit supersession instead of history rewriting;
- decisions can link directly to requirements/evidence;
- the system works for a solo repository and can support richer review later.

### Negative / constraints accepted

- maintainers must keep decision indexes/links coherent until automation exists;
- the repository gains another artifact type that must remain distinct from journals and architecture docs.

## Requirements

- `REQ-004-01`
- `REQ-004-02`
- `REQ-004-03`
- `REQ-004-06`
- `REQ-004-07`
- `UMS-GEN-003`

## Evidence

- `docs/journal/004-reversible-architecture.md`
- `docs/architecture/decision-system.md`
- `METHODOLOGY.md`
- https://github.com/architecture-decision-record/architecture-decision-record
- https://github.com/adr/madr

## Assumptions

- Markdown/Git remain sufficient for the current decision volume.
- Most architecture decisions can be understood through linked evidence rather than embedded research reports.

## Reconsideration criteria

Revisit this decision if:

- decision volume/relationships become difficult to navigate reliably in files;
- automated queries needed by the starter cannot be derived safely from the file format;
- the ADR process creates enough ceremony that consequential decisions are routinely skipped;
- a simpler artifact model can preserve the same historical/reversibility properties.

## Relationships

- Supersedes: —
- Superseded by: —
- Related ADRs: `ADR-0002` through `ADR-0009`
