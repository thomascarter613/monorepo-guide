# 001 — Define the Repository Platform Mental Model

Status: **Open engineering journal**

## Starting state

Canonical parent checkpoint while restacking:

`836a511951ca4ceebc0fa2fdd0ace803be4fbb7c`

Branch:

`series/01-platform-mental-model`

## Historical status

The initial Article 1 research and first `system-model.md` commit began before the engineering-record protocol was integrated into the series branches. This journal therefore separates reconstructed early reasoning from events recorded after protocol adoption.

## Objective

See `../requirements/001-platform-mental-model.md`.

## Initial assumptions

- Tool documentation will use overlapping vocabulary, but a repository-level model can separate the underlying concerns.
- `project` must be broader than JavaScript `package` for polyglot readiness.
- `project graph` and `task graph` need distinct meanings before we implement affected execution or caching.
- Our intended Bun/mise/Moon/Nx responsibility split is plausible but has not been proven.

## Research — current candidate capabilities

Research date: **2026-08-27**

First-party references:

- Bun: https://bun.sh/
- Moon concepts: https://moonrepo.dev/docs/concepts
- Moon projects: https://moonrepo.dev/docs/concepts/project
- Moon tasks: https://moonrepo.dev/docs/concepts/task
- Moon toolchain: https://moonrepo.dev/docs/concepts/toolchain
- Nx core concepts: https://nx.dev/docs/concepts
- Nx plugins: https://nx.dev/docs/concepts/nx-plugins
- Nx sync generators: https://nx.dev/docs/concepts/sync-generators
- mise dev tools: https://mise.jdx.dev/dev-tools/
- mise tasks: https://mise.jdx.dev/tasks/

### Bun observation

Current Bun documentation presents one binary with runtime, package-manager/workspace, test-runner, and bundler capabilities.

Our architecture currently intends to assign only a subset of those repository-wide authorities to Bun.

### mise observation

Current mise documentation includes development tool/runtime management and environment loading, but also a dependency-aware task system with parallel execution and freshness behavior.

That task capability can overlap with the repository task authority we intend to assign to Moon.

### Moon observation

Current Moon v2 documentation includes projects, tasks, dependency/action graphs, affected behavior, caching, and a proto-backed toolchain that can install/manage languages and package managers.

The version-management portion overlaps directly with the role tentatively assigned to mise, while the ecosystem-aware metadata portion may still be useful to Moon even if mise remains the executable-version authority.

### Nx observation

Current Nx documentation includes project graphs, task graphs, affected execution, caching, plugins, generators, migrations, executors, and sync generators.

This is much broader than the generation/migration role we intend to assign Nx and overlaps substantially with Moon.

## Engineering finding — capability is not authority

### Expected

We expected feature overlap, but the early responsibility diagram made the candidate tools look more naturally separated than their current product surfaces actually are.

### Tried

We compared the repository concerns from Article 0 against current first-party documentation for Bun, mise, Moon, and Nx.

### Observed

The products cover overlapping slices of the same conceptual platform:

```text
Bun:
  runtime + package manager + workspaces + tests + bundling

mise:
  tool versions + environment + tasks

Moon:
  projects + tasks + affected + caching + toolchain

Nx:
  projects + tasks + affected + caching + plugins + generators + migrations
```

### Learned

A product capability map is not an architecture authority map.

If we let every enabled feature become authoritative by default, we will create multiple competing task graphs, toolchain sources, or project models.

The word `toolchain` also needs qualification because mise and Moon can both legitimately use it for overlapping concerns.

### Changed

Created `../architecture/system-model.md` with:

- eight conceptual platform layers;
- canonical repository/workspace/project/package vocabulary;
- project/package/task graph distinctions;
- environment-toolchain vs orchestrator-toolchain-integration distinctions;
- root command, generator, migration, policy, cache, and affected-set definitions;
- explicit provisional authority hypotheses.

The rule **tool capability does not imply architectural authority** is now part of the current-state model.

### Verified

See `../verification/001-platform-mental-model.md`.

## Engineering event — branch boundary mistake

The first Article 1 `system-model.md` commit was accidentally created on `series/00-first-principles`.

Correction:

1. create `series/01-platform-mental-model` from that commit so no work was lost;
2. move `series/00-first-principles` back to its prior verified head;
3. verify Article 0 again contained only its intended paths at that point.

Lesson:

A stacked-branch name is not proof of checkpoint isolation. Each article must verify its delta against its parent checkpoint.

## Engineering event — `main` changed during Article 1

During verification, `main` had advanced through merged PR #2 (`docs: establish engineering record system`).

Rather than overwrite or ignore it, we integrated the new documentation protocol into the series stack. Article 0 was rebuilt on the new `main` tree and Article 1 is being rebuilt on the new Article 0 checkpoint.

This is a beneficial change in baseline rather than an architecture reversal: it supplies the requirements/journal/verification machinery the series wanted to create anyway.

## Decisions

Accepted for the current model:

- repository is the containing Git boundary, not the project model itself;
- repository workspace is broader than any single ecosystem workspace;
- project and package are distinct concepts;
- project graph and task graph are distinct;
- changed and affected are distinct;
- cache hits are correctness claims;
- generators and migrations solve different lifecycle problems;
- policy requires an explicit enforcement path;
- root commands should delegate to authorities;
- product capabilities do not automatically become repository authorities.

Still unresolved:

- exact mise/Moon toolchain boundary;
- whether mise tasks should exist beyond bootstrap/environment operations;
- how Nx remains generation-focused without becoming a second execution authority;
- whether Bun owns runtime authority in addition to JavaScript package/workspace authority.

## Ending state

Draft branch: `series/01-platform-mental-model`

Final checkpoint tag and PR number: pending final verification/opening of the stacked PR.
