# 003 — Choosing Authorities

Status: **In progress**

## Starting state

Starting branch: `main`

Starting commit:

`44951cd93646c2fb2dda63f384238401ef2bbc8c`

Working branch:

`series/03-authority-boundaries`

## Objective

See `../requirements/003-authority-boundaries.md`.

## What we know at the start

Articles 0–2 are accepted on `main`.

The repository has:

- a definition of what the finished starter must prove;
- a tool-independent system model;
- a living product requirements ledger;
- a historical engineering-record protocol;
- no installed monorepo implementation toolchain yet.

Article 2 deliberately made Bun, mise, Moon, Nx, and GitHub Actions implementation candidates rather than requirements. Article 3 is where we assign provisional authority to those candidates without claiming later implementation evidence.

## Initial authority hypothesis

Before current documentation research, the intended split was roughly:

- mise — runtime/tool versions and environment entry;
- Bun — JavaScript runtime/package/workspace operations;
- Moon — projects/tasks/affected/caching;
- Nx — generators/migrations/codemods only;
- GitHub Actions — hosted CI orchestration.

The purpose of this increment is to attack the hidden overlaps in that diagram.

## Questions

- Can Moon consume Bun and other runtimes from `PATH` while still providing the ecosystem metadata/graph integration we need?
- Does Moon automatically install package dependencies even if mise installs the executable version?
- Can that installation behavior be disabled without losing project graph/task graph value?
- How much task-runner capability does mise expose, and how do we prevent it from becoming a parallel task graph?
- How much task/caching/project inference does Nx expose when we only want generators/migrations?
- Can Nx generation remain useful without making normal build/test/lint/affected workflows depend on Nx execution?
- What belongs in a GitHub Actions job graph versus the repository task graph?
- Which choices can be made now from interfaces and responsibility fit, and which must remain hypotheses until later implementation experiments?

## Research date

2026-08-27.

All product-capability claims in this increment should be understood as observations from the current first-party documentation on this date, not timeless guarantees.

## Finding 1 — Bun is intentionally multi-role

### Expected

We expected Bun to overlap primarily with runtime and package management.

### Observed

Bun's current official homepage presents a single toolkit containing:

- JavaScript/TypeScript runtime;
- package manager and workspaces;
- test runner;
- bundler.

The product explicitly supports adopting one or all of these capabilities.

Source:

- https://bun.sh/

### Learned

Selecting Bun as package/workspace authority does not require selecting Bun as test or bundling authority.

Capability is broader than authority.

### Changed

The authority map will assign Bun the JavaScript package/workspace role and the default Bun-runtime role while leaving testing/build-tool decisions to later concern-specific articles.

## Finding 2 — mise is also a real task orchestrator

### Expected

We expected mise to be mostly a tool/runtime version manager with convenient helper tasks.

### Observed

Current mise documentation describes a dependency-aware task system with:

- `depends`, `wait_for`, and `depends_post` graph relationships;
- DAG resolution;
- parallel dependency execution;
- task visualization;
- build/test/lint/deploy task examples;
- last-modified checking and watch support.

Sources:

- https://mise.jdx.dev/tasks/
- https://mise.jdx.dev/tasks/task-configuration.html
- https://mise.jdx.dev/tasks/architecture.html

### Learned

If we put ordinary `build`, `test`, `lint`, and `typecheck` dependency relationships into mise while also selecting Moon, we would violate `UMS-EXE-001` even if both tools technically work.

### Changed

mise tasks, if used at all, will be constrained to environment/bootstrap operations that exist before or outside the repository project/task graph. Routine project validation and build ordering belongs to Moon.

## Finding 3 — mise now has a project tool lockfile

### Expected

We expected mise to declare versions, but the precise reproducibility mechanism needed current verification.

### Observed

Current mise documentation describes `mise.lock`, which can pin exact tool versions and checksums/URLs where backend support permits it. `mise.toml` remains the human configuration while the lockfile can preserve resolved tool versions.

Source:

- https://mise.jdx.dev/dev-tools/mise-lock.html

### Learned

mise has a stronger claim to environment/tool-version authority than a loose version-file-only model would provide. Backend support is not uniform, so later reproducibility claims still need per-tool verification.

### Changed

The provisional environment authority is mise, subject to fresh-environment proof in Arc III.

## Finding 4 — Moon's toolchain is an authority-capable subsystem, not just metadata

### Expected

We initially used language like “mise owns tool versions; Moon owns tasks,” assuming Moon could simply consume already-installed tools while retaining ecosystem integration.

### Observed

Moon v2 documentation says its toolchain is built around proto and can download/install/manage language and package-manager tools. The Bun integration can also automatically install dependencies when manifests or the lockfile change.

Moon documentation also provides a crucial escape hatch:

- language/toolchain integrations can be enabled without a version so binaries come from the environment/PATH;
- `installDependencies` can be set to `false`;
- the workspace pipeline's dependency-install behavior can also be disabled;
- `MOON_TOOLCHAIN_FORCE_GLOBALS` can force PATH/global binaries when needed.

Sources:

- https://moonrepo.dev/docs/concepts/toolchain
- https://moonrepo.dev/docs/config/toolchain
- https://moonrepo.dev/docs/setup-toolchain
- https://moonrepo.dev/docs/guides/javascript/bun-handbook
- https://moonrepo.dev/docs/config/workspace

### Learned

The original responsibility statement was too vague. If we configure Moon versions through proto or allow Moon to auto-install JavaScript dependencies, Moon would become a competing environment/package authority even if mise/Bun are also configured.

However, the documentation shows a plausible integration mode where Moon consumes PATH-provided binaries and uses its ecosystem knowledge for dependency/project inference, hashing, and task execution.

### Changed

The intended boundary is now explicit:

1. mise declares/resolves/installs the supported executable versions;
2. Bun owns JavaScript package resolution/install/lockfile semantics;
3. Moon may enable its JavaScript/Bun integrations for graph/task semantics;
4. Moon must not be configured as the independent Bun/runtime version source;
5. Moon's automatic JavaScript dependency installation must be disabled if explicit Bun installation remains the package authority.

This is still a hypothesis until configured and tested in later articles.

## Finding 5 — Moon clearly satisfies the shape of a repository task authority

### Observed

Moon's current documentation exposes:

- a directed acyclic task graph derived from project relationships;
- explicit task dependencies;
- topological/parallel execution;
- affected analysis based on files, environment variables, and graph relations;
- cache-aware task execution;
- inspectable task graphs and JSON/DOT output.

Sources:

- https://moonrepo.dev/docs/how-it-works/task-graph
- https://moonrepo.dev/docs/concepts/affected
- https://moonrepo.dev/docs/run-task
- https://moonrepo.dev/docs/commands/task-graph

### Learned

Moon matches the concern described by `UMS-EXE-001` through `UMS-EXE-004`, but documentation capability alone is not correctness proof. We still need real dependency, affected, environment-input, cache invalidation, and output-restoration fixtures later.

### Changed

Moon is selected as the provisional repository project/task/affected/cache authority.

## Finding 6 — Nx is much more than generators

### Expected

We knew Nx had an orchestrator, but the intended architecture described it mainly as generator/migration infrastructure.

### Observed

Current Nx documentation prominently includes:

- project and task graphs;
- task pipelines;
- affected execution;
- local/remote caching;
- inferred tasks via plugins;
- generators and migrations;
- release tooling and module-boundary capabilities.

Plugins can contribute project graph data, inferred tasks, generators, migrations, and executors.

Sources:

- https://nx.dev/docs/concepts
- https://nx.dev/docs/features/run-tasks
- https://nx.dev/docs/concepts/how-caching-works
- https://nx.dev/docs/concepts/nx-plugins
- https://nx.dev/docs/reference/nx/generators

### Learned

Calling Nx “generator-only” is not a product description. It is a repository policy restriction we must actively enforce through how we configure and invoke Nx.

### Changed

Nx is provisionally authorized only for repository transformations: generators, migrations, and codemods. Normal root build/test/lint/typecheck/affected/cache commands must not invoke Nx as their task runner.

An Nx generator is allowed to inspect repository/project state as part of generation, but generated execution metadata must target the authoritative Bun/Moon model rather than create a required parallel Nx execution graph.

## Finding 7 — GitHub Actions has a job graph, and that is not automatically a violation

### Observed

GitHub Actions workflows model jobs, dependencies, runners, permissions, matrices, secrets, reusable workflows, and platform event triggers. `permissions` can be scoped at workflow or job level. Reusable workflows can reduce YAML duplication.

Sources:

- https://docs.github.com/en/actions
- https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax
- https://docs.github.com/en/actions/reference/workflows-and-actions/reusing-workflow-configurations

### Learned

“One task graph” cannot mean “no other graph exists anywhere.” GitHub Actions necessarily has a hosted orchestration graph.

The real boundary is semantic:

- Moon owns repository work dependencies;
- GitHub Actions owns hosted execution concerns.

A workflow may say “validation must finish before a privileged release job,” because that is a security/delivery boundary. It should not separately encode that package B must build before package A if Moon already owns that relationship.

### Changed

The authority map will distinguish repository task dependencies from CI job dependencies and define allowable hosted-only concerns.

## Finding 8 — polyglot support remains genuinely unproved

Moon's current v2 documentation provides stable-looking Rust support but still labels Python-related toolchains as `unstable_*` in the toolchain reference.

Source:

- https://moonrepo.dev/docs/config/toolchain

### Learned

Selecting Moon today does not let us claim the Python portion of our polyglot promise is solved. The task runner can always invoke system commands, but the quality of first-class ecosystem integration is still a later risk.

### Changed

Python integration remains an explicit exit criterion. If the later Python increment requires root-level redesign, duplicated graph state, or unacceptable workarounds, the Moon authority decision must be reconsidered.

## Provisional authority map emerging from research

| Concern | Provisional authority | Important non-authoritative participants |
| --- | --- | --- |
| Tool/runtime version resolution + install | mise | Moon consumes PATH binaries; Dev Containers/Nix boundary later |
| JavaScript package/workspace install + lock state | Bun | Moon may read manifests/lockfile for graph/hash semantics |
| Default JS runtime for Bun-oriented projects | Bun | mise selects installed Bun version |
| Repository project graph | Moon | Bun metadata may provide dependency evidence; Nx may inspect during generation |
| Routine task graph/execution | Moon | Bun scripts are leaf commands; mise tasks only bootstrap/environment |
| Affected analysis | Moon | Git provides change history/input |
| Task-result cache | Moon | CI cache may later cache bootstrap/download state, not duplicate task-result authority |
| Project generation | Nx | generated output must conform to Bun/Moon repository contracts |
| Repository migrations/codemods | Nx | migrations may update Bun/Moon configuration as data |
| Hosted CI event/runner/permission orchestration | GitHub Actions | repository commands/Moon perform equivalent repo work |
| Root developer command surface | Repository-owned interface | delegates to mise/Bun/Moon/Nx as appropriate |

## Deliberately unresolved

- exact Moon v2 configuration needed to retain JavaScript/Bun ecosystem integration while preventing version/dependency-install authority overlap;
- exact Nx configuration footprint required for generation/migrations without inferred routine tasks becoming part of normal operation;
- whether Bun remains the runtime for every TypeScript project or only the default/runtime for projects that require it;
- Nix and Dev Container authority boundaries;
- remote task cache provider/transport;
- detailed task-runner comparison against Turborepo/Bazel;
- quantitative performance conclusions;
- Python integration quality.

## Next work

- preserve the source/capability evidence in `docs/artifacts/003-authority-capability-evidence.md`;
- write the current-state `docs/architecture/authority-map.md`;
- update living requirement evidence states conservatively;
- write Article 3;
- verify every increment requirement;
- update series indexes/metadata;
- open a draft PR.
