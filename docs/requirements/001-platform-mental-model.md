# 001 — Define the Repository Platform Mental Model

Status: **Draft / partially retrospectively captured after protocol adoption**

## Objective

Define a tool-independent vocabulary for the repository as a developer platform so later implementation decisions can assign authority without allowing package-manager, orchestrator, version-manager, generator, or CI terminology to become the architecture accidentally.

## Requirements

### REQ-001-01 — Distinguish repository, workspace, project, and package

Acceptance criteria:

- repository and workspace are not treated as synonyms;
- project and package are not treated as synonyms;
- the model permits first-class non-JavaScript projects that do not participate in the Bun workspace.

### REQ-001-02 — Distinguish persistent and invocation-specific graphs

Acceptance criteria:

- package/dependency graph, project graph, and task graph are separately defined;
- the model explains that one project graph can produce many task graphs;
- changed files, affected projects, and affected targets are not treated as equivalent sets.

### REQ-001-03 — Define execution/caching semantics at the conceptual level

Acceptance criteria:

- task and target are defined;
- cache reuse is described as a correctness claim based on equivalent relevant inputs, not only a speed feature.

### REQ-001-04 — Define generation, migration, policy, and root-command concepts

Acceptance criteria:

- generators and migrations have distinct responsibilities;
- documented convention is distinguished from enforceable policy;
- root commands are treated as stable repository interfaces that delegate to authorities rather than becoming shadow authorities.

### REQ-001-05 — Identify tool-capability overlap before authority selection

Acceptance criteria:

- current first-party documentation for Bun, mise, Moon, and Nx is reviewed;
- overlapping capabilities are recorded explicitly;
- capability is distinguished from architectural authority;
- unresolved overlaps become future experiments instead of silently resolved assumptions.

### REQ-001-06 — Preserve a tool-independent architecture vocabulary

Acceptance criteria:

- current architecture concerns can still be named if Bun, mise, Moon, Nx, or GitHub Actions are later replaced;
- ambiguous terms such as `workspace` and `toolchain` are qualified by scope where necessary.

## Out of scope

- selecting final authority boundaries;
- installing any candidate tool;
- defining final directory taxonomy;
- implementing project discovery;
- implementing affected execution or caching;
- implementing automated validation of the system model.

## Verification approach

Structural/manual verification plus comparison against current first-party product documentation. See `../verification/001-platform-mental-model.md`.
