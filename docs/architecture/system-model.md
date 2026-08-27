# Repository Platform System Model

This document defines the vocabulary we use throughout **Building the Ultimate Monorepo Starter from First Principles**.

The goal is not to force every tool to use our words. The goal is to keep the architecture understandable when different tools use the same word for different scopes—or different words for the same idea.

> Tool capability does not imply architectural authority.

A tool may be capable of owning several concerns while this repository deliberately assigns it only one.

## The layers

We model the monorepo as a repository-embedded developer platform with eight conceptual layers.

1. **Repository boundary** — versioned history, collaboration, review, and repository-level policy.
2. **Environment/toolchain layer** — runtimes, binaries, versions, environment assumptions, and bootstrap.
3. **Workspace layer** — the set of participating projects and ecosystem workspaces.
4. **Dependency model** — relationships among projects/packages and the architecture rules placed on them.
5. **Execution model** — tasks, targets, task dependencies, affected calculation, scheduling, and caching.
6. **Generation/evolution layer** — generators, codemods, migrations, and upgrade transformations.
7. **Quality/governance layer** — formatting, linting, typing, tests, dependency policy, dead-state detection, security checks, and repository invariants.
8. **Delivery layer** — CI orchestration, releases, automation, and distribution.

These layers describe responsibilities, not directory names and not necessarily one tool each.

## Canonical terms

### Repository

The Git version-control boundary that owns shared history, review, branches, tags, and repository-level policy.

A repository may contain one project or many projects. A repository is not automatically a package-manager workspace and is not automatically a Moon/Nx workspace.

### Workspace

A coordinated set of projects that participate in a shared operating model.

Because tools use this term differently, we qualify it when ambiguity matters:

- **repository workspace** — our architectural set of participating projects;
- **JavaScript workspace** — the package-manager workspace described by `package.json` workspaces;
- **Moon workspace** — the set of projects Moon discovers/configures;
- **Nx workspace** — the repository/project context Nx models for its own features.

These sets should usually align where appropriate, but they are not definitionally identical.

### Project

A repository-owned unit of code or infrastructure with an identity, purpose, and executable/validatable lifecycle.

Examples include:

- a web application;
- a service;
- a TypeScript library;
- a Rust binary;
- a Python service;
- an end-to-end test project;
- repository tooling;
- a documentation site.

A project may be publishable, private, deployable, executable, generated, or none of those.

### Package

An ecosystem-specific installable or publishable dependency unit, such as a package described by `package.json`, `Cargo.toml`, or Python packaging metadata.

A package can also be a project, but **project and package are not synonyms**.

Examples of projects that may not be packages include an E2E test suite, a deployment project, or repository tooling that is never distributed as an ecosystem package.

### Project graph

A directed graph whose nodes are repository projects and whose edges describe project-level relationships relevant to orchestration or architecture.

Some edges may be inferred from ecosystem dependencies. Others may be explicit because the relationship cannot be derived reliably from source/package metadata.

The project graph is persistent conceptual structure even when a particular task is not running.

### Package/dependency graph

A graph of dependency relationships expressed by one or more language/package ecosystems.

For JavaScript, this includes dependencies represented in package manifests. Rust and Python have their own native dependency metadata.

The package/dependency graph can inform the repository project graph, but it is not the whole repository architecture.

### Task

A named operation that can run in the context of a project, such as `build`, `test`, `lint`, `typecheck`, `dev`, or `generate`.

A task definition includes more than a command when correctness/performance matter. It may also describe inputs, outputs, dependencies, environment influence, cacheability, persistence, and execution constraints.

### Target

A specific task scoped to a specific project.

Conceptually:

```text
<project>:<task>
```

Moon uses this compound project/task idea directly as a target. We will use the term when a task needs project context and avoid treating a bare task name as globally unique.

### Task graph

The graph formed when requested targets are expanded through task dependencies and project relationships into the concrete work needed for an invocation.

It is not the same as the project graph:

```text
project graph: what depends on what

task graph: what work must run, and in what dependency order, for this request
```

A single project graph can produce many different task graphs.

### Affected set

The projects or targets whose correct result may have changed given a change in inputs, dependencies, configuration, environment, or another declared influence.

"Changed files" and "affected work" are not synonyms. A project can be affected because one of its dependencies changed even when none of its own files did.

### Cache

A mechanism for reusing a previously computed task result when the system can establish that the relevant inputs are equivalent.

A cache hit is a correctness claim, not merely a performance trick. The repository must eventually prove that cache keys account for all inputs capable of changing the result.

### Toolchain

An overloaded industry/tool term. In this series we distinguish two concerns:

- **environment toolchain** — which runtime/tool executable and version should exist for a repository operation;
- **orchestrator toolchain integration** — ecosystem-aware behavior an orchestrator uses to infer metadata, dependencies, commands, inputs, or runtime behavior.

Those concerns may be implemented by the same product, but they do not have to be.

This distinction matters because our initial architecture hypothesis gives environment/tool-version authority to mise while Moon also exposes a proto-backed toolchain capable of downloading/managing versions. That overlap is unresolved until we test the boundary.

### Generator

A deterministic transformation that creates or updates repository state from explicit inputs and repository conventions.

Generators are not fancy copy scripts. Their output is part of the repository contract and must be testable.

### Migration

A versioned transformation that moves repository state from an older contract to a newer contract while preserving user-owned work unless the migration explicitly documents otherwise.

### Policy

A repository rule that constrains allowed state or behavior.

Examples:

- dependency direction;
- package version alignment;
- required project metadata;
- generated-file freshness;
- security requirements;
- release rules.

A policy is only reliable when its enforcement path is explicit. Documentation without verification is guidance, not an enforced policy.

### Root command

A stable developer-facing repository operation such as bootstrap, check, test, build, or doctor.

Root commands are part of the repository's public interface for humans, CI, and agents. They should delegate to the real authority for the underlying concern rather than reimplementing it in shell glue.

### Control plane and work plane

A useful conceptual split:

- the **control plane** describes projects, policy, tasks, tool versions, dependency relationships, generation rules, and delivery behavior;
- the **work plane** is the actual application/library/service/tool code produced and maintained inside those constraints.

The starter primarily builds the control plane. Real product projects consume it.

## Relationship model

A compact conceptual model is:

```text
Git repository
│
├─ environment/toolchain contract
│
├─ repository workspace
│  ├─ project A
│  │  ├─ package metadata? (optional)
│  │  └─ tasks
│  ├─ project B
│  │  ├─ package metadata? (optional)
│  │  └─ tasks
│  └─ project C
│     └─ tasks
│
├─ project/dependency graph
│
├─ task graph(s)
│  └─ cached or executed results
│
├─ generation + migration machinery
├─ quality + governance policy
└─ CI + release/delivery orchestration
```

The important point is that the repository is the containing boundary, not the model itself. Putting directories under one Git root does not create these relationships automatically.

## Capability versus authority

Current candidate tools intentionally overlap.

| Tool | Capabilities relevant to this repository | Initial authority hypothesis |
| --- | --- | --- |
| Bun | JS runtime, package manager, workspaces, test runner, bundler | JavaScript package/workspace authority |
| mise | tool/runtime versions, environment loading, tasks | environment/tool-version authority |
| Moon | projects, task graph, affected execution, caching, toolchains | project/task orchestration authority |
| Nx | project/task graph, caching, plugins, generators, migrations | generation/migration authority only |
| Vitest | unit/integration test runner | JS/TS unit/integration testing authority |
| GitHub Actions | workflow scheduling and hosted execution | hosted CI orchestration |

The right-hand column is provisional until the authority-selection experiments later in Arc I.

A product is allowed to have capabilities we do not use. That restraint is how we keep the repository from acquiring several competing sources of truth.

## Boundary rules to test later

The following statements are hypotheses, not facts:

1. mise can remain the environment/tool-version authority without preventing Moon from using the ecosystem metadata it needs for correct project/task orchestration.
2. Moon can remain the task-orchestration authority while mise tasks are limited to bootstrap/environment operations that do not form a competing repository task graph.
3. Nx generators/migrations can operate on repository state without requiring Nx to become the routine task runner.
4. Bun can own JavaScript workspace/package management without forcing its test runner or bundler to become repository-wide authorities.
5. non-JavaScript projects can participate as first-class projects even though they do not participate in the Bun workspace.

If implementation disproves any statement, we change the architecture and preserve the evidence.

## Why this document exists

Tool documentation naturally explains a repository from that tool's point of view. A package manager sees packages. A task runner sees projects and tasks. A version manager sees tools and environments. A CI service sees jobs and workflows.

Our architecture must see the whole system.

This model gives us a vocabulary that can survive tool changes. If we replace Bun, Moon, Nx, mise, or GitHub Actions later, the architectural concern still has a name and a contract. That is a prerequisite for reversible architecture.
