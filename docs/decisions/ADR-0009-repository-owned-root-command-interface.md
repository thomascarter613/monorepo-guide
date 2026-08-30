# ADR-0009: Keep the stable root command interface repository-owned

Status: **Accepted**  
Date: 2026-08-30  
Supersedes: —  
Superseded by: —

## Context

Humans, CI, IDE integrations, and AI agents need a stable repository-facing interface for common operations such as bootstrap, doctor, check, test, build, generate, and migrate. If callers must directly encode every internal tool invocation, changing an authority becomes a repository-wide client migration and the public workflow leaks implementation details.

## Decision

The repository itself owns the stable root command contract. The implementation behind each command delegates to the concern authority—mise, Bun, Moon, Nx, ecosystem-native tools, or hosted CI as appropriate.

The root interface must not duplicate task ordering, package semantics, affected analysis, or other authoritative logic in wrapper glue.

## Rationale

A repository-owned facade gives humans and automation a stable contract while preserving replaceable internal authorities. It also provides one location for consistent help, diagnostics, argument semantics, and agent-facing instructions.

## Consequences

### Positive

- internal tools can change with less caller churn;
- local/CI/agent workflows can share one documented entry point;
- user-facing diagnostics can add context without becoming a shadow implementation;
- the repository can eventually self-document supported operations.

### Negative / constraints accepted

- wrapper design must remain thin and avoid recreating authority logic;
- an additional command layer has maintenance cost;
- exact command names/implementation remain deferred to repository-kernel/execution increments.

## Requirements

- `UMS-ENV-002`
- `UMS-ENV-003`
- `UMS-QLT-001`
- `UMS-DEL-001`

## Evidence

- `docs/architecture/authority-map.md`
- `docs/journal/003-authority-boundaries.md`
- `docs/architecture/system-model.md`

## Assumptions

- a thin repository-owned interface can delegate without obscuring underlying tool diagnostics.
- stable command semantics provide more value than requiring callers to use tool-specific CLI surfaces directly.

## Reconsideration criteria

Revisit this decision if:

- wrappers repeatedly obscure useful diagnostics or become large shadow implementations;
- one underlying tool can provide the stable multi-concern public interface without unacceptable lock-in or authority collapse;
- maintaining the facade creates more cognitive load than it removes.

## Relationships

- Supersedes: —
- Superseded by: —
- Related ADRs: `ADR-0002`, `ADR-0003`, `ADR-0004`, `ADR-0006`, `ADR-0007`, `ADR-0008`
