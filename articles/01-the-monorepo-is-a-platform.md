# The Monorepo Is a Platform, Not a Folder Full of Packages

*Building the Ultimate Monorepo Starter from First Principles — Article 1*

*A tutorial, engineering log, architecture journal, and reproducible build from an empty directory to a production-grade polyglot workspace.*

---

In Article 0, we made the word **ultimate** expensive.

We defined the finished starter in terms of properties we intend to prove: reproducibility, explicit authority, useful determinism, fast feedback, local/CI parity, enforceable governance, evolvability, observability, security, polyglot readiness, AI legibility, and understandability.

Now we need a model of the thing that is supposed to provide those properties.

That sounds abstract. It is also one of the highest-leverage steps in the entire series.

If we skip it, every tool we install will try to teach us its own mental model. A package manager will make packages feel like the center of the universe. A task runner will make tasks and projects feel primary. A version manager will make the environment look like the control plane. A CI service will tempt us to encode architecture in workflow YAML. A generator framework will make its project model look canonical.

All of those perspectives can be useful.

None of them is the architecture by itself.

So before we install Bun, mise, Moon, Nx, Biome, Vitest, or anything else, we are going to define the repository in terms that survive those tools.

Our central claim is:

> **A serious monorepo is a repository-embedded developer platform.**

The folders are only the storage layout.

The platform is the operating model that tells those folders how to participate.

## Where we are starting

Article 0 left us with three meaningful artifacts:

```text
README
articles/00-what-does-ultimate-mean.md
docs/architecture/definition-of-ultimate.md
docs/series-roadmap.md
```

There is still no package manager configuration, no task runner, no TypeScript configuration, no CI, and no runtime pinning.

That is exactly where we want to be.

The first implementation increment for this article is not a tool installation. It is a **system model**:

```text
docs/architecture/system-model.md
```

That document gives us canonical vocabulary for the rest of the series and, more importantly, records where our candidate tools already overlap.

We are going to use that overlap as evidence rather than hide it.

## The weak definition of a monorepo

The common definition is something like:

> A monorepo is one repository containing multiple projects or packages.

That definition is useful for distinguishing a monorepo from many independent repositories.

It is not enough to design one.

Suppose we create this directory tree:

```text
repo/
├── apps/
│   ├── web/
│   └── api/
└── packages/
    ├── ui/
    └── config/
```

Have we built a monorepo platform?

Not really.

We have colocated directories.

We still do not know:

- how those directories are discovered;
- which of them are projects;
- which are packages;
- which dependencies are allowed;
- which tools are required;
- how tool versions are selected;
- how one project references another;
- what `build` means in each project;
- whether `test` depends on `build`;
- what changed when a shared package changes;
- which downstream work becomes affected;
- whether task results are cacheable;
- what invalidates those results;
- how a new project is created;
- how a repository-wide policy change reaches existing projects;
- how CI executes the same model;
- how releases are produced;
- how a Rust or Python project joins the repository;
- how a new engineer discovers all of this;
- how an AI agent discovers it without guessing.

A folder tree answers almost none of those questions.

That is why "put multiple packages in one repository" is a storage decision, not an operating model.

## The useful definition

For this series, we will model the monorepo as a **small internal developer platform embedded inside a Git repository**.

That platform has several layers:

1. repository boundary;
2. environment/toolchain layer;
3. workspace layer;
4. dependency model;
5. execution model;
6. generation/evolution layer;
7. quality/governance layer;
8. delivery layer.

This is not a claim that we need eight products.

It is a claim that we have at least eight kinds of responsibility.

That distinction matters because one product may implement several capabilities while we deliberately assign it authority over only one concern.

We will return to that idea repeatedly:

> **Capability is not authority.**

## Layer 1: the repository boundary

The repository is the outer version-control boundary.

It gives us shared:

- history;
- branches;
- commits;
- tags;
- review;
- collaboration;
- repository-level metadata;
- policy entry points.

For this series, GitHub hosts that boundary.

But the Git repository is not itself the project graph, the package-manager workspace, the task graph, or the development environment.

This distinction sounds obvious until tooling starts using the word *workspace* to mean several different things.

A repository can contain:

- files that are not projects;
- projects that are not packages;
- packages that belong to different language ecosystems;
- generated fixtures;
- documentation;
- repository tooling;
- release metadata;
- CI configuration.

The Git root contains the platform.

It is not a substitute for the platform model.

## Layer 2: the environment/toolchain layer

Before a project can build, the right executables must exist.

That includes concerns such as:

- runtime versions;
- compiler versions;
- package-manager versions;
- repository tools;
- environment variables;
- shell-visible paths;
- operating-system prerequisites;
- bootstrapping behavior.

In our initial architecture hypothesis, **mise** owns repository tool/runtime version selection.

That sounds straightforward until we inspect Moon.

Current Moon documentation describes a proto-backed toolchain that can download, install, and manage languages and package managers itself. Moon can use those toolchains to provide ecosystem-aware behavior and reproducible versions.

So "toolchain" is already overloaded before we install anything.

We therefore split the concept in two:

### Environment toolchain

Which executable and version should exist for repository work?

Examples:

```text
bun = X
node = Y
python = Z
rust = A
moon = B
```

### Orchestrator toolchain integration

What ecosystem knowledge does the orchestrator need to correctly model projects and tasks?

Examples:

- parse package metadata;
- infer dependencies;
- understand lockfiles;
- discover ecosystem aliases;
- calculate task inputs;
- select the correct executable environment.

Those concerns *can* be implemented by one product.

They do not have to be.

This gives us an explicit experiment for a later article:

> Can mise remain the environment/tool-version authority while Moon still uses enough ecosystem integration to correctly model and execute the repository?

If the answer is no, we will change the design.

## Layer 3: the workspace layer

The word **workspace** is dangerous because almost every repository tool uses it.

A JavaScript package manager has a workspace.

Moon has a workspace.

Nx has a workspace.

An IDE has a workspace.

A developer may casually use "workspace" to mean the repository checkout.

If we let those meanings blur together, we create accidental architecture.

So our canonical definition is:

> **A repository workspace is the coordinated set of projects participating in the repository operating model.**

When a tool-specific meaning matters, we qualify it:

- repository workspace;
- JavaScript workspace;
- Moon workspace;
- Nx workspace.

Those sets may overlap heavily.

They are not automatically identical.

That distinction is what makes genuine polyglot support possible.

A Rust project should be a first-class member of the **repository workspace** even though it is not a package in the Bun JavaScript workspace.

If we define "project" as "something found in `package.json` workspaces," we have already failed the polyglot requirement before writing any Rust.

## Project is not package

This is one of the most important distinctions in the series.

A **project** is a repository-owned unit with an identity, purpose, and lifecycle.

A **package** is an ecosystem-specific installable or publishable dependency unit.

Sometimes the same directory is both.

For example:

```text
packages/ui/
```

might be:

- a repository project;
- a JavaScript workspace member;
- an npm-compatible package;
- a buildable unit;
- a testable unit;
- perhaps a publishable unit.

But consider:

```text
apps/web-e2e/
```

That might be a repository project with tasks like:

```text
install-browser
start-dependencies
test-e2e
```

while never being a package we publish or consume as a dependency.

Or consider:

```text
tools/repository-doctor/
```

It may be a project because it has code, tests, and tasks, even if its packaging status is an implementation detail.

Later:

```text
services/search-rust/
```

can be a repository project and a Cargo package while having nothing to do with the JavaScript package workspace.

Therefore:

> **Every package may participate as a project, but not every project must be a package.**

That one sentence prevents a surprising amount of future coupling.

## The project graph

Once projects have identities, we need relationships.

A **project graph** is a directed graph describing project-level dependency relationships.

Imagine three projects:

```text
web -> ui -> tokens
```

The arrows mean the project on the left depends on the project on the right.

That graph answers questions such as:

- what can be built independently?
- what may be affected when `tokens` changes?
- which dependency directions exist?
- where are architecture boundaries being crossed?

Some edges may come from package metadata.

For example, if `web/package.json` depends on `@repo/ui`, the orchestrator may infer a relationship from the JavaScript workspace.

Other relationships may need to be explicit.

A browser test project may depend operationally on a web application even though there is no package dependency between them.

A code-generation project may produce artifacts consumed by a Rust service.

An integration-test project may require multiple services.

So the project graph is broader than the package dependency graph.

## Package graph versus project graph

For a TypeScript-only repository, it is easy to accidentally treat these as the same thing.

Suppose:

```text
apps/web/package.json
packages/ui/package.json
packages/tokens/package.json
```

and the package dependencies exactly match the project dependencies.

At that moment, the two graphs look identical.

That is convenient.

It is not a durable definition.

Once we add:

- an E2E project;
- generated API clients;
- a Rust binary;
- Python tooling;
- deployment projects;
- docs built from multiple package sources;

repository relationships stop being expressible purely as JavaScript package dependencies.

So we allow native package graphs to **inform** the project graph without declaring that they are the project graph.

## Tasks are operations, not scripts

A task is a named operation in the context of a project.

Examples:

```text
build
test
lint
typecheck
dev
generate
```

At first glance, that sounds like a script name.

For serious orchestration, a task is more than a command string.

A task may need to describe:

- its command;
- input files;
- output files;
- environment variables that affect results;
- dependencies on other tasks;
- whether it is cacheable;
- whether it is persistent;
- whether it can run in CI;
- execution constraints;
- platform/toolchain requirements.

Consider:

```text
build = "run compiler"
```

versus:

```text
build:
  command: run compiler
  inputs: source + config + dependency outputs + relevant env
  outputs: dist/**
  depends_on: dependencies' build tasks
  cacheable: true
```

The second is an execution contract.

That is the level of model we eventually need.

## A target is a task with project context

The name `build` is not globally meaningful.

`web:build` and `api:build` may use different commands, inputs, outputs, and toolchains.

So we use **target** for a task scoped to a project:

```text
<project>:<task>
```

Moon uses this project/task compound idea directly.

Conceptually:

```text
web:build
ui:test
api:typecheck
```

This gives us a stable way to talk about one unit of requested work even if the underlying tool's exact target syntax changes later.

## The task graph is not the project graph

This distinction is subtle enough that it deserves an example.

Suppose our project graph is:

```text
web -> ui -> tokens
```

Each project has:

```text
build
test
```

Now request:

```text
web:build
```

If build tasks depend on dependency builds, the task graph may be:

```text
tokens:build
    ↓
ui:build
    ↓
web:build
```

Now request:

```text
web:test
```

That could produce a different graph:

```text
tokens:build
    ↓
ui:build
    ↓
web:test
```

or perhaps:

```text
web:test
```

if the test system consumes source directly and no build prerequisites are required.

The point is:

```text
project graph = durable relationships among projects

task graph = concrete work dependency graph for an invocation
```

One project graph can produce many task graphs.

If we confuse the two, caching and affected execution become much harder to reason about.

## "Changed" is not "affected"

Suppose only this file changes:

```text
packages/tokens/src/colors.ts
```

The changed project may be `tokens`.

But if:

```text
web -> ui -> tokens
```

then all three projects may be **affected** depending on the requested task.

That means:

```text
changed files != affected projects != affected targets
```

Affected calculation is a correctness problem before it is a performance optimization.

Running too much work wastes time.

Running too little work can ship a bug.

Later, when we implement affected execution, we will deliberately create dependency scenarios designed to falsify our assumptions.

## The cache is a correctness system

Caches are often introduced as speed features.

That framing is incomplete.

When an orchestrator returns a cached build instead of executing the build, it is claiming:

> The inputs relevant to this result are equivalent to the inputs that produced the stored result.

If that claim is wrong, the cache can return stale output while everything looks green.

Therefore a task cache needs a model of relevant inputs such as:

- source files;
- configuration files;
- dependency outputs;
- lockfiles;
- tool/runtime versions;
- selected environment variables;
- command definitions;
- other semantic inputs.

This is why we will not celebrate a cache-hit percentage until we trust the cache key.

Performance built on unsound invalidation is just faster incorrectness.

## Generation is part of the platform

If a repository has conventions, people need a reliable way to create new projects that satisfy them.

The weak approach is:

```text
copy packages/old-package packages/new-package
find-and-replace names
hope nothing stale came along
```

The platform approach is a **generator**.

A generator should turn explicit inputs and repository conventions into deterministic state.

For example:

```text
name = payments
kind = service
language = typescript
```

could produce:

- directory structure;
- manifests;
- task metadata;
- tests;
- ownership/configuration;
- documentation;
- dependency declarations.

Generation is not just convenience.

It is how architecture becomes the default path instead of a wiki recommendation.

## Migrations are how the platform changes its mind

Generators handle new state.

Migrations handle existing state.

Suppose version 1 of the starter creates:

```text
tsconfig.json
```

with one repository convention.

Later we decide the convention is wrong.

A starter that can only generate new repositories has two bad options:

1. tell existing users to manually edit dozens or hundreds of projects;
2. tell them to regenerate the repository and move their code.

Neither is a serious upgrade story.

A migration should encode the transformation from old contract to new contract.

That is why our eventual Nx role is about generators, codemods, and migrations—not just initial scaffolding.

## Policy is not documentation

Suppose our architecture says:

> applications may depend on shared libraries, but shared libraries may not depend on applications.

If that rule exists only in `ARCHITECTURE.md`, we have guidance.

If CI can detect and reject a violating dependency edge, we have enforced policy.

The difference matters.

A platform needs to distinguish:

- documented expectation;
- locally checked convention;
- CI-enforced invariant;
- automatically repaired/generated state.

We will use the word **policy** for rules whose enforcement path is explicit.

Until then, they are conventions or requirements waiting for implementation.

## Root commands are APIs

One of the easiest ways to make a monorepo unpleasant is to require every developer to understand every underlying tool before performing routine work.

Another easy way is to hide everything behind a giant magical wrapper nobody understands.

We need a middle path.

The repository should eventually expose stable root operations such as:

```text
bootstrap
doctor
check
test
build
format
generate
```

These commands become an interface used by:

- humans;
- CI;
- scripts;
- coding agents;
- onboarding instructions.

That makes them APIs.

But a root command should **delegate**, not duplicate.

For example, `check` may invoke the task graph.

It should not quietly reimplement the task graph in a 300-line shell script.

This becomes one of our architecture rules:

> Root commands provide a stable interface to repository authorities; they do not become shadow authorities.

## The control plane and the work plane

A useful way to summarize the architecture is to borrow a control-plane/work-plane distinction.

The **work plane** is product code:

- applications;
- services;
- libraries;
- tests;
- tools;
- documentation products.

The **control plane** describes how that work participates in the repository:

- project identity;
- dependency relationships;
- tool versions;
- task definitions;
- task dependencies;
- cache inputs/outputs;
- generators;
- migrations;
- policy;
- CI behavior;
- release behavior.

The starter we are building is primarily a reusable control plane.

That is why a monorepo starter is much more than a sample application layout.

A starter succeeds when real projects can enter the work plane without every team reconstructing the control plane themselves.

## Our first tool-model collision

When we wrote Article 0, our initial authority hypothesis looked clean:

```text
Bun  -> JavaScript workspace/package authority
mise -> tool/runtime version authority
Moon -> project/task orchestration authority
Nx   -> generators/migrations/codemods
```

Before writing this article, we checked the current documentation for those products.

The boundaries are not naturally clean at all.

### Bun

Current Bun documentation presents Bun as an all-in-one JavaScript runtime/toolkit with:

- runtime;
- package manager;
- test runner;
- bundler.

We currently want only part of that authority surface at the repository level: JavaScript package/workspace management, with the runtime role evaluated separately and Vitest planned as the unit/integration test authority.

### mise

Current mise documentation covers:

- development tool/runtime installation and version switching;
- environment loading;
- a task system with dependencies and parallel execution;
- freshness/last-modified behavior.

We currently want mise primarily as the environment/tool-version authority.

If we start using mise's task graph for normal repository build/test/lint work while Moon also owns task orchestration, we will have two control planes.

That is a boundary we need to prevent or deliberately redesign.

### Moon

Current Moon v2 documentation describes:

- workspace projects;
- task definitions;
- project dependencies;
- action/task execution graphs;
- affected behavior;
- caching;
- a proto-backed toolchain capable of installing/managing languages and package managers.

The last capability overlaps directly with the role we tentatively assigned to mise.

Moon can also derive ecosystem metadata and dependencies through its language/toolchain integration, which may be valuable even if it does not own executable version installation.

We need to test how separable those concerns actually are.

### Nx

Current Nx documentation describes a broad system with:

- project graphs;
- task graphs;
- affected execution;
- caching;
- plugins;
- generators;
- migrations;
- executors;
- sync generators.

That significantly overlaps Moon.

Our current design intentionally constrains Nx to generation/evolution work.

But "we promise not to use the other features" is not yet proof that the dependency remains lightweight or conceptually clean.

We will test that later.

## Engineering log: the tools overlap more than the diagram suggested

This is the first useful correction to the tidy architecture diagram from Article 0.

### Expected

We expected some feature overlap between candidate tools but assumed the intended authority split would be easy to describe.

### Tried

We reviewed current first-party documentation for Bun, mise, Moon, and Nx before installing any of them.

### Observed

Each candidate covers a larger slice of the platform than the role we intend to assign it:

```text
Bun:
  runtime + package manager + test runner + bundler

mise:
  tool versions + environment + tasks

Moon:
  projects + tasks + graphs + cache + affected + toolchain

Nx:
  projects + tasks + graphs + cache + affected + plugins + generators + migrations
```

### Learned

A tool inventory is a poor architecture diagram.

Products optimize for useful feature sets. Our repository needs explicit authority boundaries that may be narrower than those feature sets.

We also learned that the word **toolchain** needs qualification. mise and Moon can both legitimately claim toolchain responsibilities while referring to overlapping but not necessarily identical concerns.

### Changed

We added `docs/architecture/system-model.md` and established two rules:

1. **tool capability does not imply architectural authority**;
2. ambiguous terms are qualified by scope when needed.

We also recorded the overlap as an unresolved experiment rather than a solved decision.

### Verified

The system model now separates:

- repository from workspace;
- project from package;
- package/dependency graph from project graph;
- project graph from task graph;
- changed files from affected work;
- environment toolchain from orchestrator toolchain integration;
- generators from migrations;
- conventions from enforceable policy.

Later articles can now challenge a boundary without changing the meaning of the underlying architectural concern.

## Why not just adopt one tool's vocabulary?

There is a reasonable counterargument:

> If Moon is going to be the orchestrator, why not just use Moon's concepts everywhere?

Because we want the architecture to be reversible.

If every conceptual boundary is defined in Moon-specific terms, replacing Moon later becomes both a tooling migration and a language migration.

The same is true for Nx, Bun, or mise.

Instead, we want this relationship:

```text
architecture concept
      ↓ implemented by
current tool
```

not:

```text
current tool concept
      = architecture itself
```

This does not mean inventing a pointless abstraction layer over every command.

It means documentation and decision records should name the repository concern first.

For example:

```text
Concern: repository task orchestration
Current authority: Moon
```

If Moon is replaced, the concern remains.

That is architectural reversibility.

## A concrete model

Here is the conceptual system we are working toward:

```text
Git repository
│
├── environment/toolchain contract
│
├── repository workspace
│   ├── project: web
│   │   ├── package metadata (JavaScript)
│   │   └── tasks: build, test, lint, dev
│   │
│   ├── project: ui
│   │   ├── package metadata (JavaScript)
│   │   └── tasks: build, test, lint
│   │
│   ├── project: browser-tests
│   │   └── tasks: test-e2e
│   │
│   └── project: search
│       ├── Cargo metadata (Rust)
│       └── tasks: build, test, lint
│
├── project graph
│   ├── web -> ui
│   └── browser-tests -> web
│
├── task graphs
│   └── generated per invocation
│
├── generation/migration system
├── quality/governance system
└── delivery/release system
```

Notice what is *not* true:

- every project is not a JavaScript package;
- the JavaScript workspace is not the whole repository workspace;
- the package graph is not automatically the entire project graph;
- the project graph is not the task graph;
- CI is not the task authority;
- generators are not one-time setup scripts;
- caches are not just performance knobs.

That negative space is part of the architecture.

## Testing the model with examples

Before we encode tooling, we can pressure-test the vocabulary with representative future repository units.

| Example | Repository project? | JS workspace package? | Ecosystem package? | Typical tasks? |
| --- | --- | --- | --- | --- |
| React web app | yes | yes | usually yes/private | build, test, lint, dev |
| shared TS UI library | yes | yes | yes | build, test, lint |
| Playwright E2E suite | yes | maybe | maybe/private | test-e2e |
| repository doctor | yes | maybe | maybe/private | test, run |
| Rust CLI/service | yes | no | Cargo package | build, test, lint |
| Python service | yes | no | Python package/project | test, lint, run |
| root architecture docs | no | no | no | repository-level validation may read them |
| generated starter fixture | maybe, depending on test model | maybe | maybe | generate, verify |

The table exposes why "package" is too narrow as the universal project abstraction.

It also shows why project identity cannot come solely from one language's package manifest.

## What this means for directory design

We are deliberately **not** choosing the final directory taxonomy in this article.

That comes later in the repository-kernel arc.

But the system model gives us constraints for that future decision.

A directory taxonomy should not imply:

```text
apps = deployable JavaScript things
packages = every reusable thing in every language
```

unless that is actually the architecture we intend.

Instead, directory names should help humans navigate while project metadata and native ecosystem metadata describe executable relationships.

Filesystem layout is an information architecture choice.

It should not become an accidental dependency system.

## What this means for CI

A common CI design starts with workflow jobs:

```text
lint job
test job
build job
```

and then gradually embeds repository knowledge into YAML:

```text
if apps/web changed, run this
if packages/ui changed, run that
install this version here
repeat task command there
```

That turns the CI provider into a shadow control plane.

Our model says CI should eventually ask the repository:

- what environment do you require?
- what projects/tasks are affected?
- what canonical validation operation should run?
- what results can be reused?

GitHub Actions should orchestrate hosted execution.

It should not independently rediscover repository architecture.

We will enforce that principle when we reach the delivery arc.

## What this means for AI agents

AI-ready architecture is often discussed as though we need special agent-only machinery everywhere.

Some of that can help.

But the first requirement is simpler: the repository itself must be legible.

An agent needs to answer the same questions a human does:

- what kind of project is this?
- what may it depend on?
- which command validates it?
- which tool owns formatting?
- how do I generate a new sibling project?
- what files are generated?
- what must not be edited manually?
- what checks prove the change correct?

A repository with five competing task systems and undocumented authority boundaries is difficult for both humans and agents.

So the system model we are writing now is already part of the AI-native work, even though we have not created agent instructions yet.

Good agent context begins with good architecture.

## The repository increment

This article adds:

```text
docs/architecture/system-model.md
articles/01-the-monorepo-is-a-platform.md
```

and updates the repository entry point and roadmap to reflect the new checkpoint.

The system-model document is intentionally shorter and more normative than this article.

The article teaches the reasoning.

The architecture document preserves the contract.

That separation matters because future tooling and agents should not need to parse an entire narrative article to discover what the repository means by "project" or "task graph."

## Reproducing this checkpoint

Clone the repository and start from the Article 0 checkpoint:

```bash
git clone https://github.com/thomascarter613/monorepo-guide.git
cd monorepo-guide
git switch series/00-first-principles
```

The Article 1 work is maintained on:

```text
series/01-platform-mental-model
```

To inspect it:

```bash
git fetch origin
git switch series/01-platform-mental-model
```

Then inspect the increment relative to Article 0:

```bash
git diff --stat series/00-first-principles..series/01-platform-mental-model
git diff --name-status series/00-first-principles..series/01-platform-mental-model
```

At this stage there is still no installed repository quality toolchain.

So, just as in Article 0, we must distinguish structural verification from automated contract tests.

## Verification

We can verify the intended structure manually:

```bash
test -f docs/architecture/system-model.md
test -f articles/01-the-monorepo-is-a-platform.md
```

We can verify that the architecture model includes the key distinctions:

```bash
grep -n "Project is not" articles/01-the-monorepo-is-a-platform.md
grep -n "Capability versus authority" docs/architecture/system-model.md
grep -n "environment toolchain" docs/architecture/system-model.md
grep -n "task graph" docs/architecture/system-model.md
```

And we can inspect the cumulative Git history:

```bash
git log --oneline --decorate --graph --all
```

What we **cannot** honestly claim yet is that a repository validator automatically enforces the terminology document, links, headings, or roadmap state.

That capability belongs later in the series.

We will backfill checks as the quality system becomes real.

## Research references for this checkpoint

We checked current first-party documentation on August 27, 2026 before finalizing the model:

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

These links are evidence for current product capabilities, not permanent architecture authorities. If those products evolve, our architecture vocabulary should still make sense.

## What we have decided

After this checkpoint, the following definitions are part of the repository architecture language:

- the repository is the containing version-control boundary;
- the repository workspace is the coordinated set of participating projects;
- a project is not synonymous with a package;
- ecosystem dependency graphs can inform but do not fully define the repository project graph;
- the project graph is not the task graph;
- a target is a project-scoped task;
- affected work is broader than changed files;
- cache reuse is a correctness claim;
- generators create or update state from explicit inputs and conventions;
- migrations move existing state between repository contracts;
- policy requires an explicit enforcement path;
- root commands are stable interfaces that delegate to authorities;
- tool capability does not imply architectural authority.

## What remains unresolved

We have intentionally **not** decided several implementation questions yet:

- whether mise or Moon should physically install every executable Moon needs;
- how much of Moon's proto-backed toolchain integration can or should remain active;
- whether mise tasks should exist at all beyond environment/bootstrap operations;
- how Nx can be constrained to generation/evolution without creating a second project/task authority;
- whether Bun should also be the JavaScript runtime authority or only the package/workspace authority;
- how project identity will be declared/discovered across languages;
- how the root command API will be implemented;
- what directory taxonomy best reflects this model.

Those are not omissions.

They are queued decisions.

## Checkpoint

Article 0 asked:

> What properties must the finished starter prove?

Article 1 asks:

> What system are those properties describing?

Our answer is no longer "a repository containing packages."

It is:

> **a repository-embedded developer platform with explicit environment, workspace, dependency, execution, generation, governance, and delivery layers.**

That model is deliberately broader than JavaScript and deliberately narrower than any one tool's marketing surface.

We now have vocabulary stable enough to do the next dangerous thing: turn broad goals into explicit, testable requirements before choosing which tools are authoritative for each concern.

That is Article 2.
