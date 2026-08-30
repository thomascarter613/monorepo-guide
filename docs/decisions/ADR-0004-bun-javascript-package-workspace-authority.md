# ADR-0004: Use Bun as JavaScript package/workspace authority

Status: **Accepted**  
Date: 2026-08-30  
Supersedes: —  
Superseded by: —

## Context

The TypeScript-first workspace needs one authority for JavaScript package metadata, dependency resolution/installation, workspace semantics, and lock state. Moon can inspect JavaScript dependency metadata and can automatically install dependencies, but allowing both Moon and Bun to own installation would blur reproducibility and mutation boundaries.

## Decision

Use Bun as the JavaScript package/workspace dependency authority. `package.json` workspace/package metadata and Bun lock state define JavaScript dependency state; Bun performs dependency installation. Moon may consume that state for graph/hash/task semantics but must not independently become the package-install authority.

## Rationale

Bun provides the intended TypeScript-first runtime/package experience while allowing us to adopt only the roles we need. Separating package authority from orchestration avoids making `moon run` an implicit package-management operation.

## Consequences

### Positive

- explicit package-install path and lock authority;
- package semantics remain ecosystem-native;
- Moon can remain orchestration-focused;
- Bun's test/bundler capabilities remain optional rather than automatically authoritative.

### Negative / constraints accepted

- Moon automatic dependency installation must be disabled/constrained;
- Bun workspace/lock/release behavior still requires later executable proof;
- other ecosystems require their own native package authorities.

## Requirements

- `UMS-WSP-002`
- `UMS-WSP-004`
- `UMS-PLY-001`
- `REQ-003-03`

## Evidence

- `docs/artifacts/003-authority-capability-evidence.md`
- `docs/architecture/authority-map.md`
- `docs/journal/003-authority-boundaries.md`

## Assumptions

- Bun will satisfy representative internal-workspace, frozen-lock/install, and release scenarios.
- disabling Moon install behavior will not break required dependency inference/hash semantics.

## Reconsideration criteria

Revisit this decision if:

- Bun fails representative workspace/linking/lockfile/release requirements;
- its install semantics create unacceptable reproducibility or compatibility problems;
- constraining Moon dependency installation breaks essential graph/task integration;
- another JavaScript package authority materially better satisfies the same tool-neutral requirements.

## Relationships

- Supersedes: —
- Superseded by: —
- Related ADRs: `ADR-0002`, `ADR-0006`
