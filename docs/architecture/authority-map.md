# Repository Authority Map

Status: **Working architecture contract — provisional until implementation evidence**

This document defines which repository concern each major tool is currently authorized to own.

It exists because our chosen tools overlap heavily. The repository does not avoid overlap by pretending the features do not exist. It avoids conflicting sources of truth by assigning **authority per concern**.

> A tool may understand, consume, inspect, invoke, or transform state that another tool owns. Participation is not authority.

The current map was established in Article 3 from the product requirements in `requirements-ledger.md` and dated first-party capability evidence in `../artifacts/003-authority-capability-evidence.md`.

## Core rule

For a concern that affects normal repository correctness, there should be one canonical authority.

A second tool may:

- consume authoritative state;
- infer useful read-only relationships from it;
- invoke the authority;
- transform the authority's configuration through a controlled generator/migration;
- host or schedule the authority's execution;
- cache a different layer of state with an explicitly different semantic role.

A second tool must not silently become another required source of truth for the same concern.

---

# Authority table

| Concern | Current authority | Allowed participants | Shadow authority we prohibit |
| --- | --- | --- | --- |
| Git history / immutable source checkpoint | Git | GitHub hosts/reviews; series metadata records SHAs | mutable branch names as the only historical identity |
| repository product requirements | `docs/architecture/requirements-ledger.md` | articles, ADRs, verification records | tool configuration being treated as an unstated requirement |
| supported executable/tool version declaration + installation | **mise** | Dev Containers/Nix may provision an outer environment later; Moon consumes executables | Moon/proto independently pinning the same tools; ad-hoc CI setup versions |
| JavaScript package/workspace dependency state | **Bun** | Moon reads package metadata/lock state; Nx generators may edit manifests | Moon automatic dependency install as an independent policy; Nx package execution authority |
| default JavaScript runtime for Bun-oriented projects | **Bun** | mise selects the installed Bun version | Moon/proto owning a second Bun version pin |
| durable repository project identity / stable project metadata | **repository-owned project contract** | ecosystem manifests provide evidence; Moon consumes/derives orchestration state; Nx generators may transform the contract | Moon/Nx/package-manager-specific identifiers becoming the only durable project identity |
| operational repository project graph | **Moon** | Bun/Cargo/Python metadata may provide dependency evidence; Nx may inspect during transformations | Nx project graph becoming required routine execution state |
| routine task DAG and dependency ordering | **Moon** | Bun/project commands are task leaves; root commands delegate | mise task DAG or Nx task pipeline duplicating routine build/test/lint/typecheck ordering |
| affected project/target analysis | **Moon** | Git supplies change history | handwritten CI changed-file logic or Nx affected becoming a parallel authoritative calculation |
| task-result hashing/cache semantics | **Moon** | hosted storage/transport may be added later | Nx cache or generic CI path cache independently representing task correctness |
| generator/scaffolding transformations | **Nx** | repository/Bun/Moon config is transformed as output | ad-hoc copy/paste generators that bypass repository contracts |
| repository migrations/codemods | **Nx** | migration may transform any owned config under explicit scope | recreate-the-repo upgrades or parallel migration systems without a defined boundary |
| hosted CI event/runner/permission orchestration | **GitHub Actions** | repository root commands and Moon execute repository work | workflow YAML reimplementing project/task dependency semantics |
| developer-facing root command API | **repository-owned interface** | delegates to mise/Bun/Moon/Nx as appropriate | exposing every tool-specific invocation as the only supported UX |
| ecosystem-native package semantics for future Rust/Python/etc. | **ecosystem-native authority** | mise provisions tools; Moon orchestrates tasks | forcing every ecosystem through JavaScript package semantics |

The phrase **repository-owned interface** means the stable command surface is part of the repository contract even when its implementation delegates immediately to another authority. The implementation mechanism is deferred to the repository-kernel/execution articles.

The phrase **repository-owned project contract** deliberately does not yet prescribe a manifest format. It means that durable project identity must belong to repository state that can survive replacement of Moon, Nx, Bun, or another implementation tool. Article 3 assigns Moon the operational project graph used for orchestration, not the permanent identity of the projects being orchestrated. The exact representation belongs to the repository-kernel work where we can design and test it without smuggling a tool-specific ID into the architecture.

---

# Boundary 1 — mise owns executable versions; Moon consumes them

Relevant product requirements:

- `UMS-ENV-001`
- `UMS-ENV-002`
- `UMS-ENV-003`
- `UMS-EXE-001`

## Why this boundary exists

mise and Moon can both manage tool versions.

mise has project configuration and a tool lockfile designed around resolving/installing executable versions. Moon v2's toolchain can also use proto to download/install runtimes and package managers.

If both are configured as independent version authorities, a repository can have two answers to a basic question:

> Which Bun/Rust/Python/etc. executable is supported here?

That is exactly the ambiguity this architecture is trying to remove.

## Current contract

**mise owns version selection, resolution, and installation for supported development/CI executables.**

Moon may enable ecosystem/toolchain integration to obtain language-aware graphing, hashing, command execution, and metadata behavior, but it should consume the executable made available by the supported environment rather than establish an independent version source.

The implementation articles should test a configuration equivalent in intent to:

- no independent Moon/proto version pin for tools already owned by mise;
- Moon language integrations enabled as needed;
- binaries available through the mise-controlled environment/PATH;
- explicit failure when the supported executable is absent or wrong rather than silently downloading an unreviewed second copy.

Potential Moon mechanisms to evaluate include leaving version fields unset, disabling `.prototools` version inheritance for relevant toolchains, or forcing global/PATH binaries where necessary. We do **not** freeze the exact option set here before running the implementation experiment.

## Reconsider this authority if

- mise cannot reproduce required tools across supported developer/CI platforms with acceptable reliability;
- lockfile/backend limitations create material nondeterminism we cannot mitigate;
- Moon ecosystem correctness requires proto-managed versions in a way that cannot safely consume mise-provided tools;
- the combined configuration becomes more complex or fragile than giving Moon end-to-end toolchain authority;
- later Nix/Dev Container requirements expose an irreconcilable double-pinning problem.

---

# Boundary 2 — Bun owns JavaScript dependency state; Moon may understand it

Relevant product requirements:

- `UMS-WSP-002`
- `UMS-WSP-003`
- `UMS-WSP-004`
- `UMS-EXE-001`
- `UMS-EXE-002`

## Current contract

For the JavaScript/TypeScript workspace:

- `package.json`/workspace metadata and the Bun lockfile describe package dependency state;
- Bun performs dependency resolution/install operations;
- Moon may parse package metadata and the lockfile to infer project relationships, calculate task inputs, and execute Bun-backed commands;
- Moon must not independently become the package-install policy authority.

Moon's current Bun/JavaScript integration can automatically install dependencies when manifests/lock state change. If Bun remains our package authority, later Moon configuration should disable that automatic installation behavior and make dependency installation an explicit repository/bootstrap operation.

This separation gives us a useful distinction:

```text
Bun owns:      what package dependency state is installed
Moon consumes: what that state means for project/task relationships
```

## Why not let Moon auto-install anyway?

Convenience is not the problem. Ambiguous ownership is.

If `moon run app:test` may mutate `node_modules` or install a dependency implicitly while `bun install --frozen-lockfile` is supposed to be the authoritative install path, then a task-run invocation is also a package-management operation. That makes debugging reproducibility and CI/local parity harder.

We may revisit this after real implementation evidence, but the default architecture is explicit installation.

## Reconsider this boundary if

- disabling Moon dependency installation breaks required Bun graph/hash integration;
- explicit install creates unacceptable or error-prone workflows that Moon can safely solve without creating a second source of truth;
- Bun fails the representative workspace/linking/lockfile/release requirements later in Arc IV.

---

# Boundary 3 — Moon owns operational project/task/affected/cache semantics

Relevant product requirements:

- `UMS-WSP-001`
- `UMS-EXE-001`
- `UMS-EXE-002`
- `UMS-EXE-003`
- `UMS-EXE-004`
- `UMS-OBS-001`
- `UMS-PLY-001`

## Current contract

Moon is the provisional authority for:

- the operational repository project graph used by orchestration;
- project relationships needed by orchestration;
- routine task definitions;
- task dependency ordering;
- affected project/target analysis;
- task input/output/environment declarations;
- task-result hashing and cache semantics;
- task graph introspection.

Durable **project identity itself remains repository-owned**. Moon may assign or consume tool-specific project identifiers as part of its operational model, but those identifiers must not become the only representation of project identity that survives across orchestrator replacement, regeneration, or migration. The repository-kernel increment will decide the smallest durable project contract worth maintaining.

This does **not** mean Moon owns ecosystem package semantics. Cargo, Bun, Python packaging tools, and other ecosystem-native systems remain authoritative for their local concerns.

It also does not mean other tools can never contain a graph. Nx may construct a graph while a generator runs; GitHub Actions has a job graph; mise can model bootstrap dependencies. The invariant is that **normal repository work has one authoritative operational project/task DAG**.

## Bun scripts

Projects may keep ecosystem-local commands/scripts when they are useful.

A Bun `package.json` script can be the leaf command Moon executes, for example conceptually:

```text
Moon target: app:test
    -> bun run test
        -> test runner
```

The script must not become a second repository-level dependency graph that decides which other projects/tasks must run first.

## mise tasks

mise tasks are permitted for bootstrap/environment operations that exist before the repository task graph can reasonably run.

Examples that may be legitimate later:

- install/verify toolchain;
- enter or inspect supported environment state;
- initial bootstrap that installs the orchestrator itself.

Examples that would violate the current boundary:

- a mise `check` DAG independently ordering lint → build → test across projects;
- mise affected/freshness rules deciding which application/library tasks should run in CI.

## Nx tasks

Normal repository commands should not require:

- `nx run` / `nx run-many` for build/test/lint/typecheck;
- `nx affected` as the authoritative affected calculation;
- Nx computation cache for routine task correctness;
- Nx Cloud as a required task execution service.

If later generation/migration work cannot avoid such dependencies, that is evidence against the current Nx boundary.

## Reconsider Moon authority if

- representative TypeScript tasks cannot be modeled without material duplication;
- affected analysis misses required dependents or global/config invalidation scenarios;
- cache keys cannot account for our relevant source/config/environment inputs;
- cross-language project/task relationships require root architecture workarounds that violate the tool-independent system model;
- Moon-specific project identifiers or metadata become impossible to derive from/export into the repository-owned project contract without unacceptable duplication;
- Python integration remains materially second-class or unstable in the tested architecture;
- CI/local task parity becomes substantially harder than with an alternative;
- measured overhead is unacceptable for the repository size/profile.

A full Moon/Nx/Turborepo/Bazel execution comparison remains scheduled for the execution-system arc; this Article 3 decision is an authority boundary, not the final performance proof.

---

# Boundary 4 — Nx owns transformations, not routine execution

Relevant product requirements:

- `UMS-GEN-001`
- `UMS-GEN-002`
- `UMS-GEN-003`
- `UMS-EXE-001`

## Current contract

Nx is provisionally authorized for:

- deterministic project generators;
- repository scaffolding transformations;
- codemods;
- versioned migrations/upgrades.

Nx may read repository/project metadata necessary to make those transformations. It may update the repository-owned project contract, Bun manifests, Moon project configuration, TypeScript configuration, documentation, or other owned state when the generator/migration contract says so.

The output must be usable by the repository's actual authorities without requiring a second routine execution system.

Conceptually:

```text
Nx generator
   ├─ creates project files
   ├─ updates durable project identity/metadata -> repository-owned state
   ├─ updates package/workspace metadata -> Bun-owned state
   ├─ creates/updates orchestration project/task metadata -> Moon-owned state
   └─ updates docs/policy metadata

Normal future workflow
   -> does not require Nx task execution
```

## Why Nx instead of a custom generator immediately?

The requirement is not “use Nx.” The attraction is that Nx already has mature concepts for generators, plugins, migrations, and codemod-style repository evolution. Reusing that transformation infrastructure may be lower-risk than building a custom lifecycle engine before we know our generator/migration requirements well enough.

That benefit is only real if we can prevent its broader task/graph/caching platform from becoming a parallel authority.

## Reconsider Nx transformation authority if

- generator/migration use requires routine Nx task execution;
- keeping `nx.json`/plugin metadata synchronized creates an unacceptable second project model;
- deterministic noninteractive generation is difficult to test independently;
- migrations are tightly coupled to Nx-owned executors rather than repository state transformations;
- a smaller custom transformation layer becomes clearly simpler after our actual generator contracts stabilize.

---

# Boundary 5 — GitHub Actions owns hosted CI orchestration, not repository semantics

Relevant product requirements:

- `UMS-DEL-001`
- `UMS-SEC-003`
- `UMS-QLT-001`
- `UMS-EXE-001`

## GitHub Actions owns

- workflow event triggers;
- hosted/self-hosted runner selection;
- job-level OS/runtime environment provisioning;
- repository/environment secrets wiring;
- `GITHUB_TOKEN` permission scoping;
- matrices when the variation is genuinely a hosted environment concern;
- concurrency/cancellation policy;
- artifact upload/download;
- deployment environments/approval gates;
- privileged release job boundaries;
- reusable hosted workflow composition.

## GitHub Actions does not own

- which application depends on which library;
- which build must precede another build;
- the canonical definition of lint/typecheck/test/build;
- the canonical affected set;
- task-result cache validity.

The expected direction is:

```text
GitHub event
    ↓
workflow chooses runner / permissions / hosted job boundary
    ↓
repository command
    ↓
Moon task graph
    ↓
ecosystem command/tool
```

## Legitimate CI job dependencies

The one-task-graph principle does not outlaw `needs:`.

A CI job dependency is legitimate when it models a hosted concern that the repository task graph should not own, for example:

```text
untrusted validation
      ↓
privileged publish/release
```

or:

```text
build platform-specific artifact on runner A
build platform-specific artifact on runner B
              ↓
      combine/publish artifacts
```

It becomes duplication when workflow YAML independently models repository project/task ordering already represented in Moon.

## Reconsider this boundary if

- repository commands cannot express the equivalent local validation path;
- a hosted concern genuinely requires CI-only semantics that cannot safely be delegated;
- another CI platform replaces GitHub Actions while preserving the same architectural boundary.

The last condition is important: `UMS-DEL-001` is about the boundary, not GitHub product lock-in.

---

# Boundary 6 — The repository owns the developer-facing command contract

Relevant product requirements:

- `UMS-QLT-001`
- `UMS-ENV-002`
- `UMS-ENV-003`
- `UMS-DEL-001`

Humans, CI, IDE integrations, and AI agents should not need to memorize the entire internal authority map to perform common repository work.

The eventual root command surface may include operations conceptually like:

```text
bootstrap
doctor
check
test
build
generate
migrate
```

The names and exact implementation are deferred.

The important architectural rule is delegation:

- bootstrap/doctor may delegate heavily to mise/environment logic;
- package install delegates to Bun;
- check/test/build delegates to Moon targets;
- generate/migrate delegates to Nx transformations;
- CI calls the same repository-level operations where equivalent work exists.

A wrapper is not allowed to become a hidden second implementation of the task graph.

---

# Future ecosystem authority rule

The repository is TypeScript-first, not JavaScript-universal.

When Rust, Python, or another ecosystem arrives:

- durable project identity remains repository-owned rather than package-manager-specific;
- its native package/build metadata remains authoritative for ecosystem-local semantics;
- mise may provision the executable toolchain;
- Moon orchestrates the operational repository graph and cross-project task ordering;
- generators/migrations may update its files where supported;
- root commands provide a consistent entry point without pretending the ecosystems are identical.

For example, Cargo should remain authoritative for Rust packages even when Moon invokes Cargo tasks.

This is the principle we will test in the polyglot arc.

---

# Forbidden dependency directions between authorities

The following would violate the current architecture unless an explicit later decision supersedes this map:

1. **Moon → proto version authority for mise-owned tools** as an independent second pin.
2. **Moon → implicit JS package installation** when Bun install is the declared package-install authority.
3. **mise → routine repository task DAG** for build/test/lint/typecheck ordering.
4. **Nx → routine repository task/affected/cache authority**.
5. **GitHub Actions → independent repository project/task dependency definitions**.
6. **root wrapper → hand-maintained duplicate task ordering**.
7. **Bun workspace → universal project identity**, excluding non-JavaScript projects.
8. **Moon/Nx/tool-specific project IDs → sole durable project identity**, coupling repository identity to a replaceable implementation tool.

These are review heuristics now and should become machine-verifiable invariants where practical later.

---

# Authority handoff model

A common request should flow through as few authority transitions as practical.

## Bootstrap

```text
repository command
    ↓
mise — install/resolve supported tools
    ↓
Bun — install JS workspace dependencies
    ↓
repository verification / Moon becomes available for normal tasks
```

## Build/test/check

```text
repository command
    ↓
Moon — select operational project/target DAG, affected scope, cache semantics
    ↓
project task command
    ↓
Bun / compiler / test runner / ecosystem-native tool
```

## Generate project

```text
repository command
    ↓
Nx generator
    ↓
write repository state
    ├─ repository-owned durable project identity/metadata
    ├─ Bun-owned package/workspace metadata
    ├─ Moon-owned orchestration project/task metadata
    └─ repository-owned documentation/policy metadata
    ↓
normal repository verification through Moon
```

## CI validation

```text
GitHub Actions
    ↓
runner + permissions + event context
    ↓
repository command
    ↓
Moon
    ↓
project/ecosystem tools
```

---

# Evidence status

This document is an **authority decision**, not implementation proof.

The repository still needs to demonstrate:

- the smallest durable repository-owned project identity/metadata representation and its relationship to Moon project IDs;
- mise version/lock/install behavior from fresh environments;
- a Moon configuration that consumes mise-provided tools without silent version duplication;
- Bun workspace and frozen-lockfile correctness;
- disabling Moon automatic dependency installation without losing needed graph/hash behavior;
- Moon affected/cache correctness through deliberate fixtures;
- Nx generation/migration operation without routine Nx task authority;
- CI/local parity;
- Rust and Python integration;
- remote cache/storage boundaries;
- performance and CI-economics claims.
