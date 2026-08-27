# Definition of “Ultimate”

Status: **Working architecture contract**

This document defines what the series means by an “ultimate monorepo starter.” It is intentionally testable. We do not earn the word *ultimate* by accumulating tools; we earn it by demonstrating useful engineering properties under realistic constraints.

## The product we are actually building

The repository is a **developer platform in miniature**. It should provide a predictable operating model for projects, dependencies, tasks, generation, validation, delivery, security, and evolution while remaining understandable enough that a new engineer—or an AI coding agent—can discover how it works without tribal knowledge.

The implementation is TypeScript-first, but the architecture must not make JavaScript the only first-class ecosystem.

## Required properties

### 1. Reproducible

A fresh clone should be able to reconstruct the supported development environment from repository-declared state. Tool versions, package-manager behavior, workspace dependencies, generated artifacts, and CI behavior must not rely on undocumented machine state.

### 2. Explicit

Important repository behavior should have a discoverable source of truth. We prefer visible contracts over convention that exists only in maintainers’ heads.

### 3. Deterministic where determinism is possible

The same generator inputs should produce the same repository shape. The same lockfile and toolchain inputs should resolve consistently. Validation should be machine-checkable rather than dependent on memory.

### 4. Fast enough to preserve feedback loops

The repository must understand its project and task graph well enough to avoid treating every change as a full-repository rebuild. Local work and CI should exploit affected execution and caching where correctness permits.

### 5. Locally and remotely consistent

CI should exercise the same repository-level commands and task definitions that developers use locally. CI may orchestrate them differently, but it should not become a second implementation of the build system.

### 6. Governed without becoming hostile

Dependency policy, formatting, linting, type safety, dead-code detection, testing, security checks, and release rules should be enforceable automatically. Guardrails must explain failures and make the correct path easier than bypassing them.

### 7. Polyglot-ready

Adding a non-JavaScript project must not require replacing the repository’s orchestration model, directory contract, CI philosophy, or developer interface. Later in the series we will prove this with at least one non-JavaScript ecosystem.

### 8. Evolvable

The starter must support migrations, codemods, generator changes, dependency upgrades, and policy changes without assuming consumers will recreate their repositories from scratch.

### 9. Observable

We should be able to inspect project relationships, task relationships, cache behavior, CI cost drivers, dependency state, and repository health. Performance claims require measurements.

### 10. Secure by default

Secrets detection, dependency hygiene, software-supply-chain checks, container scanning where relevant, least-privilege CI, and controlled automation should be part of the platform rather than an appendix.

### 11. AI-legible

Repository structure, commands, architectural boundaries, and verification procedures should be discoverable by AI-assisted engineering tools without granting generated output special authority. Nondeterministic assistance must terminate in deterministic checks.

### 12. Understandable

A system that only its creator can operate is not an ultimate starter. Documentation, naming, project metadata, error messages, and root-level interfaces are architecture.

## Non-goals

We are deliberately **not** trying to:

- select every possible tool or support every language on day one;
- hide every ecosystem behind a universal abstraction;
- replace ecosystem-native package managers, compilers, or test runners when they are the right local authority;
- optimize for benchmark theater at the expense of debuggability;
- require paid remote infrastructure for the repository to function;
- encode an application architecture that every adopter must use;
- make all decisions permanent;
- claim portability we have not tested;
- treat AI-generated changes as trustworthy without verification;
- create a “one command” experience by hiding mutable global state.

## Architectural principles

### One authority per concern

We will avoid overlapping tools that compete to own the same responsibility. Multiple tools may participate in a workflow, but their boundaries must be explicit.

Our initial intended responsibility map is:

- **Bun** — JavaScript/TypeScript package and workspace operations.
- **mise** — developer tool/runtime version management and environment entry point.
- **Moon** — project/task graph and execution orchestration.
- **Nx** — generators, scaffolding, migrations, and codemod-style evolution; not the task runner.
- **Biome** — formatting and linting for supported source types.
- **Vitest** — primary unit/integration testing for TypeScript packages where appropriate.
- **Playwright** — browser/end-to-end testing where appropriate.
- **Syncpack** — workspace dependency-version policy.
- **Knip** — unused files, dependencies, and exports detection.
- **Lefthook** — Git hook orchestration.
- **GitHub Actions** — hosted CI orchestration.
- **Renovate** — dependency update automation.
- **Changesets** — package change intent and release/version workflow.
- **Gitleaks / Trivy** — security and supply-chain scanning roles to be defined precisely when implemented.

This map is a hypothesis, not scripture. The series must revisit a choice if implementation evidence shows overlapping authority, poor ecosystem fit, unacceptable performance, or maintenance cost.

### Repository commands are a public API

Root-level developer commands should be stable, memorable, documented, and usable by humans, CI, and agents. Internals may change without forcing every consumer to relearn the repository.

### Generated structure is a contract

Generators are production code. We will test them for determinism, expected files, dependency declarations, project registration, and policy compliance.

### CI is a deployment environment for repository policy

CI should consume the repository’s task graph and validation rules, not reimplement them as YAML-only logic.

### Prefer reversible decisions early

Early architecture should preserve options while evidence is weak. We will distinguish between cheap-to-change configuration choices and expensive structural commitments.

## Falsifiable assumptions

We will explicitly try to disprove the following assumptions during the series.

| Assumption | How we will test it | Evidence that forces reconsideration |
| --- | --- | --- |
| Bun can be the JS/TS workspace authority | install, link, publish, script and real dependency scenarios across multiple packages | ecosystem incompatibility, unreliable workspace behavior, or required workarounds that become structural |
| Moon can be the repository task authority | TypeScript projects first, then non-JS projects, affected execution and caching | inability to model required polyglot tasks or unacceptable duplication/complexity |
| Nx can be constrained to generation/migrations | generators produce fully registered Moon/Bun projects without Nx owning execution | Nx execution becomes an undeclared dependency of normal workflows |
| mise can be the supported toolchain entry point | fresh-environment onboarding and CI/bootstrap experiments | inconsistent tool resolution, unsupported environments, or hidden prerequisites that defeat reproducibility |
| Local and CI workflows can share task definitions | CI invokes repository commands and Moon tasks rather than bespoke equivalents | material CI requirements that cannot be represented safely in repository tasks |
| The repo can be polyglot without redesign | add at least one non-JS project and integrate build/test/lint/cache behavior | root architecture changes that reveal JS-only assumptions |
| AI-readiness can be architectural rather than prompt theater | give an agent repository-local instructions and machine-readable context, then evaluate changes through normal checks | success depends on undocumented conversational context or privileged manual correction |
| The starter can be upgradeable | migrate a repository created from an earlier starter version to a later contract | upgrades require recreation or unbounded manual edits |

## Evidence format for engineering-log entries

When experimentation matters, articles should record the following rather than smoothing the history into a retrospective narrative:

1. **Expected** — what we believed would happen and why.
2. **Tried** — the exact command, configuration, prompt, or implementation.
3. **Observed** — the actual output, failure, timing, diff, or behavior.
4. **Learned** — what the observation says about our model.
5. **Changed** — the resulting architecture or implementation change.
6. **Verified** — how we proved the correction worked.

Failures are evidence, not editorial defects.

## Completion criteria for the overall starter

The series is not complete until we can demonstrate, at minimum:

- a documented fresh-clone bootstrap path;
- pinned and inspectable toolchain state;
- a TypeScript workspace with internal packages and enforceable dependency rules;
- a repository task graph with affected execution and caching;
- deterministic project generators and migration mechanisms;
- formatting, linting, type checking, unit/integration testing, end-to-end testing where appropriate, dependency hygiene, and dead-code checks;
- developer hooks that reuse repository validation rather than inventing separate policy;
- GitHub Actions workflows that reuse local task definitions;
- dependency-update automation and a release/versioning workflow;
- security checks appropriate to source, dependencies, CI, and containers;
- a containerized/dev-container development story and an explicit position on Nix;
- at least one non-JavaScript project integrated without changing the core architecture;
- machine-readable repository/project context and explicit AI-agent guidance;
- fresh-clone, generator, migration, failure-injection, and onboarding tests;
- measured performance and CI-economics observations;
- a reusable starter distribution mechanism;
- an upgrade path from one starter version to another.

## The standard for changing this contract

This document may change. In fact, we expect it to.

But when an important property, boundary, or success criterion changes, the engineering log should explain what evidence caused the change. The goal is not to preserve our first opinion. The goal is to preserve the reasoning trail that gets us to a better system.
