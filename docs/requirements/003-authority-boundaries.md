# 003 — Choosing Authorities

Status: **Draft**

## Objective

Choose provisional architectural authorities for the repository's overlapping concerns without confusing a tool's capabilities with the authority the repository grants it.

This increment converts the tool-neutral requirements established in Article 2 into an explicit responsibility map for environment/tool versions, JavaScript workspace/package operations, project/task orchestration, generation/migrations, and hosted CI orchestration.

The result is still provisional architecture. Later implementation articles must prove that the selected boundaries work under real package, task, cache, polyglot, CI, and migration scenarios.

## Requirements

### REQ-003-01 — Evaluate current candidate capabilities from first-party evidence

Acceptance criteria:

- Bun, mise, Moon, Nx, and GitHub Actions are evaluated using current first-party documentation;
- overlapping capabilities are recorded rather than omitted to make the responsibility map look cleaner;
- the evidence record distinguishes documented capability from repository authority;
- time-sensitive findings record the research date and source URLs.

### REQ-003-02 — Assign one provisional authority per core concern

Acceptance criteria:

- supported tool/runtime version resolution and installation has one authority;
- JavaScript workspace/package installation and lockfile state has one authority;
- repository project/task graph, affected analysis, and task-result caching have one authority;
- project generation/migration/codemod transformations have one authority;
- hosted CI event/runner/permission orchestration has one authority;
- root developer commands remain repository-owned public interfaces that delegate rather than become shadow implementations.

### REQ-003-03 — Separate ecosystem integration from executable-version authority

Acceptance criteria:

- the architecture explicitly distinguishes Moon ecosystem/toolchain integration from Moon's optional proto-backed runtime installation capability;
- the intended mise/Moon boundary states who installs/versions binaries and who consumes them for project/task semantics;
- Moon's ability to install dependencies is treated as an overlap that must be disabled or constrained when Bun is the package-install authority;
- later implementation obligations for proving this boundary are listed.

### REQ-003-04 — Prevent shadow task graphs

Acceptance criteria:

- Moon is selected or rejected explicitly as the routine repository task-graph authority;
- mise's dependency-aware task runner is acknowledged and constrained if Moon remains authoritative;
- Nx task execution, affected execution, and caching capabilities are acknowledged and constrained if Nx remains generation-focused;
- Bun scripts may remain project-local commands but do not become a second repository-wide dependency graph;
- CI job dependencies are distinguished from the repository's task graph.

### REQ-003-05 — Define the CI orchestration boundary

Acceptance criteria:

- GitHub Actions owns hosted event routing, runners, permissions, secrets, matrices, and other platform-only concerns;
- equivalent build/test/lint/typecheck policy is expected to delegate to repository commands/task definitions;
- CI-specific job graphs are allowed only where they represent hosted orchestration rather than duplicated repository task semantics;
- least-privilege workflow permissions remain part of the delivery contract.

### REQ-003-06 — Define explicit exit criteria for every authority choice

Acceptance criteria:

- each selected authority has evidence that would force reconsideration;
- the architecture does not claim later implementation proof that has not yet occurred;
- Moon's still-unproven Python/polyglot path is called out as a future falsification point rather than assumed successful;
- Nx generation-only viability remains an explicit future test.

### REQ-003-07 — Trace authority choices to product requirements

Acceptance criteria:

- the authority map cites relevant `UMS-*` requirements;
- at minimum `UMS-ENV-001`, `UMS-WSP-002`, `UMS-EXE-001`, `UMS-EXE-003`, `UMS-EXE-004`, `UMS-GEN-002`, and `UMS-DEL-001` are connected to the decision;
- requirement evidence states are updated only where this increment genuinely adds evidence.

### REQ-003-08 — Preserve a durable capability/authority evidence artifact

Acceptance criteria:

- a repository artifact records current first-party capability findings and source URLs;
- the artifact includes the key overlaps that create architectural risk;
- the reader-facing article can summarize the findings without being the only evidence location.

### REQ-003-09 — Produce a current-state authority map

Acceptance criteria:

- `docs/architecture/authority-map.md` describes the current responsibility split;
- it lists allowed participation and prohibited shadow authority for each concern;
- it explains cross-tool handoffs such as mise → Moon, Bun → Moon, Nx → Moon/Bun, and GitHub Actions → repository commands;
- it remains understandable if individual tools are later replaced.

### REQ-003-10 — Publish and verify Article 3 as a reproducible checkpoint

Acceptance criteria:

- the reader-facing Article 3 explains the reasoning, overlaps, decisions, unresolved risks, and later proof obligations;
- the journal records actual findings and corrections from the increment;
- verification maps every Article 3 requirement to evidence;
- series metadata/indexes identify the increment and its immutable starting commit;
- no implementation tooling is presented as installed/configured unless it actually exists in the branch.

## Out of scope

- installing Bun, mise, Moon, Nx, or any quality tool into the repository;
- a full benchmark comparison of Moon vs Nx vs Turborepo vs Bazel (reserved for the execution-system arc);
- proving Bun workspace behavior with real packages (reserved for the workspace/dependency arc);
- proving Moon affected/cache correctness with real tasks (reserved for the execution-system arc);
- implementing Nx generators or migrations (reserved for the generation/evolution arc);
- implementing GitHub Actions workflows (reserved for the delivery arc);
- finalizing the Dev Container/Nix/native-environment boundary;
- claiming broad polyglot support before the Rust/Python proof increments.

## Verification approach

Structural/manual verification plus dated first-party documentation evidence. See `../verification/003-authority-boundaries.md` once completed.
