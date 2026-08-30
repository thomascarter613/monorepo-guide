# 003 — Authority Capability Evidence

Research date: **2026-08-27**

Purpose: preserve the first-party product-capability evidence used by Article 3 to separate **what a tool can do** from **what this repository authorizes it to own**.

This is a dated research artifact, not current-state architecture. Product documentation changes; later authority reviews should refresh the evidence instead of silently assuming these observations remain true forever.

## Bun

First-party sources:

- https://bun.sh/

Current documented capability observations:

- Bun presents itself as an all-in-one JavaScript runtime/toolkit.
- The product combines runtime, package manager/workspaces, test runner, and bundler capabilities.
- Bun explicitly supports adopting one or all of those roles.
- The package-manager surface includes workspaces and a lockfile.

Authority implication:

Selecting Bun as JavaScript package/workspace authority does **not** imply that Bun must also own repository testing or bundling. Those are later concern-specific decisions.

## mise

First-party sources:

- https://mise.jdx.dev/configuration.html
- https://mise.jdx.dev/dev-tools/mise-lock.html
- https://mise.jdx.dev/tasks/
- https://mise.jdx.dev/tasks/task-configuration.html
- https://mise.jdx.dev/tasks/architecture.html

Current documented capability observations:

- `mise.toml` can declare project tool versions and environment state.
- `mise.lock` can preserve exact tool versions and, depending on backend support, URLs/checksums/provenance information.
- mise includes a full project-task subsystem, not merely command aliases.
- mise task dependencies form a DAG and can run independent work in parallel.
- mise task examples explicitly include build, test, lint, deploy, and development workflows.

Authority risk:

If routine repository task dependencies are encoded in mise as well as Moon, `UMS-EXE-001` would have two task authorities.

Repository boundary selected in Article 3:

- mise: executable/tool version declaration, resolution, and installation;
- mise tasks: only environment/bootstrap helpers if needed, not routine project build/test/lint/typecheck orchestration.

## Moon

First-party sources:

- https://moonrepo.dev/docs/concepts/toolchain
- https://moonrepo.dev/docs/config/toolchain
- https://moonrepo.dev/docs/setup-toolchain
- https://moonrepo.dev/docs/guides/javascript/bun-handbook
- https://moonrepo.dev/docs/config/workspace
- https://moonrepo.dev/docs/how-it-works/task-graph
- https://moonrepo.dev/docs/concepts/affected
- https://moonrepo.dev/docs/run-task
- https://moonrepo.dev/docs/commands/task-graph
- https://moonrepo.dev/docs/config/tasks

Current documented capability observations:

### Task/execution capabilities

- Moon represents configured task relationships as a directed acyclic graph.
- Task dependencies affect topological/parallel execution.
- Moon supports affected analysis over changed files, environment inputs, and graph relationships.
- Moon can inspect/emit task graph information, including machine-readable/graph formats.
- Task execution integrates hashing/cache behavior.

### Toolchain overlap

- Moon's toolchain is proto-backed and can download/install/manage language/package-manager tools when versions are configured.
- Moon can also run with tool binaries supplied by the surrounding environment/PATH.
- `MOON_TOOLCHAIN_FORCE_GLOBALS` can force use of PATH/global binaries.

### JavaScript/Bun overlap

- Moon can enable JavaScript and Bun ecosystem integrations while Bun is supplied from the environment.
- The Bun integration can infer project relationships from package dependency metadata.
- Moon can automatically install JavaScript dependencies when manifests/lockfiles change.
- `installDependencies: false` can disable toolchain dependency installation.
- workspace pipeline dependency-install behavior can also be disabled.

Authority risk:

Without an explicit boundary, Moon can compete with mise for runtime installation/version authority and with Bun for dependency-install authority.

Repository boundary selected in Article 3:

- Moon owns repository project/task relationships, affected analysis, task execution ordering, and task-result cache semantics.
- Moon consumes mise-provided executables rather than independently pinning/installing the same runtime tools.
- Moon may consume Bun/package metadata for graph/hash semantics.
- Moon must not become an independent package-install authority when Bun owns JavaScript package installation.

### Polyglot risk note

The current toolchain reference includes Rust support and still exposes Python-related integrations under `unstable_*` identifiers. That means the later Python proof remains a genuine falsification point for the architecture.

## Nx

First-party sources:

- https://nx.dev/docs/concepts
- https://nx.dev/docs/features/run-tasks
- https://nx.dev/docs/concepts/how-caching-works
- https://nx.dev/docs/features/ci-features/affected
- https://nx.dev/docs/concepts/nx-plugins
- https://nx.dev/docs/reference/nx/generators
- https://nx.dev/docs/features

Current documented capability observations:

- Nx models project/task graphs.
- Nx can run task pipelines and affected-only work.
- Nx provides computation caching based on declared/inferred inputs and outputs.
- Nx plugins may infer projects/tasks and contribute graph data.
- Nx plugins may provide generators, migrations, executors, and configuration synchronization.
- Nx provides generator and migration facilities directly relevant to the starter's evolution requirements.

Authority risk:

Installing Nx for generators does not make it generator-only. If normal repository commands start using `nx run`, `nx affected`, Nx caching, or inferred Nx tasks as required execution state, the repository has created a second task authority alongside Moon.

Repository boundary selected in Article 3:

- Nx is authorized for repository transformations: generation, migration, and codemod-style changes.
- Nx may inspect project state as required by those transformations.
- Nx task execution/affected/caching is not part of the canonical routine repository workflow.
- generated projects must register with the Bun/Moon contracts instead of requiring Nx execution for routine operation.

## GitHub Actions

First-party sources:

- https://docs.github.com/en/actions
- https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax
- https://docs.github.com/en/actions/reference/workflows-and-actions/reusing-workflow-configurations

Current documented capability observations:

- GitHub Actions owns hosted workflow triggers, jobs, runners, matrices, environments, secrets, token permissions, and reusable workflow composition.
- job-level and workflow-level `permissions` can reduce `GITHUB_TOKEN` access.
- workflow jobs may have dependency relationships.
- reusable workflows can centralize hosted workflow logic.

Authority implication:

A GitHub Actions job graph is legitimate when it models hosted concerns, such as validation gating a privileged release job. It becomes an architectural problem when it independently re-encodes repository task dependencies that Moon should already own.

Repository boundary selected in Article 3:

- GitHub Actions owns hosted event/runner/security orchestration.
- repository build/test/lint/typecheck semantics remain repository/Moon state and should be invoked from workflows rather than recreated in YAML.

## Cross-tool collision matrix

| Concern | Bun | mise | Moon | Nx | GitHub Actions |
| --- | --- | --- | --- | --- | --- |
| runtime/tool install/version | Bun can self-install/upgrade itself; runtime role | **strong capability** | **strong proto-backed capability** | plugin/runtime dependencies but not chosen here | runner image/setup actions can install tools |
| JS package install/workspace | **strong capability** | can run package-manager tasks | **can auto-install and infer JS deps** | can operate on Bun workspaces and package metadata | can execute install steps |
| repository task DAG | scripts/parallel execution capabilities | **strong task DAG** | **strong task DAG** | **strong task DAG** | hosted job DAG |
| affected analysis | not selected | last-modified task features | **strong graph-aware capability** | **strong graph-aware capability** | changed-file logic can be hand-written but should not own it |
| task-result caching | not chosen | task freshness features | **strong capability** | **strong capability** | cache action can store arbitrary paths |
| generation/migrations | starter/project commands exist but not chosen | templates/tasks can transform files | scaffolding support exists but not chosen | **strong generators/migrations capability** | workflow automation can edit via scripts but should not own it |
| hosted CI events/runners/permissions | no | no | no | CI products/plugins exist | **platform authority** |

The collision matrix is the reason Article 3 assigns authority **per concern** rather than choosing one “monorepo tool.”

## Evidence limitations

This artifact records documented interfaces, not implementation proof.

It does not prove:

- mise fresh-clone behavior in this repository;
- Bun lockfile/workspace correctness in our future package graph;
- Moon cache/affected correctness under our actual inputs;
- that Moon's integration can be constrained exactly as intended without lost functionality;
- that Nx generation/migrations can remain isolated from routine task execution;
- CI/local parity;
- Rust/Python polyglot success;
- performance or CI-cost superiority.

Those claims require later executable experiments.
