# What Does an “Ultimate Monorepo Starter” Actually Mean?

*Building the Ultimate Monorepo Starter from First Principles — Article 0*

*A tutorial, engineering log, architecture journal, and reproducible build from an empty directory to a production-grade polyglot workspace.*

---

There is a tempting way to start a monorepo tutorial.

Create a `package.json`. Add a workspace glob. Install a task runner. Copy a TypeScript configuration. Add a formatter. Add CI. Declare victory.

We are not going to do that.

Before we choose configuration, we need to decide what the configuration is supposed to accomplish. Before we install a task runner, we need a model of what tasks and projects mean. Before we optimize CI, we need to decide what correctness we are unwilling to trade for speed. Before we call anything “production-grade,” we need tests capable of proving that claim wrong.

And before we use the word **ultimate**, we should make that word expensive.

This series is an attempt to build a monorepo starter that earns the label through evidence rather than tool count. We will build the real repository while writing the articles. Every installment will leave behind an actual increment: requirements, implementation, tests, verification, and a repository checkpoint. When an experiment fails, we will keep the failure in the engineering log instead of editing history until the architecture looks inevitable.

That means Article 0 contains almost no exciting configuration.

That is deliberate.

Our first task is to define the system we are trying to build, the properties it must demonstrate, the things it should intentionally refuse to become, and the assumptions we plan to attack later.

## The real starting state

The canonical repository for this series is:

`https://github.com/thomascarter613/monorepo-guide`

The hosting repository already has an initial Git commit because we need somewhere to preserve the engineering history. At the baseline commit, however, the working tree is intentionally almost empty: only zero-byte `README` and `LICENSE` placeholders exist.

So when we say we are starting “before `git init`,” we mean **before the first meaningful architectural decision is encoded in the repository**. We are not going to invent a fake empty-directory history when GitHub necessarily gives us a repository shell.

The baseline commit is:

```text
c7bfe90d22ebe89f3a03c39590d5de9dc68c2d1c
```

If you want to inspect the exact pre-series state:

```bash
git clone https://github.com/thomascarter613/monorepo-guide.git
cd monorepo-guide
git switch --detach c7bfe90d22ebe89f3a03c39590d5de9dc68c2d1c
find . -maxdepth 2 -type f -not -path './.git/*' -print
```

There is effectively nothing to reverse-engineer. That gives us a rare luxury: we can make the architecture follow the reasoning rather than writing reasoning that justifies an architecture we inherited.

## “Ultimate” cannot mean “contains the most tools”

A starter repository is easy to make impressive in a screenshot.

Add a dozen badges. Add five layers of configuration. Add every fashionable checker. Add containers, a remote cache, a release bot, a generator framework, a custom CLI, and an ASCII banner. The repository will *look* sophisticated before it has hosted a single real project.

But complexity is not capability.

Every tool we add creates at least one new boundary:

- what does this tool own?
- what state does it read?
- what state does it write?
- what invokes it?
- what does it duplicate?
- how is it upgraded?
- how does it fail?
- how does CI reproduce it?
- how does a new engineer discover it?
- how do we know it is still necessary a year later?

An “ultimate” starter therefore cannot be the repository with the largest technology inventory. It needs to be the repository with the strongest **operating model**.

We will use a lot of tools by the time this series is finished. But each tool needs a job description, and two tools should not quietly become competing authorities over the same concern.

That gives us our first architectural principle:

> **One authority per concern.**

Multiple tools can participate in a workflow. They cannot all be the source of truth for the same thing.

## We are building a developer platform in miniature

A monorepo is often described as “multiple projects in one Git repository.”

That description is technically correct and architecturally insufficient.

Once several projects share a repository, we need answers to questions that do not exist—or do not exist in the same form—in a collection of unrelated repositories:

- How are projects discovered?
- How do projects depend on one another?
- Which dependencies are allowed?
- Which task must run before another task?
- Which projects changed?
- Which tasks are affected by those changes?
- Which results can be cached safely?
- Which tool versions are supported?
- How does a fresh machine reconstruct the environment?
- How are new projects created without copying an old project and its mistakes?
- How do repository-wide policy changes reach existing projects?
- How does CI understand the same project graph as a developer’s machine?
- How are packages versioned and released?
- How do non-JavaScript projects participate without becoming second-class citizens?
- How does an AI coding agent discover the same boundaries a human engineer is expected to respect?

Those are platform questions.

So the product we are building is not merely a folder structure. It is a **small internal developer platform embedded in a repository**.

That framing changes our priorities. Documentation is no longer decoration. Root commands are APIs. Generators are production code. CI is an execution environment for repository policy. Project metadata is part of the system model. Upgrade machinery is not a later convenience; it is how the starter survives being successful.

## Who we are building this for

The primary reader is an intermediate engineer who already understands Git, terminals, TypeScript, and package managers but has not designed a serious monorepo platform.

We will therefore explain the mechanism, not just paste the configuration.

At the same time, the series needs to remain useful to senior, staff, and principal engineers who care about different questions: boundary ownership, failure modes, organizational scaling, feedback latency, CI economics, migration cost, reversibility, and governance.

That gives the series two simultaneous layers.

The tutorial layer asks:

> How do we make this work?

The architecture journal asks:

> Why should it work this way, what alternative did we reject, and what evidence would make us change our mind?

Neither layer is optional.

## TypeScript-first does not mean JavaScript-only

We are going to establish the first-class developer experience around TypeScript and Bun. That is where most of our early examples, packages, generators, tests, and tooling integrations will live.

But the architecture must be **genuinely polyglot-ready**.

That phrase is easy to claim and easy to fake. A repository does not become polyglot because we can drop a Rust directory somewhere under `tools/` and run `cargo build` manually.

A non-JavaScript project needs to participate in the repository’s operating model:

- toolchain setup;
- project discovery;
- task orchestration;
- affected execution;
- caching rules;
- CI scheduling;
- dependency or contract relationships;
- quality gates;
- developer documentation;
- diagnostics;
- and, where relevant, release behavior.

Later in the series, we will add a real Rust project. Then we will add a second ecosystem—currently planned as Python—specifically to search for hidden JavaScript assumptions.

If adding those projects requires us to redesign the repository root, our earlier “polyglot-ready” claim was false.

That is the point of the test.

## Requirements, preferences, and hypotheses are different things

One of the easiest ways to create architectural dogma is to write every early preference as though it were a requirement.

We will keep three categories separate.

A **requirement** describes a property the finished starter must provide. For example:

- a fresh clone can reconstruct the supported development environment;
- CI and local development share task definitions;
- new projects can be generated deterministically;
- the repository can integrate a non-JavaScript project without replacing its orchestration model.

A **preference** is an implementation choice we currently favor. For example:

- Bun as the JavaScript workspace/package authority;
- Moon as the project/task orchestrator;
- mise as the runtime/tool version manager;
- Biome rather than an ESLint + Prettier pair.

A **hypothesis** connects a preference to a requirement:

> We believe Moon can remain the repository task authority when we introduce Rust and Python projects.

That statement can fail.

Good.

We want architecture that can encounter evidence.

## The properties we intend to prove

We have captured the full working contract in `docs/architecture/definition-of-ultimate.md`. The short version is that the finished starter must be:

### Reproducible

A fresh clone should reconstruct the supported environment from repository-declared state rather than undocumented machine history.

This is broader than pinning a runtime version. We will eventually need to reason about package lockfiles, toolchains, environment variables, native dependencies, generated state, containers, caches, and CI images.

### Explicit

Important behavior needs a discoverable source of truth.

If the repository only works because the maintainer remembers that one particular command must be run before another command, that knowledge is part of the architecture whether or not we wrote it down.

Undocumented architecture is still architecture. It is simply harder to operate.

### Deterministic where determinism is available

Project generators should produce stable output for the same inputs. Repository migrations should be testable. Dependency state should be locked. Machine-verifiable rules should replace “remember to do X” whenever practical.

We will not pretend every system is hermetic. We will state the boundary of the guarantees we can actually demonstrate.

### Fast enough to protect feedback loops

A monorepo that knows every project but rebuilds all of them for every change has thrown away valuable information.

We want affected execution, correct caching, and task parallelism—but only after we understand the correctness model. A fast stale cache hit is a bug with excellent latency.

### Consistent between local development and CI

CI should not become a second build system encoded in YAML.

The hosted workflow can decide *when* and *where* to execute work. The repository should decide *what the work is*.

A developer, CI runner, and coding agent should converge on the same canonical checks.

### Governed without becoming hostile

Formatting, linting, types, tests, dependency rules, dead-code checks, secret detection, and release rules should be enforceable.

But a guardrail that only says “no” pushes developers toward bypasses. Failures should tell us what contract we violated and how to fix it.

The correct path should also be the easiest path.

### Polyglot-ready

We covered this above, but it belongs in the definition because we will test it structurally rather than rhetorically.

### Evolvable

A starter that can create repositories but cannot upgrade them has a short useful life.

Generators establish structure. Migrations evolve structure. Codemods update existing code. Versioned repository contracts give those changes a target.

Eventually we will create a repository from an older version of our own starter and upgrade it forward. If the upgrade story is “start over and copy your application into a fresh repo,” we did not build an upgradeable starter.

### Observable

We need to inspect project graphs, task graphs, cache behavior, dependency state, timing, and CI cost.

Statements such as “affected execution is much faster” or “remote caching saves CI time” are hypotheses until we measure them in our repository.

### Secure by default

Security should enter the architecture before release day.

We will address secrets, dependency vulnerabilities, supply-chain concerns, container scanning where relevant, automation permissions, and the security consequences of bots that can modify the repository.

### AI-legible

AI assistance will be a first-class theme in the series, but not a replacement for engineering controls.

The interesting question is not “Can an LLM generate this config?” It almost certainly can.

The interesting questions are:

- Can an agent discover which config is authoritative?
- Can it locate the canonical commands?
- Can it understand project boundaries from repository-local information?
- Can we give it enough context without pasting the entire repository into every prompt?
- Can its output be checked through deterministic validation?
- Can we preserve a clear human/automation authority boundary?

A well-architected repository should improve both human onboarding and agent performance because both benefit from explicit structure.

### Understandable

This is the property that keeps the others from collapsing into ceremony.

If we need a 90-minute oral tradition to explain why the repository works, the starter has failed one of its core jobs.

## Non-goals matter as much as goals

The easiest architecture to extend is one whose scope has no boundary. It is also the easiest architecture to ruin.

We are explicitly not trying to support every language on day one. We are not building a universal abstraction that hides every ecosystem-native tool. Cargo should remain authoritative for Rust package concerns; Python tooling should remain native where that is the better authority. Our repository layer should coordinate ecosystems, not pretend they are identical.

We are not requiring paid remote infrastructure for basic correctness. We may evaluate remote caching or hosted services, but a developer must be able to understand and run the repository without subscribing to its architecture.

We are not encoding a universal application architecture. The starter can provide example projects and recommended boundaries without deciding that every future product is a web frontend plus an API plus a database package.

And we are not optimizing for the most magical possible onboarding command if that magic hides uncontrolled state. “One command” is only valuable when we can explain what the command does and make it repeatable.

## Our initial tool responsibility map is a hypothesis

We already have a strong intended technical direction:

- **Bun** for JavaScript/TypeScript package and workspace operations;
- **mise** for runtime and tool version management;
- **Moon** for project/task orchestration;
- **Nx** for generators, scaffolding, migrations, and codemods—not task execution;
- **Biome** for formatting and linting;
- **Vitest** for TypeScript unit/integration testing where appropriate;
- **Playwright** for browser/end-to-end tests;
- **Syncpack** for dependency-version policy;
- **Knip** for unused files, exports, and dependencies;
- **Lefthook** for Git hook orchestration;
- **GitHub Actions** for hosted CI orchestration;
- **Renovate** for dependency update automation;
- **Changesets** for package change intent and releases;
- **Gitleaks** and **Trivy** for security roles we will define precisely when we implement them.

Notice the wording: **intended**.

We are not going to spend Article 0 pretending we have already proved these choices.

Each tool comparison will happen when its concern becomes concrete. Bun will be compared to pnpm, npm, and Yarn when we need a workspace authority. Moon will be compared to Nx, Turborepo, and Bazel when we have a task-graph model to satisfy. mise will be compared with alternatives when we define the supported environment boundary.

The question will never be “Which tool has the longest feature list?”

It will be:

> What problem are we solving here, which alternatives satisfy it, what did we choose, and what evidence would make us reconsider?

## Some architecture decisions are cheaper to reverse than others

Early-stage systems create an illusion: everything looks easy to change because almost nothing exists yet.

But decisions have different **reversal costs**.

Changing a formatter option is usually cheap.

Changing the directory taxonomy after hundreds of projects and scripts depend on it can be expensive.

Renaming one root command is easy before documentation, CI, generators, human muscle memory, and external automation all consume it. Later, that command behaves like a public API.

Choosing how project identity is represented becomes expensive once every generator, task rule, dependency policy, and CI query relies on it.

So we will deliberately separate:

- **reversible implementation details**, where speed of learning matters more than perfect foresight;
- **structural contracts**, where we should spend more reasoning effort before encoding them broadly.

We will also use architecture decision records and experiments later in Arc I, but the principle starts now: we want a repository that can change its mind without pretending it never had a previous one.

## We are going to try to falsify our own architecture

A starter repository is especially vulnerable to happy-path demonstrations. The author knows how it works, runs commands in the intended order, and naturally avoids the edge cases they forgot to document.

We need adversarial tests.

Here are several assumptions we plan to attack:

### Assumption: Bun can be our JavaScript workspace authority

We will test real internal packages, workspace linking, install behavior, scripts, dependency policy, publishing/release scenarios, and compatibility with the rest of the toolchain.

If the repository accumulates structural workarounds to preserve Bun, the preference loses.

### Assumption: Moon can own task orchestration for a polyglot repo

It is not enough to make TypeScript tasks work. We will introduce Rust and Python, affected execution, caching, cross-project task dependencies, and CI behavior.

If Moon cannot model the work cleanly, we revisit the decision.

### Assumption: Nx can remain generation-only

We want Nx’s generator/migration ecosystem without allowing a second task graph to silently become authoritative.

We will prove this by making routine build, lint, type-check, test, and affected workflows independent of Nx execution.

If our generators require Nx runtime semantics for normal operation, we have crossed the boundary we claimed to establish.

### Assumption: mise can be the toolchain entry point

We will test fresh-machine behavior, shell integration, CI/bootstrap usage, polyglot runtimes, and hidden prerequisites.

The standard is not “mise installed successfully on the author’s laptop.”

### Assumption: CI can reuse local task definitions

When hosted CI arrives, we will resist the temptation to rewrite repository behavior in workflow YAML.

If a check exists only in CI, we should be able to explain why that difference is inherent rather than accidental.

### Assumption: an AI-ready repository is primarily an architecture problem

Later we will give agents repository-local instructions and machine-readable context, record prompts that work and prompts that fail, and validate every generated change through the same quality gates as human work.

If success requires secret conversational context that is not represented in the repository, we have not created an AI-legible engineering environment.

### Assumption: the starter is upgradeable

This one gets a particularly unforgiving test near the end: create a repository from an older starter version, upgrade it with our migration machinery, and verify that user-owned code remains intact.

If we cannot do that, “starter” describes scaffolding, not lifecycle.

## The engineering log will not be cleaned up for appearances

Tutorials often compress messy discovery into a sequence of perfect commands.

That is useful when the goal is merely to teach a stable API. It is misleading when the subject is architecture.

For experiments that influence a decision, we will use a consistent evidence format:

1. **Expected** — what we believed would happen.
2. **Tried** — the exact command, configuration, prompt, or implementation.
3. **Observed** — what actually happened.
4. **Learned** — what that says about our model.
5. **Changed** — what we altered because of it.
6. **Verified** — how we proved the correction.

That includes AI-assisted work.

If a prompt produces a bad migration, we should not only show the corrected prompt. The failed prompt can reveal missing repository context, an ambiguous contract, or a validation gap. Those are engineering findings.

The important constraint is that failure logging must remain useful rather than theatrical. We are not going to intentionally fumble commands for drama. We will preserve failures that genuinely occur and matter to the design.

## Repository commands will eventually become a public API

One design principle deserves attention this early because it influences nearly every later arc.

The root developer experience is an interface.

Humans will use it. CI will use it. IDE tasks may use it. Documentation will refer to it. Agents will discover and invoke it. Generators may print it as a next step.

That means we should treat commands such as “check the repository,” “test what changed,” “generate a package,” “diagnose my environment,” and “prepare a release” the way we would treat a small public API:

- stable names;
- clear semantics;
- useful errors;
- documented inputs;
- minimal surprising side effects;
- implementation freedom behind the interface.

We should not require every developer to memorize which underlying tool owns each internal operation in order to do common work. But we also should not hide the underlying architecture so aggressively that failures become impossible to debug.

That balance—stable interface, inspectable internals—will recur throughout the series.

## How the series will be organized

The current roadmap contains twelve arcs:

1. **First Principles** — requirements, mental models, authority boundaries, reversible decisions.
2. **Repository Kernel** — root contract, directory taxonomy, manifests, bootstrap interface.
3. **Reproducible Development Environment** — mise, environment boundaries, containers/Nix positioning, diagnostics.
4. **Workspace and Dependency System** — Bun, TypeScript, internal packages, dependency governance.
5. **Execution System** — Moon, projects, tasks, affected execution, caching, root command UX.
6. **Generation and Evolution** — Nx generators, deterministic scaffolding, generator tests, codemods, migrations.
7. **Engineering Quality System** — Biome, type checking, Vitest, Playwright, Knip, Lefthook, layered validation.
8. **Delivery System** — GitHub Actions, CI caching/economics, security, Renovate, Changesets, releases.
9. **Polyglot Expansion** — Rust, Python, cross-language relationships, polyglot CI.
10. **AI-Native Engineering** — repository instructions, machine-readable context, prompt logs, deterministic verification.
11. **Proving the Starter** — fresh-clone testing, failure injection, performance measurement, onboarding, self-validation.
12. **From Repository to Product** — scaffolding, versioned starter contracts, upgrades, release/maintenance.

The current plan reaches Article 60.

That number is not a badge either. If implementation shows two articles belong together, we will combine them. If a hidden problem deserves its own experiment, we will split one. The roadmap is allowed to learn.

## Article 0’s actual repository increment

A conceptual article still needs a checkpoint.

For this installment we are adding three durable pieces of state plus an entry point:

```text
README
docs/
  architecture/
    definition-of-ultimate.md
  series-roadmap.md
articles/
  00-what-does-ultimate-mean.md
```

The root `README` explains what the repository is and points to the canonical documents.

`docs/architecture/definition-of-ultimate.md` is the working architecture contract. It contains the required properties, non-goals, responsibility map, falsifiable assumptions, evidence format, and completion criteria.

`docs/series-roadmap.md` is the evolving curriculum.

And this file is the first publishable article.

No build system has been selected *by configuration* yet. No package manager has been installed into the repo. No task runner is wired. No formatter is hiding an accidental architecture decision inside a generated file.

That absence is part of the checkpoint.

## Verification

Article 0 does not yet have a programmatic quality system to run. We would be lying if we called a Markdown file existing on a branch a “test suite.”

So the verification appropriate to this checkpoint is structural and reviewable.

From the Article 0 branch:

```bash
git switch series/00-first-principles

git diff --stat main...HEAD

git diff main...HEAD -- \
  README \
  docs/architecture/definition-of-ultimate.md \
  docs/series-roadmap.md \
  articles/00-what-does-ultimate-mean.md
```

We verify that:

- the series has a canonical repository entry point;
- “ultimate” has testable completion criteria rather than a marketing definition;
- non-goals constrain the design space;
- our initial tool choices are recorded as hypotheses with authority boundaries;
- assumptions include explicit evidence that would force reconsideration;
- the roadmap includes implementation, testing, CI, security, polyglot proof, AI-native engineering, self-validation, and upgrade machinery;
- the series has an explicit evidence format for failures and architectural changes.

Later, one of our goals is to make even repository-documentation contracts machine-verifiable where doing so adds value. Today, manual inspection is the honest level of verification available.

## What we have learned before writing configuration

Even this “pre-build” step has already constrained the architecture.

We know that we cannot evaluate tools in isolation because responsibility overlap matters as much as features.

We know that affected execution and caching must be treated as correctness systems, not only performance systems.

We know that project generation without migrations is incomplete if the starter is supposed to evolve.

We know that polyglot support must be proven through integration with the root operating model rather than demonstrated by the existence of a second file extension.

We know that AI-readiness should mostly emerge from explicit architecture, stable commands, discoverable context, and deterministic checks—not from a giant prompt file that tries to explain a confusing repository.

And we know that the final test is not whether *we* can use the starter after months of building it. The final test is whether a fresh human or agent context can reconstruct the intended model from the repository itself.

That is a much harder standard than “the demo works.”

Good.

## Next: build the mental model before the machinery

In Article 1, we will define the vocabulary and layers that the rest of the architecture depends on: repository, workspace, project, package, project graph, task graph, toolchain, cache, generator, migration, policy, and CI orchestration.

That may sound academic.

It is not.

Many monorepo problems begin when two tools use the same word to mean different things, or when we treat a package graph and a task graph as though they were interchangeable. If we cannot describe the system precisely, we will struggle to assign authority precisely.

Only after the model is explicit will we start turning our tool preferences into decisions.

For now, the important result is simple:

We have not built the monorepo yet.

We have built the standard by which we will decide whether the monorepo is any good.
