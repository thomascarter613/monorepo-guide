# Choosing Authorities: Who Owns Packages, Tools, Tasks, Generation, and CI?

*Building the Ultimate Monorepo Starter from First Principles — Article 3*

*A tutorial, engineering log, architecture journal, and reproducible build from an empty directory to a production-grade polyglot workspace.*

---

We finally have enough vocabulary to make a dangerous decision.

In Article 0, we defined what the finished starter must prove.

In Article 1, we stopped calling every directory with a `package.json` the same thing and built a tool-independent model of repositories, workspaces, projects, packages, project graphs, task graphs, toolchains, generation, policy, and delivery.

In Article 2, we turned broad goals such as *reproducible*, *polyglot-ready*, and *locally/CI consistent* into stable product requirements.

Now we can ask the question most monorepo tutorials ask much earlier:

> Which tool should own what?

The delay matters.

If we had answered this in Article 0, the answer would have been a list of preferences:

- Bun;
- mise;
- Moon;
- Nx;
- GitHub Actions.

That is a technology stack.

It is not an architecture.

The architecture appears only when we can answer a harder set of questions:

- Which concern does each tool own?
- Which other tools are allowed to read or transform that state?
- Which overlapping features are deliberately disabled?
- Which graph is authoritative?
- Which lockfile is authoritative for which layer?
- What can CI decide that the repository task graph should not decide?
- What evidence would make us reverse each choice?

That is what this article does.

And the first result is already messier—and more useful—than the neat diagram we started with.

## The repository state before Article 3

Article 3 begins from current `main` at:

```text
44951cd93646c2fb2dda63f384238401ef2bbc8c
```

The working branch is:

```text
series/03-authority-boundaries
```

At this point the repository still contains **no installed monorepo implementation toolchain**.

That is intentional.

We are choosing responsibility boundaries before writing configuration that accidentally chooses them for us.

The increment begins with:

```text
docs/requirements/003-authority-boundaries.md
docs/journal/003-authority-boundaries.md
```

Then we preserve the dated product-capability research separately:

```text
docs/artifacts/003-authority-capability-evidence.md
```

And the current-state architecture decision lives in:

```text
docs/architecture/authority-map.md
```

This article is the reader-facing explanation of those records.

## “One tool per concern” sounds simpler than it is

Our first architectural principle was:

> One authority per concern.

That can be misunderstood in two opposite ways.

The first bad interpretation is:

> Pick one monorepo tool and make it own everything.

The second bad interpretation is:

> Every tool must have completely non-overlapping features.

Neither is realistic.

Modern developer tools are platforms.

Bun is not only a package manager.

mise is not only a version manager.

Moon is not only a task runner.

Nx is not only a generator framework.

GitHub Actions is not only a place where commands run.

The products overlap because each is trying to make a larger portion of the workflow coherent.

Our job is not to wish that overlap away.

Our job is to decide which overlapping capabilities are **authoritative** in *this repository*.

That gives us a better rule:

> **A tool can participate in a concern without owning the concern.**

A tool may:

- read another authority's files;
- infer relationships from them;
- invoke the authority;
- transform the authority's configuration through a generator;
- host the authority's execution;
- cache a different layer of state;
- expose diagnostics around it.

What it must not do is quietly become a second required source of truth for the same correctness decision.

That distinction becomes the core of this article.

# Our candidate map before research

Before revisiting current documentation, our intended map looked like this:

| Tool | Intended role |
| --- | --- |
| mise | tool/runtime versions and environment entry |
| Bun | JavaScript runtime, package manager, workspaces |
| Moon | project/task graph, affected execution, caching |
| Nx | generators, migrations, codemods |
| GitHub Actions | hosted CI orchestration |

It is elegant.

It is also incomplete.

The current product documentation immediately exposes why.

# Finding 1: Bun is intentionally more than our Bun role

As of this article's research date—August 27, 2026—the Bun homepage describes Bun as an all-in-one JavaScript runtime and toolkit.

Its headline product roles include:

- runtime;
- package manager;
- test runner;
- bundler.

Source:

`https://bun.sh/`

Bun explicitly presents these as independently adoptable capabilities.

That matters because our current roadmap intends to evaluate Vitest later.

If we say:

> Bun is in the repository, therefore Bun test owns testing,

we have skipped the testing requirements and the later comparison entirely.

The right conclusion is narrower:

> Bun can own JavaScript package/workspace state without automatically owning every JavaScript concern it supports.

So our provisional Bun authority becomes:

- JavaScript workspace/package dependency state;
- dependency installation;
- Bun lockfile semantics;
- default runtime for Bun-oriented projects.

Its test runner and bundler remain **capabilities**, not current authorities.

That is our first concrete example of capability ≠ authority.

# Finding 2: mise is not just a version file with a nice CLI

Our mental shorthand for mise was:

> mise pins runtimes and tools.

That is true, but current mise does considerably more.

Its project configuration can declare tools and environment state. Its `mise.lock` can preserve exact resolved versions and, depending on backend support, integrity/download information.

That reinforces mise's fit for `UMS-ENV-001`:

> the repository must declare its supported toolchain closely enough that fresh environments do not depend on undocumented global version choices.

But mise also has a real task system.

Current documentation describes:

- `depends`;
- `wait_for`;
- `depends_post`;
- DAG resolution;
- parallel dependency execution;
- task dependency visualization;
- last-modified checks;
- watch mode;
- examples for build, lint, test, deploy, and development workflows.

Sources:

```text
https://mise.jdx.dev/configuration.html
https://mise.jdx.dev/dev-tools/mise-lock.html
https://mise.jdx.dev/tasks/
https://mise.jdx.dev/tasks/task-configuration.html
https://mise.jdx.dev/tasks/architecture.html
```

That is not a helper alias system.

It is an orchestrator.

And that creates a collision with Moon.

## A configuration that would look reasonable and be architecturally wrong

Imagine we eventually write:

```toml
[tasks.build]
run = "moon run :build"

[tasks.test]
depends = ["build"]
run = "moon run :test"

[tasks.check]
depends = ["build", "test"]
run = "moon run :lint && moon run :typecheck"
```

At first glance, that looks harmless.

mise is just calling Moon, right?

No.

The moment the dependency relationship:

```text
test depends on build
```

exists in mise *and* Moon, we have two task graphs.

The same repository behavior now has two places that can drift.

If someone changes Moon to allow tests without build but forgets the mise DAG, which is correct?

If CI invokes Moon directly but humans invoke mise, do they still perform equivalent work?

If an AI agent sees `mise run check`, should it edit the mise dependency graph or Moon?

We have created exactly the ambiguity `UMS-EXE-001` forbids.

So mise's authority needs an explicit negative boundary:

> **mise may own environment/bootstrap operations, but it must not own routine repository project-task dependency semantics.**

That means mise tasks may still be useful later for things such as:

- install the supported executable toolchain;
- diagnose environment configuration;
- run bootstrap operations that exist before Moon is available.

But build/test/lint/typecheck ordering belongs elsewhere.

# Finding 3: Moon's “toolchain” is much more authoritative than our original diagram admitted

This was the most important finding of the article.

We had been using this sentence:

> mise owns tool versions; Moon owns tasks.

That sentence is too vague to implement safely.

Moon v2 has a proto-backed toolchain layer that can download, install, and manage runtimes and package-manager executables.

Its Bun integration can also automatically install JavaScript dependencies when manifests or the lockfile change.

Sources:

```text
https://moonrepo.dev/docs/concepts/toolchain
https://moonrepo.dev/docs/config/toolchain
https://moonrepo.dev/docs/setup-toolchain
https://moonrepo.dev/docs/guides/javascript/bun-handbook
https://moonrepo.dev/docs/config/workspace
```

If we simply configure:

```text
mise → Bun version
Moon/proto → Bun version
```

we now have two tool-version authorities.

If we additionally allow:

```text
bun install
```

as our documented dependency-install path while Moon automatically installs dependencies before tasks, we also have two package-install entry points with different triggering semantics.

The products can both do the job.

That does not mean both should.

## The useful part of Moon's toolchain integration is not only installation

This is where the decision gets subtle.

We do not want to disable all Moon language awareness.

Moon's JavaScript/Bun integration can provide useful repository semantics:

- infer relationships from `dependencies`, `devDependencies`, and `peerDependencies`;
- understand workspace/package manifests;
- incorporate ecosystem state into hashing;
- locate appropriate binaries;
- enrich project/task behavior.

That is valuable for the project/task graph.

Fortunately, Moon's current documentation also shows that language integrations can consume binaries already available from the environment instead of pinning/downloading their own versions.

It also documents switches for disabling automatic dependency installation.

That gives us the most important boundary in the current architecture:

```text
mise owns: executable version resolution + installation
Bun owns:  JavaScript dependency resolution + installation + lock state
Moon owns: project/task meaning derived from those states
```

Moon is allowed to **understand** Bun without owning Bun's version or package installation.

That is a much stronger statement than “mise for tools, Moon for tasks.”

## What later implementation must prove

We have not configured this yet.

So the exact Moon options remain an implementation hypothesis.

Later we need to prove a configuration with these semantics:

1. mise installs/resolves the supported Bun and Moon executables;
2. Moon does not establish a competing Bun version pin through proto;
3. Moon's JavaScript/Bun ecosystem integration still works;
4. Moon automatic dependency installation is disabled;
5. explicit Bun install remains sufficient;
6. project inference/hashing/task behavior remains correct.

Possible Moon mechanisms include:

- leaving toolchain `version` fields unset;
- disabling `.prototools` inheritance where appropriate;
- disabling `installDependencies`;
- disabling workspace pipeline dependency installation;
- forcing PATH/global executables if needed.

We are deliberately **not** freezing the exact configuration in this article.

That belongs in the implementation increment where we can run it.

This is an important discipline:

> Architecture decides the boundary. Implementation experiments decide the exact knobs required to enforce it.

# Finding 4: Moon fits the shape of the task authority—but we have not proven it

Moon's current documentation gives us the expected shape for the execution requirements we wrote in Article 2.

It models task relationships as a DAG.

It supports explicit dependencies.

It runs independent work in parallel and required work in topological order.

It supports affected analysis over:

- files;
- environment variables;
- project/task graph relations.

It supports task hashing and cached results.

It can expose task graphs for inspection.

Sources:

```text
https://moonrepo.dev/docs/how-it-works/task-graph
https://moonrepo.dev/docs/concepts/affected
https://moonrepo.dev/docs/run-task
https://moonrepo.dev/docs/commands/task-graph
```

That maps cleanly to:

- `UMS-EXE-001` — one authoritative repository task graph;
- `UMS-EXE-002` — explicit task inputs/outputs;
- `UMS-EXE-003` — correct affected execution;
- `UMS-EXE-004` — cache hits are correctness claims;
- `UMS-OBS-001` — repository graph/state introspection.

But there is a trap here.

Documentation demonstrates capability.

It does **not** prove correctness in our repository.

We have not yet shown that:

- changing a shared package affects the right dependents;
- changing root TypeScript configuration invalidates the right tasks;
- changing an environment variable causes the expected cache miss;
- cached outputs are restored completely;
- Rust and Python projects participate cleanly;
- graph construction overhead is acceptable;
- CI affected behavior matches local behavior.

So the correct status is:

> Moon is the **provisional authority we intend to prove**.

Not:

> Moon won and the architecture problem is solved.

The distinction will matter later when something fails.

# Finding 5: Nx is not “our generator tool” unless we actively constrain it

Nx has one of the largest overlaps in our stack.

Current Nx documentation covers:

- project graphs;
- task graphs;
- task pipelines;
- affected execution;
- local and remote caching;
- plugin-based task inference;
- generators;
- migrations;
- executors;
- release tooling;
- module-boundary tooling.

Sources:

```text
https://nx.dev/docs/concepts
https://nx.dev/docs/features/run-tasks
https://nx.dev/docs/concepts/how-caching-works
https://nx.dev/docs/features/ci-features/affected
https://nx.dev/docs/concepts/nx-plugins
https://nx.dev/docs/reference/nx/generators
```

So describing Nx as:

> the generator layer

is not a fact about Nx.

It is a **policy we are imposing on Nx**.

That is okay.

In fact, that is the whole point of architecture.

## Why keep Nx at all?

If we do not want its task runner, why introduce it?

Because generation and evolution are not small concerns in this starter.

We need to eventually support:

- deterministic project scaffolding;
- project registration;
- repository-wide convention changes;
- codemods;
- versioned migrations;
- upgrading a repository created by an older starter version.

Nx already has mature generator/plugin/migration concepts.

Using that machinery may be cheaper and safer than inventing our own lifecycle engine before we understand the real transformation contracts.

But that value only survives if we can keep the execution boundary clean.

Our provisional Nx contract is:

> **Nx may transform repository state. Nx does not own normal repository execution state.**

A future generator may:

- create project files;
- update `package.json` workspace/package metadata;
- create Moon project/task metadata;
- update TypeScript configuration;
- update documentation;
- add tests.

But after generation finishes, the repository should work normally without requiring:

```text
nx run
nx run-many
nx affected
Nx computation cache
Nx Cloud
```

for routine build/test/lint/typecheck execution.

## A useful distinction: temporary graph vs authoritative graph

An Nx generator may need to inspect a graph to decide what to generate.

That does not automatically violate our one-task-graph principle.

The key question is:

> Does normal repository correctness depend on Nx's graph after the transformation is complete?

If the answer is no, Nx's graph is an implementation detail of the transformation.

If the answer becomes yes, our boundary has failed.

This distinction lets us use powerful tools without automatically handing them the entire architecture.

# Finding 6: GitHub Actions has a graph too

At this point, “one task graph” needs one more qualification.

GitHub Actions workflows have jobs.

Jobs can depend on other jobs.

They run on runners.

They have permissions, matrices, environments, secrets, concurrency rules, artifacts, and reusable workflow composition.

Sources:

```text
https://docs.github.com/en/actions
https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax
https://docs.github.com/en/actions/reference/workflows-and-actions/reusing-workflow-configurations
```

Does that mean GitHub Actions violates our one-task-graph rule?

No.

The rule was too imprecise if interpreted that way.

We need to distinguish:

```text
repository work dependency graph
```

from:

```text
hosted execution/security graph
```

Moon should know that:

```text
library build → application build → application test
```

GitHub Actions may legitimately know that:

```text
untrusted validation → privileged publish job
```

Those are different concerns.

The first is repository semantics.

The second is a hosted security/delivery boundary.

## What GitHub Actions is allowed to own

GitHub Actions owns things that only the hosted platform can reasonably own:

- event triggers;
- runner selection;
- job OS/environment;
- secrets wiring;
- token permissions;
- matrices for hosted platform variation;
- concurrency/cancellation;
- artifact transfer;
- deployment environments;
- privileged release boundaries.

Current workflow syntax also lets us scope `GITHUB_TOKEN` permissions at workflow or job level, which aligns directly with `UMS-SEC-003`.

## What GitHub Actions must not own

Workflow YAML should not become the only place that knows:

- how to build the repository;
- how to lint it;
- which project depends on which package;
- which task must run before another;
- what the affected set is;
- which task output is cache-valid.

The intended flow is:

```text
GitHub event
    ↓
runner + permissions + hosted job boundary
    ↓
repository command
    ↓
Moon
    ↓
project/ecosystem tool
```

That is what we mean when we say:

> CI orchestrates repository policy; it does not reimplement it.

# The authority map

We can now state the current architecture more precisely.

| Concern | Provisional authority |
| --- | --- |
| executable/tool version resolution and installation | **mise** |
| JavaScript package/workspace dependency state and installation | **Bun** |
| default runtime for Bun-oriented JS/TS projects | **Bun** |
| repository project graph | **Moon** |
| routine task DAG and execution | **Moon** |
| affected analysis | **Moon** |
| task-result hashing/cache semantics | **Moon** |
| generators/scaffolding | **Nx** |
| migrations/codemods | **Nx** |
| hosted CI event/runner/permission orchestration | **GitHub Actions** |
| developer-facing root command API | **the repository itself** |
| future Rust/Python package semantics | **ecosystem-native tools** |

The canonical current-state version is:

`docs/architecture/authority-map.md`

The dated evidence behind it is:

`docs/artifacts/003-authority-capability-evidence.md`

# The root command API gets its own authority

One line in that table deserves explanation:

> developer-facing root command API → the repository itself

Why not Moon?

Why not mise?

Because the public interface and the implementation behind the interface are different concerns.

A future developer should be able to ask the repository to:

```text
bootstrap
doctor
check
test
build
generate
migrate
```

without having to remember every internal tool boundary.

But the wrapper must delegate.

Conceptually:

```text
repo bootstrap
  -> mise
  -> Bun install

repo check
  -> Moon

repo generate package
  -> Nx generator
  -> repository verification through Moon
```

The wrapper owns:

- stable names;
- argument semantics;
- help;
- user-facing error context;
- public contract.

It must **not** own:

- duplicated project dependencies;
- duplicated task ordering;
- a hidden package manager;
- a second affected algorithm.

This gives us a useful architecture pattern:

> stable interface, delegated authority.

# Why we are not choosing one monorepo platform to own everything

At this point, a reasonable objection is:

> Why not use Nx for almost all of this?

That would be a legitimate architecture.

Nx has project graphs, tasks, affected execution, caching, generators, migrations, boundaries, releases, and CI tooling.

For many TypeScript-heavy monorepos, consolidating around Nx could be simpler than our split.

We are not rejecting that design because Nx is incapable.

We are pursuing a different optimization target.

Our starter is trying to prove:

- a genuinely polyglot root model;
- ecosystem-native authority below the repository layer;
- explicit separation between environment management and orchestration;
- generator/migration reuse without tying routine execution to the generator framework;
- replaceable concern authorities where practical.

Moon's task model and polyglot orientation fit the orchestration concern we want to test.

Nx's generator/migration infrastructure fits the evolution concern we want to test.

That split may be brilliant.

It may also prove to be unnecessary complexity.

The only acceptable way to find out is to implement and test it.

So we write the exit criteria now.

# Exit criteria: when we abandon the mise authority

mise loses environment/tool authority if evidence shows that:

- supported tool installation is unreliable across our target developer/CI platforms;
- its lock/backend model leaves unacceptable nondeterminism for required tools;
- Moon cannot safely consume mise-provided runtimes while retaining needed ecosystem behavior;
- Nix/Dev Container integration creates irreconcilable competing version sources;
- the combined system is materially more complex than allowing a different tool to own end-to-end toolchains.

We are choosing mise.

We are not promising to defend it against evidence.

# Exit criteria: when Bun loses workspace authority

Bun loses JavaScript workspace/package authority if later Article 13 experiments expose:

- unreliable internal workspace dependency semantics;
- lockfile/install behavior that fails our clean-install requirements;
- ecosystem incompatibilities that force structural workarounds;
- publish/release scenarios we require but cannot support cleanly;
- integration problems with Moon that make the combined architecture brittle.

The fact that Bun's homepage benchmark is fast is interesting.

It is not our acceptance test.

# Exit criteria: when Moon loses task authority

Moon loses task authority if later experiments show:

- representative TypeScript tasks require material configuration duplication;
- affected analysis misses required dependents or global invalidation;
- cache keys cannot model material source/config/environment influences;
- restored outputs are incomplete or hard to reason about;
- cross-language relationships require root architecture exceptions;
- Python integration remains materially second-class in the architecture we need;
- local/CI parity becomes harder rather than easier;
- measured overhead is unacceptable;
- a competing orchestrator satisfies the same requirements with materially lower complexity.

This last point is why Article 18 still exists.

Article 3 assigns authority.

Article 18 will do the deeper execution-system comparison against Moon, Nx, Turborepo, and Bazel with real repository requirements and workloads.

# Exit criteria: when Nx loses transformation authority

Nx loses generator/migration authority if:

- normal generated projects require Nx execution to build/test/lint;
- maintaining Nx metadata creates a second project model with unacceptable synchronization cost;
- generator determinism/noninteractive testing is awkward;
- migrations depend on Nx executors rather than repository-state transformations;
- a smaller custom transformation layer becomes obviously simpler after our actual contract stabilizes.

This is particularly important because “use Nx only for generators” is not a common product preset we can assume will enforce itself.

We have to prove the restriction is practical.

# Exit criteria: when GitHub Actions loses CI authority

GitHub Actions is not a deep product commitment in the same way.

The architectural requirement is:

> hosted CI orchestrates repository policy rather than redefining it.

If we later move to another CI platform, the same boundary should survive.

That is a sign that we have described the concern rather than the vendor.

# A new failure we should avoid before it happens: generic CI caching

There is another overlap worth recording now.

GitHub Actions can cache directories.

Moon can cache task results.

Those are not necessarily the same cache.

A generic CI cache may be useful later for:

- downloaded tool archives;
- package-manager download caches;
- other bootstrap state.

But if we use `actions/cache` to cache task output directories independently while Moon also claims task-result cache correctness, we can create two different cache-validity models.

That is dangerous.

Our provisional rule is:

> **Moon owns task-result validity. CI cache may optimize other layers only when their semantics are explicitly different.**

Remote Moon cache transport is a later decision.

# Another subtle boundary: Bun scripts are not the task graph

JavaScript projects will almost certainly have scripts.

That is fine.

A future package may contain:

```json
{
  "scripts": {
    "test": "vitest run"
  }
}
```

Moon can invoke that script.

The script is a leaf command.

The problem begins when root scripts start independently encoding repository ordering:

```json
{
  "scripts": {
    "check": "bun run build && bun run test && ..."
  }
}
```

while Moon also defines the real dependencies.

That would create a second orchestration system even if the commands happen to call the same tools.

So our rule becomes:

> ecosystem scripts may express **how a project command runs**; Moon expresses **how repository work relates**.

That is a boundary we can later test automatically.

# The polyglot warning we found in current Moon docs

The entire architecture has claimed from the beginning that it must be genuinely polyglot-ready.

We therefore need to treat language-support details as falsification evidence, not footnotes.

Moon's current v2 toolchain reference includes Rust support and still lists Python-related toolchains using `unstable_*` identifiers.

Source:

`https://moonrepo.dev/docs/config/toolchain`

That does not mean Moon cannot orchestrate Python.

Moon can run system commands.

But it means we **cannot** claim today that the first-class Python integration side of our architecture is solved.

Good.

That keeps Article 42 meaningful.

When we add Python later, we will ask:

- Does the project participate naturally in the graph?
- Can mise own its runtime/tool version cleanly?
- Can Moon model tasks and affected relationships without JS-specific assumptions?
- Do we lose important ecosystem semantics?
- Does the configuration become full of exceptions?

If the answers are bad, Moon's current authority decision is allowed to fail.

# What we changed in the repository

Article 3 is another architecture increment rather than a tooling-install increment.

The core new files are:

```text
docs/requirements/003-authority-boundaries.md
docs/journal/003-authority-boundaries.md
docs/artifacts/003-authority-capability-evidence.md
docs/architecture/authority-map.md
articles/03-choosing-authorities.md
```

We also update:

```text
docs/architecture/requirements-ledger.md
README
articles/README.md
docs/series-roadmap.md
docs/series.yaml
```

and close with:

```text
docs/verification/003-authority-boundaries.md
```

No package manager configuration is added.

No Moon workspace is created.

No Nx workspace is initialized.

No GitHub Actions workflow is added.

That absence is still deliberate.

The architecture boundary now exists before the tools have a chance to create conflicting state.

# Reproducing the checkpoint

Start from Article 3's accepted starting commit:

```bash
git clone https://github.com/thomascarter613/monorepo-guide.git
cd monorepo-guide

git switch --detach 44951cd93646c2fb2dda63f384238401ef2bbc8c
```

Inspect the pre-increment state:

```bash
git status --short
git log -1 --oneline
```

Then inspect the Article 3 branch:

```bash
git switch series/03-authority-boundaries

git diff --stat 44951cd93646c2fb2dda63f384238401ef2bbc8c...HEAD
```

Inspect the architecture/evidence directly:

```bash
sed -n '1,260p' docs/architecture/authority-map.md
sed -n '1,260p' docs/artifacts/003-authority-capability-evidence.md
sed -n '1,260p' docs/journal/003-authority-boundaries.md
```

Because no repository quality toolchain exists yet, verification for this increment remains structural/manual plus source-evidence review.

That will change soon.

# Verification questions for Article 3

The verification record asks at least:

1. Did we research current first-party documentation rather than rely on remembered product boundaries?
2. Did we record overlap honestly?
3. Does every core concern have one provisional authority?
4. Did we distinguish Moon ecosystem integration from Moon version/install authority?
5. Did we explicitly constrain mise's task DAG?
6. Did we explicitly constrain Nx's task/affected/cache capabilities?
7. Did we distinguish GitHub's hosted job graph from Moon's repository task graph?
8. Did we define exit criteria?
9. Did we avoid claiming configuration/tests that do not yet exist?
10. Can later articles cite a stable authority map rather than re-deciding ownership implicitly?

If any answer is no, Article 3 is not complete.

# What the authority map does not prove

This article is easy to overstate because the diagram now looks satisfyingly complete.

We have **not** proved:

- mise bootstrap reproducibility;
- Bun workspace installation correctness;
- Moon project discovery;
- Moon affected correctness;
- Moon cache correctness;
- Nx generator isolation;
- GitHub Actions/local parity;
- remote caching;
- Rust integration;
- Python integration;
- developer feedback performance;
- CI economics.

The product requirement ledger should therefore remain mostly `Accepted`.

One requirement does gain meaningful partial evidence:

`UMS-EXE-001` requires the authority for project/task relationships to be documented.

Article 3 does that.

But the same requirement also requires us to prove that root commands, CI, and generators do not introduce competing execution graphs.

Those do not exist yet.

So the honest state is partial, not verified.

That is exactly why Article 2 introduced evidence states.

# The architecture after Article 3

We can finally draw the intended control flow without hand-waving:

```text
                     repository-owned command API
                                │
          ┌─────────────────────┼─────────────────────┐
          │                     │                     │
     bootstrap/env         normal work         transform repo
          │                     │                     │
        mise                  Moon                    Nx
          │                     │                     │
   installs versions      task/affected/cache    generator/migrate
          │                     │                     │
          ├─────────┐           │            ┌────────┴─────────┐
          │         │           │            │                  │
         Bun     other tools   task leaf   Bun-owned state   Moon-owned state
          │                     │
    JS packages            ecosystem tool

GitHub Actions sits outside this flow as hosted orchestration:

GitHub event/runner/permissions
          │
          ▼
repository-owned command API
          │
          ▼
         Moon
```

This diagram is more complicated than:

```text
Use Nx.
```

That complexity has to earn its keep.

The next several arcs are where we find out whether it does.

# The deepest lesson from this article

The important decision is not:

> We chose Moon over Nx.

It is:

> We stopped treating tools as indivisible architecture units.

A product can be excellent at several jobs.

Our repository can still authorize it for only one.

That is how we preserve replaceability and avoid accidental sources of truth.

The consequence is that boundaries must be explicit enough to test.

“mise for tools, Moon for tasks” was not explicit enough.

The improved form is something closer to:

> mise is the supported executable version/install authority. Moon may enable ecosystem integrations but must consume those environment-provided executables rather than pinning a competing version. Bun owns JavaScript dependency install/lock state; Moon may read that state for graph/hash semantics but must not silently install it. Moon owns routine repository project/task/affected/cache semantics. Nx may transform repository state through generators/migrations but must not be required for routine execution. GitHub Actions owns hosted event/runner/permission orchestration and delegates repository work to the canonical repository/Moon interface.

That is wordier.

It is also implementable.

And, more importantly, falsifiable.

# Next: make reversible decisions first-class

We now have consequential architecture choices.

That creates a new problem.

Where do we record the decisions so future evidence can supersede them without rewriting history?

Article 4 is:

**Reversible Architecture: ADRs, Experiments, and Exit Criteria**

We already have an engineering-record directory for decisions and a template from the repository's documentation protocol.

Next we will turn that infrastructure into an explicit decision lifecycle:

- proposed vs accepted decisions;
- evidence requirements;
- reversible vs expensive choices;
- experiments;
- exit criteria;
- superseding decisions;
- current-state architecture vs historical rationale.

Article 3 gives us the first real architecture choices worth recording that way.

Then, with first principles complete, we can finally begin the repository kernel.
