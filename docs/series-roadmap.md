# Series Roadmap

# Building the Ultimate Monorepo Starter from First Principles

*A tutorial, engineering log, architecture journal, and reproducible build from an empty directory to a production-grade polyglot workspace.*

This roadmap is a curriculum, not a promise that our first sequence survives contact with implementation. Article numbers and boundaries may change when experiments expose a better dependency order. When that happens, the engineering log should say why.

Every implementation article follows the same basic progression:

**question / requirement → alternatives → decision → implementation → tests → verification → checkpoint → lessons / open questions**

Where experimentation is involved, we additionally record:

**Expected → Tried → Observed → Learned → Changed → Verified**

---

## Arc I — First Principles

### Article 0 — What Does an “Ultimate Monorepo Starter” Actually Mean?
Define the product before selecting configuration: requirements, non-goals, architectural properties, falsifiable assumptions, reversible decisions, and the evidence standard for the series.

### Article 1 — The Monorepo Is a Platform, Not a Folder Full of Packages
Build the mental model: repositories, workspaces, projects, package graphs, task graphs, toolchains, policy, delivery systems, and why confusing these layers creates accidental architecture.

### Article 2 — Requirements Before Tools: Writing the Monorepo Architecture Contract
Turn goals into testable requirements and quality attributes. Separate requirements from preferences and create the first decision/evidence ledger.

### Article 3 — Choosing Authorities: Who Owns Packages, Tools, Tasks, Generation, and CI?
Compare overlapping responsibilities and establish the one-authority-per-concern model. Introduce the intended Bun/mise/Moon/Nx boundary and the conditions under which we would reverse it.

### Article 4 — Reversible Architecture: ADRs, Experiments, and Exit Criteria
Create a lightweight decision-record system and show how to document assumptions without turning provisional choices into dogma.

---

## Arc II — Repository Kernel

### Article 5 — Building the Smallest Useful Repository Contract
Establish root files, naming, source-of-truth conventions, repository metadata, documentation entry points, and the first machine-verifiable invariants.

### Article 6 — Designing a Directory Taxonomy That Can Survive Growth
Define `apps`, `packages`, `tools`, `docs`, fixtures/examples, and language-specific project placement without encoding a single application architecture.

### Article 7 — The Root Manifest as an Interface, Not a Junk Drawer
Introduce the workspace root package metadata and stable developer-facing commands while resisting script sprawl.

### Article 8 — Bootstrap as a Product Surface
Design the supported path from fresh clone to usable workspace, including prerequisite policy, failure messages, and idempotence expectations.

---

## Arc III — Reproducible Development Environment

### Article 9 — Pinning the Toolchain with mise
Make tool/runtime versions repository-declared and verify behavior from a fresh shell.

### Article 10 — What Reproducibility Actually Means
Separate version pinning, dependency locking, environment variables, OS dependencies, generated state, caches, and hermeticity. Define the reproducibility level we can honestly claim.

### Article 11 — Dev Containers, Native Bootstrap, and Nix: Choosing Boundaries Instead of Camps
Compare environment strategies around the actual repository requirements and document where each approach belongs.

### Article 12 — Building `doctor`: Make Hidden Prerequisites Observable
Create a diagnostic interface that checks tools, versions, environment assumptions, credentials/configuration presence, and common failure modes.

---

## Arc IV — Workspace and Dependency System

### Article 13 — Why Bun Owns the JavaScript Workspace
Compare Bun with pnpm, npm, and Yarn against our requirements; establish workspace configuration and prove basic internal linking.

### Article 14 — TypeScript as a Repository Contract
Create shared compiler foundations without a giant magical config. Define package-level responsibility, references/build modes where useful, and editor behavior.

### Article 15 — Designing Internal Packages: Source, Build, and Export Boundaries
Create representative internal libraries and settle package entry points, exports, build artifacts, and consumption patterns.

### Article 16 — Dependency Boundaries Are Architecture
Define allowed relationships among apps, libraries, tooling, and shared infrastructure. Add machine-enforceable rules.

### Article 17 — Dependency Version Policy with Syncpack
Decide where versions should align, where divergence is legitimate, and how policy failures should be presented.

---

## Arc V — Execution System

### Article 18 — Why Moon Owns the Task Graph
Compare Moon with Nx, Turborepo, and Bazel using the repository’s actual requirements instead of feature checklists.

### Article 19 — Modeling Projects and Tasks Without Configuration Copy-Paste
Introduce Moon project discovery, task definitions, inheritance, inputs/outputs, and environment semantics.

### Article 20 — Affected Execution: Stop Rebuilding the Universe
Connect source changes to project/task impact and verify correctness with deliberately chosen dependency scenarios.

### Article 21 — Caching: Performance Feature, Correctness Liability
Define cache keys, outputs, environment influence, invalidation, local cache behavior, and the evidence required before trusting cache hits.

### Article 22 — Designing the Root Command UX
Create stable repository commands that wrap the task graph without hiding it, and define how developers, CI, and agents should invoke work.

---

## Arc VI — Generation and Evolution

### Article 23 — Why Nx Is Here If Moon Runs the Tasks
Constrain Nx to generators, scaffolding, migrations, and codemods. Prove that routine build/test/lint execution remains independent of Nx.

### Article 24 — Our First Deterministic Project Generator
Generate a real workspace project with naming validation, files, dependencies, task metadata, tests, and documentation.

### Article 25 — Testing Generators Like Production Code
Add snapshot/structural tests, repeated-run behavior, invalid-input tests, and deterministic-output checks.

### Article 26 — Codemods and Migrations: The Starter Must Be Able to Change Its Mind
Introduce repository migrations and demonstrate an intentional contract change without manual sweeping edits.

### Article 27 — Generator UX Is Developer Experience
Improve prompts/arguments, errors, defaults, discoverability, and generated next steps while preserving automation-friendly noninteractive usage.

---

## Arc VII — Engineering Quality System

### Article 28 — Biome as the Formatting and Linting Authority
Compare Biome with ESLint + Prettier for the actual workspace and establish repository-wide and project-local behavior.

### Article 29 — Type Checking as a Graph-Aware Task
Integrate TypeScript validation with Moon inputs, dependencies, affected execution, and caching.

### Article 30 — Vitest: Unit and Integration Testing Without a Parallel Build System
Define test project conventions, shared defaults, isolation, coverage policy, and task-graph behavior.

### Article 31 — Playwright: End-to-End Tests as First-Class Projects
Model browser tests explicitly, handle runtime dependencies, and integrate them without making every developer run them constantly.

### Article 32 — Knip and the Cost of Dead Repository State
Detect unused files, dependencies, and exports while documenting legitimate exceptions and generated-code boundaries.

### Article 33 — Lefthook Without Local-Only Policy
Add Git hooks that invoke canonical repository checks and remain an optimization rather than the only enforcement point.

### Article 34 — One Quality Gate, Many Feedback Speeds
Assemble fast local checks, pre-push checks, full validation, and CI tiers from shared task definitions.

---

## Arc VIII — Delivery System

### Article 35 — GitHub Actions Should Orchestrate, Not Reimplement
Create CI that bootstraps the declared environment and delegates work to repository tasks.

### Article 36 — CI Affected Execution and Cache Economics
Measure what we save, what cache restoration costs, and when a supposedly clever optimization is slower than simply running the task.

### Article 37 — Secure the Repository, Not Just the Application
Integrate secret scanning and vulnerability/supply-chain checks, starting with Gitleaks and Trivy roles that do not overlap ambiguously.

### Article 38 — Renovate as an Automated Maintainer
Configure update grouping, schedules/policies, lockfile maintenance, automerge boundaries, and verification requirements.

### Article 39 — Changesets and the Meaning of a Version in a Monorepo
Define publishable vs private packages, version strategies, release intent, changelogs, and how changes move toward release.

### Article 40 — Building the Release Pipeline
Create a reproducible release workflow, permissions model, provenance/signing decisions where appropriate, and recovery procedures.

---

## Arc IX — Polyglot Expansion

### Article 41 — The Polyglot Test: Add Rust Without Redesigning the Root
Add a real Rust project and integrate its native toolchain into mise + Moon while keeping Cargo authoritative for Rust concerns.

### Article 42 — A Second Ecosystem: Add Python and Find the Hidden Assumptions
Introduce a Python project specifically to expose places where the architecture accidentally equated “project” with “package.json.”

### Article 43 — Cross-Language Task and Dependency Boundaries
Model generated artifacts, service/API contracts, integration tests, and task dependencies that cross ecosystem boundaries.

### Article 44 — Polyglot CI Without Matrix Explosion
Design CI around affected projects and capability needs rather than cloning an entire workflow per language.

---

## Arc X — AI-Native Engineering

### Article 45 — An AI-Ready Repository Is Mostly Good Architecture
Define what coding agents need: stable commands, explicit boundaries, local instructions, small source-of-truth documents, and deterministic verification.

### Article 46 — Writing Repository Instructions for Humans and Agents
Add agent-facing guidance, scoped instructions, prohibited shortcuts, verification expectations, and context-routing rules.

### Article 47 — Machine-Readable Repository Context
Expose project metadata, dependency/task relationships, ownership/intent, and useful introspection without generating another stale documentation universe.

### Article 48 — Prompt Logs, Failed Prompts, and Verification
Document where AI assistance helps, where it misleads, which prompts/context patterns work, and how every generated change is reduced to testable evidence.

### Article 49 — Deterministic Gates Around Nondeterministic Assistance
Build an AI-assisted workflow where agents can propose changes but formatting, types, tests, boundaries, generators, and repository invariants remain the authority.

---

## Arc XI — Proving the Starter

### Article 50 — Fresh-Clone Testing: Can the Repository Rebuild Its Own Assumptions?
Automate clean-environment bootstrap and prove what is and is not required outside the repository.

### Article 51 — Failure Injection: Break the Repository on Purpose
Test wrong tool versions, stale generated files, dependency-policy violations, broken cache outputs, leaked secrets, invalid generators, and CI misconfiguration.

### Article 52 — Measuring Developer Feedback and CI Cost
Collect task timings, cache behavior, affected-project counts, cold/warm runs, and CI observations. Replace performance vibes with evidence.

### Article 53 — The Onboarding Test
Give the repository to a fresh human/agent context and record where instructions, commands, diagnostics, or mental models fail.

### Article 54 — Repository Self-Validation
Create contract tests for the root architecture itself: required files, project metadata, command mappings, generator outputs, dependency rules, and documentation links.

---

## Arc XII — From Repository to Product

### Article 55 — Extracting the Starter Without Forking the Tutorial History
Separate canonical implementation, examples/fixtures, generated starter output, and educational history.

### Article 56 — Building the Scaffolder
Create a versioned CLI/generator experience that can instantiate the starter reproducibly and noninteractively.

### Article 57 — Starter Versions and Upgrade Channels
Define how generated repositories know what starter contract they came from and how migrations move them forward.

### Article 58 — Testing an Upgrade from One Starter Version to the Next
Create an older fixture, upgrade it through real migrations, and verify that user-owned code survives.

### Article 59 — Releasing and Maintaining the Starter
Package distribution, release automation, changelog strategy, compatibility policy, support windows, and dependency-update operations.

### Article 60 — Did We Earn “Ultimate”?
Revisit every criterion from Article 0 with evidence, rejected ideas, performance data, failure history, remaining compromises, and a list of conditions that should cause future maintainers to change the architecture.

---

## Editorial rules for every article

Each article should:

1. teach enough background that an intermediate engineer can follow the implementation;
2. preserve deeper architectural reasoning for experienced readers rather than hiding it behind “best practice” claims;
3. include every command required to reproduce the checkpoint;
4. show important configuration/code excerpts and link readers to the canonical full files;
5. distinguish facts, requirements, preferences, assumptions, and measurements;
6. compare alternatives only when the decision is actually relevant;
7. record failed experiments when they influence the design;
8. include an explicit verification section;
9. identify the repository checkpoint/PR or commit associated with the installment;
10. state what remains unresolved rather than manufacturing certainty;
11. document meaningful AI assistance, including prompts/context and verification, when AI materially influenced the implementation;
12. leave the repository in a coherent state that the next installment can start from.

## Current status

- [x] Article 0 scope and architecture contract
- [x] Article 1 repository-platform system model
- [x] Article 2 living product requirements/evidence ledger
- [ ] Arc I implementation complete
- [ ] Repository kernel complete
- [ ] Reproducible environment complete
- [ ] Workspace/dependency system complete
- [ ] Execution system complete
- [ ] Generation/evolution system complete
- [ ] Quality system complete
- [ ] Delivery system complete
- [ ] Polyglot proof complete
- [ ] AI-native engineering layer complete
- [ ] Starter proof suite complete
- [ ] Starter productization complete

The unchecked boxes are intentional. This repository is the log of proving them.
