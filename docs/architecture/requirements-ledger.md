# Monorepo Starter Requirements Ledger

Status: **Working product contract — v0.1**

This document is the living requirements contract for the starter we are building in **Building the Ultimate Monorepo Starter from First Principles**.

It is intentionally separate from `docs/requirements/NNN-*.md`:

- numbered requirement files record what a particular historical engineering increment intended to make true;
- this ledger records what the starter **currently** must prove as a product.

Historical increment requirements should remain faithful to what was known at the time. This ledger is allowed to evolve when evidence changes the current contract.

## Normative language

When the uppercase terms **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** appear in requirement statements, this ledger adopts the requirement-level meanings defined by BCP 14 (RFC 2119 as updated by RFC 8174).

That convention is used for precision, not as a claim that this repository is an IETF specification.

References:

- https://www.rfc-editor.org/info/rfc2119/
- https://www.rfc-editor.org/info/rfc8174/

Lowercase uses of words such as “must” or “should” in explanatory prose keep their ordinary English meanings.

## Requirement states

A product requirement moves through evidence states rather than simply “done/not done.”

| State | Meaning |
| --- | --- |
| **Accepted** | Part of the current product contract, but not yet demonstrated completely. |
| **Partial** | Some acceptance criteria have evidence, but the complete requirement is not yet proved. |
| **Verified** | All current acceptance criteria have durable evidence. |
| **Superseded** | Replaced by a newer requirement or contract after an explicitly recorded change. |
| **Rejected** | Considered but deliberately removed from the product contract with recorded rationale. |

A requirement can have implementation without being verified. “The config exists” is not proof that the promised behavior works.

## Requirement classes

The ledger contains three kinds of normative concern:

- **Capability** — something the starter must enable or do.
- **Quality attribute** — a property of how the starter behaves, such as reproducibility or maintainability.
- **Evidence/process constraint** — a rule needed to make architectural claims reproducible and auditable.

These are all requirements when they constrain whether we consider the starter successful. They are not all runtime features.

## What does not belong here

A current implementation preference is not automatically a product requirement.

These are **not** requirements merely because we currently intend to use them:

- Bun;
- mise;
- Moon;
- Nx;
- Biome;
- Vitest;
- Playwright;
- GitHub Actions.

Those products are candidate implementations. Later ADRs must explain why a candidate satisfies the requirements better than alternatives for the concern it is authorized to own.

If we can replace a tool without changing the product promise, the tool name does not belong in the requirement statement.

## Traceability model

The intended evidence chain is:

```text
product requirement
        │
        ├── acceptance criteria
        │
        ├── implementation / configuration
        │
        ├── ADR or experiment when a consequential choice is involved
        │
        ├── executable check / benchmark / inspection
        │
        └── verification record + durable evidence
```

Later articles should cite requirement IDs directly.

Example:

```text
UMS-EXE-003
  ↓
ADR: choose affected-execution authority
  ↓
Moon configuration / project graph
  ↓
affected-change fixture tests
  ↓
verification/020-affected-execution.md
```

The concrete implementation can change while the requirement ID remains stable.

---

# 1. Environment and bootstrap

## UMS-ENV-001 — Repository-declared supported toolchain

**Class:** Quality attribute / capability  
**State:** Accepted

The repository **MUST** declare the tool/runtime versions required for supported development and CI workflows closely enough that a fresh environment does not depend on undocumented global version choices.

Acceptance criteria:

- supported runtime/tool versions are discoverable from version-controlled repository state;
- setup documentation does not require an undocumented “use whatever version you already have” step for required tools;
- CI resolves supported versions from the same repository contract or an explicitly derived representation.

Planned evidence:

- toolchain configuration;
- fresh-environment bootstrap verification;
- CI bootstrap inspection.

## UMS-ENV-002 — Fresh-clone bootstrap

**Class:** Capability  
**State:** Accepted

A supported fresh clone **MUST** have a documented path to a usable development workspace without undocumented manual repair steps.

Acceptance criteria:

- the bootstrap procedure starts from a documented prerequisite boundary;
- required dependencies/tooling can be installed or resolved through documented commands;
- rerunning bootstrap is safe or its non-idempotent behavior is explicitly documented and tested;
- the repository can verify that bootstrap reached a usable state.

Planned evidence:

- clean-environment test;
- bootstrap logs;
- `doctor`/verification checks.

## UMS-ENV-003 — Environment diagnostics

**Class:** Capability / understandability  
**State:** Accepted

The repository **MUST** provide a supported diagnostic path for detecting important environment mismatches and hidden prerequisites.

Acceptance criteria:

- required tools/versions can be checked;
- common missing prerequisites produce actionable diagnostics;
- checks distinguish environment failure from project/test failure where practical.

Planned evidence:

- `doctor` implementation;
- negative-path tests.

---

# 2. Workspace, project, and dependency model

## UMS-WSP-001 — Project is not synonymous with package

**Class:** Architecture capability  
**State:** Partial

The repository model **MUST** permit first-class projects that are not JavaScript packages and **MUST NOT** require `package.json` as the universal definition of project identity.

Acceptance criteria:

- the architecture vocabulary defines project independently from ecosystem package metadata;
- at least one first-class non-JavaScript project participates in repository orchestration later in the series;
- project discovery/metadata supports non-package projects where required.

Current evidence:

- `docs/architecture/system-model.md` establishes the conceptual distinction.

Remaining evidence:

- Rust and Python integration increments.

## UMS-WSP-002 — JavaScript workspace integrity

**Class:** Capability  
**State:** Accepted

The JavaScript/TypeScript workspace authority **MUST** support internal workspace dependencies, reproducible dependency installation, and the package relationships required by representative applications/libraries.

Acceptance criteria:

- internal packages can depend on each other through supported workspace semantics;
- clean installation is lockfile-controlled;
- package dependency state remains inspectable and enforceable;
- publishable and private-package scenarios required by the starter are demonstrated before release claims are made.

Planned evidence:

- package/workspace experiments;
- clean install tests;
- release fixtures.

## UMS-WSP-003 — Enforceable dependency boundaries

**Class:** Governance / architecture  
**State:** Accepted

Architectural dependency rules **MUST** be machine-enforceable where the repository claims they are policy.

Acceptance criteria:

- allowed/forbidden relationships are documented;
- violating fixtures or tests fail deterministically;
- policy failures identify the violated relationship and remediation path where practical.

Planned evidence:

- dependency-boundary configuration;
- negative fixture tests.

## UMS-WSP-004 — Dependency version policy

**Class:** Governance  
**State:** Accepted

The repository **MUST** have an explicit policy for dependency-version alignment and legitimate divergence across workspace projects.

Acceptance criteria:

- dependency ranges that must align are enforceable;
- intentional divergence can be represented without disabling the entire rule set;
- violations are detectable locally and in CI through canonical repository checks.

Planned evidence:

- version-policy configuration;
- violation fixtures.

---

# 3. Execution, affected analysis, and caching

## UMS-EXE-001 — One authoritative repository task graph

**Class:** Architecture constraint  
**State:** Accepted

Routine repository build/test/lint/typecheck orchestration **MUST** have one authoritative task graph.

Other tools **MAY** execute local helper operations, but they **MUST NOT** become competing undeclared sources of truth for normal repository task dependencies.

Acceptance criteria:

- the authority for project/task relationships is documented;
- root commands delegate to it rather than recreate task ordering independently;
- CI uses the same task definitions for equivalent work;
- generator tooling does not silently introduce a second required execution graph.

Planned evidence:

- authority ADR;
- task configuration;
- command/CI inspection tests.

## UMS-EXE-002 — Explicit task inputs and outputs

**Class:** Correctness / observability  
**State:** Accepted

Cacheable or affected-aware tasks **MUST** describe relevant inputs, outputs, environment influences, and dependencies sufficiently for the repository to reason about correctness.

Acceptance criteria:

- task outputs needed for cache restoration are declared;
- material environment/config inputs are accounted for;
- task dependency order is explicit or reliably inferred from documented project relationships.

Planned evidence:

- task definitions;
- cache invalidation tests.

## UMS-EXE-003 — Correct affected execution

**Class:** Performance efficiency / correctness  
**State:** Accepted

The repository **MUST** be able to limit appropriate validation/build work to affected projects or targets without omitting work whose correct result could have changed.

Acceptance criteria:

- direct project changes affect the expected project;
- dependency changes propagate to dependent work where required;
- repository/global configuration changes invalidate the appropriate scope;
- deliberately constructed change scenarios compare expected and actual affected sets.

Planned evidence:

- graph fixtures;
- affected-set tests;
- CI observations.

## UMS-EXE-004 — Cache hits are correctness claims

**Class:** Performance efficiency / correctness  
**State:** Accepted

A reused cached result **MUST** only be considered valid when all declared influences capable of changing that result are equivalent under the cache model.

Acceptance criteria:

- changing a relevant source/config/environment input causes a cache miss or otherwise recomputes correctly;
- unchanged equivalent inputs can produce a cache hit;
- outputs are restored completely enough for downstream tasks;
- cache behavior can be inspected when debugging incorrect reuse.

Planned evidence:

- cold/warm runs;
- cache invalidation fixtures;
- output restoration checks.

---

# 4. Generation, migration, and evolution

## UMS-GEN-001 — Deterministic project generation

**Class:** Capability / determinism  
**State:** Accepted

Given equivalent explicit inputs and repository contract state, supported project generators **MUST** produce equivalent intended repository structure.

Acceptance criteria:

- generated files/metadata/dependencies are testable;
- invalid inputs fail with actionable errors;
- automation-friendly noninteractive invocation is supported;
- repeated generation behavior is defined and tested.

Planned evidence:

- generator tests;
- snapshot/structural assertions;
- invalid-input tests.

## UMS-GEN-002 — Existing repositories can evolve

**Class:** Maintainability / flexibility  
**State:** Accepted

The starter **MUST** provide a versioned migration path for supported contract changes that affect repositories already created from an earlier starter version.

Acceptance criteria:

- starter contract/version origin is representable;
- migrations can transform an older fixture forward;
- user-owned code outside the documented migration scope is preserved;
- migration failure/recovery behavior is documented.

Planned evidence:

- migration fixtures;
- old-version → new-version upgrade test.

## UMS-GEN-003 — Consequential architecture changes are traceable

**Class:** Evidence/process constraint  
**State:** Partial

When a consequential accepted architecture decision changes, the repository **MUST** preserve why it changed instead of silently rewriting the old rationale.

Acceptance criteria:

- superseding decisions reference prior decisions where applicable;
- the engineering journal records evidence that caused the change;
- current-state architecture documents are updated separately from historical records.

Current evidence:

- engineering protocol and early journals establish the preservation model.

Remaining evidence:

- first real superseded ADR/migration.

---

# 5. Quality and developer feedback

## UMS-QLT-001 — Canonical repository verification

**Class:** Governance / capability  
**State:** Accepted

The repository **MUST** expose a canonical verification interface that composes the checks required for repository correctness at the relevant scope.

Acceptance criteria:

- formatting/linting/type/test/dependency/repository checks can be invoked through documented repository commands as they are introduced;
- local and CI invocation reuse the same underlying checks;
- the canonical interface exits non-zero when a required check fails.

Planned evidence:

- root command contract;
- CI parity inspection;
- failure fixtures.

## UMS-QLT-002 — Layered feedback speeds

**Class:** Performance efficiency / developer experience  
**State:** Accepted

The repository **SHOULD** provide fast feedback paths for common local work without weakening the authoritative full validation path.

Acceptance criteria:

- fast checks and full checks have explicit scope differences;
- hook-time optimizations do not become the only enforcement location;
- measured timings inform which checks run at each feedback tier.

Planned evidence:

- task timings;
- hook/CI configuration.

## UMS-QLT-003 — Actionable policy failures

**Class:** Interaction capability / understandability  
**State:** Accepted

Repository policy failures **SHOULD** identify what contract was violated and provide enough context to discover the supported correction path.

Acceptance criteria:

- representative dependency/version/environment/generator failures are reviewed for actionable output;
- wrappers do not erase useful underlying diagnostics without replacing them with better context.

Planned evidence:

- negative-path fixtures;
- onboarding observations.

---

# 6. Delivery, CI, and releases

## UMS-DEL-001 — CI orchestrates repository policy

**Class:** Architecture constraint / portability  
**State:** Accepted

Hosted CI **MUST** orchestrate canonical repository commands/task definitions rather than reimplement equivalent build/test policy only in workflow YAML.

Acceptance criteria:

- equivalent local and CI checks resolve to shared repository definitions;
- CI-only behavior is limited to genuinely hosted concerns such as event routing, credentials, matrix/environment provisioning, artifact publication, or deployment permissions;
- divergence is documented when unavoidable.

Planned evidence:

- workflow inspection;
- local-vs-CI command mapping.

## UMS-DEL-002 — Automated dependency maintenance is gated

**Class:** Maintainability / security  
**State:** Accepted

Automated dependency updates **MUST** pass the repository’s applicable verification requirements before privileged automerge/release behavior is allowed.

Acceptance criteria:

- update grouping/scheduling policy is explicit;
- automerge boundaries are explicit;
- lockfile maintenance is reproducible;
- required checks gate automated changes.

Planned evidence:

- dependency-bot configuration;
- protected automation workflow.

## UMS-DEL-003 — Release intent is explicit

**Class:** Capability / maintainability  
**State:** Accepted

The repository **MUST** distinguish publishable/versioned artifacts from private projects and represent release intent explicitly.

Acceptance criteria:

- publishable vs private package policy is documented;
- version/change intent can be recorded before release;
- release automation derives from repository state rather than ad-hoc manual version edits.

Planned evidence:

- Changesets/release configuration;
- release fixture.

---

# 7. Polyglot architecture

## UMS-PLY-001 — A non-JavaScript project is first-class

**Class:** Compatibility / flexibility  
**State:** Accepted

Adding a supported non-JavaScript project **MUST NOT** require replacing the repository’s core orchestration philosophy, root command model, or engineering-record structure.

Acceptance criteria:

- the project participates in toolchain setup, tasks, affected analysis, CI, and quality gates appropriate to its ecosystem;
- the ecosystem-native package/build authority remains usable rather than being hidden behind a fake JavaScript abstraction;
- root-level architecture changes, if any, are recorded and assessed against the original polyglot claim.

Planned evidence:

- Rust integration increment.

## UMS-PLY-002 — A second ecosystem tests hidden assumptions

**Class:** Compatibility / architecture validation  
**State:** Accepted

Before declaring the starter broadly polyglot-ready, the series **MUST** integrate a second non-JavaScript ecosystem chosen to expose assumptions that survived the first polyglot integration.

Acceptance criteria:

- the second ecosystem participates as a first-class project;
- any JavaScript-specific project identity/config assumptions are documented;
- required corrections preserve the tool-independent system model where possible.

Planned evidence:

- Python integration increment.

---

# 8. Security

## UMS-SEC-001 — Secret detection

**Class:** Security  
**State:** Accepted

The repository **MUST** automatically detect representative committed-secret patterns through a documented security check.

Acceptance criteria:

- the scanner can be invoked locally/CI through the repository validation model;
- a deliberately injected test secret or safe fixture demonstrates failure behavior;
- exceptions, if permitted, are explicit and reviewable.

Planned evidence:

- scanner configuration;
- safe failure-injection fixture.

## UMS-SEC-002 — Dependency and artifact vulnerability visibility

**Class:** Security  
**State:** Accepted

The repository **MUST** provide automated visibility into relevant dependency/supply-chain vulnerabilities and container/image findings where those artifact types exist.

Acceptance criteria:

- scan scope and ownership are documented;
- overlapping scanners have explicit roles;
- severity/failure policy is explicit rather than inferred from defaults.

Planned evidence:

- dependency/container scan configuration;
- policy fixtures or documented sample findings.

## UMS-SEC-003 — Least-privilege automation

**Class:** Security  
**State:** Accepted

CI/release/bot workflows **MUST** use the minimum practical permissions needed for their documented operations.

Acceptance criteria:

- workflow permissions are explicit where the platform supports them;
- write-capable workflows are separated from untrusted validation paths where appropriate;
- privileged automation boundaries are documented.

Planned evidence:

- workflow permission review;
- release architecture documentation.

---

# 9. Observability and measurement

## UMS-OBS-001 — Repository graph/state introspection

**Class:** Observability / understandability  
**State:** Accepted

Developers and automation **MUST** have a supported way to inspect project relationships and task behavior relevant to debugging affected execution and repository policy.

Acceptance criteria:

- project relationships are inspectable;
- task dependencies/inputs/outputs are inspectable at a useful level;
- cache or affected behavior has a diagnostic path.

Planned evidence:

- orchestrator introspection commands;
- troubleshooting documentation.

## UMS-OBS-002 — Performance claims use measurements

**Class:** Performance efficiency / evidence constraint  
**State:** Accepted

Claims that caching, affected execution, parallelism, or CI optimization improve feedback/cost **MUST** be supported by measurements from representative repository workloads.

Acceptance criteria:

- cold and warm behavior is distinguished where relevant;
- measurement environment/context is preserved;
- optimizations that cost more than they save are allowed to be rejected.

Planned evidence:

- timing artifacts;
- CI observations;
- performance article.

---

# 10. AI-assisted engineering

## UMS-AI-001 — Repository-local agent guidance

**Class:** Understandability / maintainability  
**State:** Accepted

Important repository constraints needed by coding agents **MUST** be discoverable from repository-local, version-controlled context rather than depending on private conversational history.

Acceptance criteria:

- agent instructions identify canonical commands and important boundaries;
- scoped guidance can be discovered near specialized projects where needed;
- instructions do not grant AI-generated output authority over deterministic checks.

Planned evidence:

- agent instruction files;
- onboarding experiment.

## UMS-AI-002 — Machine-readable repository context

**Class:** Observability / AI legibility  
**State:** Accepted

The repository **SHOULD** expose useful machine-readable project/context information without creating a second stale source of truth.

Acceptance criteria:

- generated/introspected context derives from authoritative project/task/dependency state where possible;
- consumers can identify project purpose/boundaries and canonical validation paths;
- freshness can be checked or regeneration is deterministic.

Planned evidence:

- repository introspection output;
- agent-context experiment.

## UMS-AI-003 — Deterministic gates around nondeterministic assistance

**Class:** Governance / correctness  
**State:** Accepted

AI-assisted changes **MUST** pass the same applicable deterministic repository validation as human-authored changes.

Acceptance criteria:

- no AI-specific bypass exists for formatting/types/tests/boundaries/security/repository invariants;
- failed prompts or generated changes that reveal missing context are recorded when they influence architecture;
- agent success is evaluated through repository evidence rather than model self-assessment.

Planned evidence:

- prompt logs;
- normal quality gates;
- agent onboarding experiment.

---

# 11. Starter productization and upgrades

## UMS-PRD-001 — Reproducible starter creation

**Class:** Product capability  
**State:** Accepted

The finished starter **MUST** be instantiable reproducibly through a documented, automation-friendly distribution/scaffolding mechanism.

Acceptance criteria:

- noninteractive creation is supported for automation/testing;
- generated repository state identifies the starter contract/version it came from;
- fresh generated output passes its initial canonical verification.

Planned evidence:

- scaffolder tests;
- generated fixture.

## UMS-PRD-002 — Upgrade path across starter versions

**Class:** Maintainability / product lifecycle  
**State:** Accepted

A repository created by an older supported starter version **MUST** have a documented migration path to a newer supported contract without requiring recreation from scratch.

Acceptance criteria:

- an older generated fixture is preserved;
- upgrade/migration is executed through supported tooling;
- user-owned code survives according to the migration contract;
- verification passes after upgrade.

Planned evidence:

- cross-version upgrade test.

---

# 12. Developer interface and evidence integrity

## UMS-DX-001 — Stable root command interface

**Class:** Interaction capability / maintainability  
**State:** Accepted

Common repository operations **MUST** have documented, stable root-level interfaces suitable for humans, CI, and agents.

Acceptance criteria:

- bootstrap/check/test/build/generate/doctor-style operations have canonical entry points as they are introduced;
- implementation authorities can change behind the interface without requiring every consumer to learn internal invocation details;
- debugging documentation still exposes the underlying authority when needed.

Planned evidence:

- root command contract;
- CI/agent usage.

## UMS-DX-002 — Fresh-context onboarding is testable

**Class:** Interaction capability / understandability  
**State:** Accepted

Before claiming the starter is understandable, the repository **MUST** be exercised by a fresh human or agent context that did not participate in its construction.

Acceptance criteria:

- the fresh context attempts bootstrap and representative development tasks using repository-local instructions;
- confusion/failures are recorded as evidence;
- important onboarding gaps are either corrected or explicitly accepted as limitations.

Planned evidence:

- onboarding test record.

## UMS-EVD-001 — Article/checkpoint identity is immutable evidence

**Class:** Evidence/process constraint  
**State:** Partial

A published/accepted article checkpoint **MUST** resolve to immutable Git evidence rather than relying only on a mutable branch name.

Acceptance criteria:

- the accepted state is identifiable by commit SHA and, where the repository workflow supports it, a stable article tag;
- mutable article branches are treated as workflow pointers, not canonical historical identities;
- later work cannot silently redefine the earlier checkpoint without a new commit/reference.

Current evidence:

- the Article 0/1 stacked-branch merge demonstrated why branch identity is insufficient;
- PRs and commits preserve the actual sequence.

Remaining evidence:

- normalized checkpoint/tag process in a later repository-contract increment.

## UMS-EVD-002 — Requirement-to-evidence traceability

**Class:** Evidence/process constraint  
**State:** Partial

Consequential product claims **MUST** be traceable from requirement to verification evidence as the relevant implementation is introduced.

Acceptance criteria:

- requirements have stable IDs;
- verification records cite the IDs they prove;
- implementation/ADR/benchmark references can be connected to the relevant requirement without relying on oral history;
- superseded requirements preserve why the contract changed.

Current evidence:

- this ledger establishes stable product requirement IDs;
- the engineering record already provides increment requirements and verification documents.

Remaining evidence:

- later implementation increments must exercise the links consistently.

---

# Completeness audit

The first ledger was checked against two sources of scope:

1. the twelve product properties in `definition-of-ultimate.md`;
2. the planned twelve series arcs in `docs/series-roadmap.md`.

ISO/IEC 25010:2023 is additionally used as a reference model for asking whether quality concerns such as performance, compatibility, interaction capability, reliability, security, maintainability, flexibility, and safety-like operational concerns have been considered. We are **not** claiming ISO/IEC 25010 conformance.

The standard’s public abstract explicitly describes the model as useful for requirements definition, checking comprehensiveness, identifying testing objectives, acceptance criteria, and product-quality measures. That is the role it plays here: a prompt for completeness, not outsourced architecture.

Reference:

- https://www.iso.org/standard/78176.html

## Known gaps in v0.1

This ledger is intentionally not frozen.

Known gaps include:

- exact supported operating-system boundaries;
- quantitative local/CI latency targets;
- reliability expectations for long-running services, because the starter does not yet contain a representative service;
- detailed artifact provenance/signing requirements;
- disaster/recovery semantics for release automation;
- accessibility requirements for any future developer-facing web UI, if one is introduced;
- explicit licensing/compliance requirements beyond the repository’s current scope.

Those gaps are better recorded than filled with fabricated numbers or generic boilerplate.

## Change rule

Changing a requirement statement or acceptance criterion is allowed.

A change becomes consequential when it materially alters what the finished starter promises, what evidence is required, or what implementation choices remain acceptable. Consequential changes **MUST** leave an engineering-record trail explaining why the current contract changed.

The goal is not to preserve v0.1 forever.

The goal is to make it impossible to change the definition of success silently.
