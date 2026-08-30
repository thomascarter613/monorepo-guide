# Verification — 004 Reversible Architecture

Status: **PASS — Article 4 increment requirements**

Verification date: **2026-08-30**

Starting commit:

`553f457b01d554429f7117f1e2ca824386835e37`

Working branch:

`series/04-reversible-architecture`

## Scope

This verification checks the documentation/architecture decision-system increment defined by:

`../requirements/004-reversible-architecture.md`

It does not claim that the Article 3 tool authorities have passed later executable implementation tests. It verifies that the repository now has a coherent, lightweight, historically faithful decision/experiment/supersession model and that the Article 3 authority choices are represented through that model.

## Evidence inspected

Primary evidence:

- `../architecture/decision-system.md`
- `../decisions/README.md`
- `../decisions/ADR-TEMPLATE.md`
- `../decisions/ADR-0001-lightweight-reversible-decision-records.md`
- `../decisions/ADR-0002-one-authority-per-correctness-concern.md`
- `../decisions/ADR-0003-mise-executable-tool-version-authority.md`
- `../decisions/ADR-0004-bun-javascript-package-workspace-authority.md`
- `../decisions/ADR-0005-repository-owned-durable-project-identity.md`
- `../decisions/ADR-0006-moon-operational-project-task-authority.md`
- `../decisions/ADR-0007-nx-transformation-authority-only.md`
- `../decisions/ADR-0008-github-actions-hosted-ci-orchestration.md`
- `../decisions/ADR-0009-repository-owned-root-command-interface.md`
- `../experiments/README.md`
- `../experiments/EXP-TEMPLATE.md`
- `../journal/004-reversible-architecture.md`
- `../../articles/04-reversible-architecture.md`
- `../series.yaml`
- `../series-roadmap.md`

Historical evidence reused rather than duplicated:

- `../journal/003-authority-boundaries.md`
- `../artifacts/003-authority-capability-evidence.md`
- `../architecture/authority-map.md`
- `../architecture/system-model.md`

External reference material reviewed by the journal/article:

- https://github.com/architecture-decision-record/architecture-decision-record
- https://github.com/adr/madr
- https://adr.github.io/madr/

## Structural branch observation before verification bookkeeping

A comparison of `series/04-reversible-architecture` against `main` immediately before this verification file was created showed:

- status: ahead;
- 5 commits ahead;
- 0 commits behind;
- 23 changed paths;
- no package-manager/task-runner/toolchain/CI implementation configuration introduced.

Final PR metadata may contain additional documentation-only commits after this observation.

---

## REQ-004-01 — Define when an ADR is required

**Result: PASS**

Evidence:

`../architecture/decision-system.md` defines a future-maintainer/significance test and concrete ADR triggers including:

- authority/source-of-truth assignment;
- repository-wide boundaries/invariants;
- foundational tools with migration cost;
- public/stable interfaces;
- security/trust boundaries;
- durable schema/migration strategies;
- cross-project/language decisions;
- costly/non-obvious reversals.

The same document explicitly lists ordinary local implementation detail, formatting/naming, mechanical refactors, temporary experiments, and routine non-architectural updates as cases that normally do not require an ADR.

This is sufficiently specific for a future maintainer to distinguish consequential architecture from routine implementation without requiring an ADR for every code/config change.

---

## REQ-004-02 — Define an explicit ADR lifecycle

**Result: PASS**

Evidence:

The decision system defines exactly five statuses:

- Proposed;
- Accepted;
- Rejected;
- Withdrawn;
- Superseded.

Allowed transitions are documented as:

```text
Proposed -> Accepted -> Superseded
Proposed -> Rejected
Proposed -> Withdrawn
```

`Deprecated` was deliberately removed because it creates ambiguity about whether an architecture decision remains current.

The decision index documents the same lifecycle.

The historical immutability rule distinguishes frozen substantive content from the narrow later metadata maintenance needed to mark an accepted ADR as Superseded and link its replacement.

---

## REQ-004-03 — Separate current architecture from historical decisions

**Result: PASS**

Evidence:

`../architecture/decision-system.md` explicitly distinguishes:

- ADRs: why consequential architecture was chosen at a point in time;
- `architecture/`: how the repository is intended to work now.

`../architecture/README.md` identifies `decision-system.md` as current-state architecture while pointing historical reasoning to decisions/journal/experiments/verification/artifacts.

The authority decisions link back to `../architecture/authority-map.md` rather than attempting to replace it as the current-state authority view.

The supersession workflow requires a later accepted ADR and separate current-state architecture update.

---

## REQ-004-04 — Define the role of experiments

**Result: PASS**

Evidence:

Created:

- `../experiments/README.md`;
- `../experiments/EXP-TEMPLATE.md`.

The experiment system states that ordinary experiments remain in increment journals by default.

A standalone `EXP-NNNN` record is reserved for evidence that is reusable, consequential, expensive/reproducibility-sensitive, compares major alternatives, or directly tests an ADR assumption/reconsideration criterion.

The template distinguishes:

- question/hypothesis;
- method;
- controlled inputs/environment;
- expected observation;
- actual observation;
- interpretation;
- limitations;
- durable evidence;
- decisions/requirements informed.

No fake `EXP-0001` was created. Article 3 capability research remains an artifact because it was first-party documentation research rather than a controlled executable experiment.

---

## REQ-004-05 — Make assumptions and falsification explicit

**Result: PASS**

Evidence:

`ADR-TEMPLATE.md` requires both:

- `Assumptions`;
- `Reconsideration criteria`.

Each applied Article 3 authority ADR contains material assumptions and concrete conditions that should trigger review.

Examples include:

- `ADR-0003`: reconsider if Moon cannot safely consume mise-provided tools or the environment boundary becomes materially more fragile;
- `ADR-0004`: reconsider if Bun fails representative workspace/lock/release behavior;
- `ADR-0006`: reconsider if affected/cache correctness, polyglot behavior, or overhead fails representative evidence;
- `ADR-0007`: reconsider if Nx generation requires routine Nx execution/project state.

The decision system explicitly states that crossing a reconsideration criterion triggers review/investigation rather than automatically reversing architecture.

---

## REQ-004-06 — Trace decisions to requirements and evidence

**Result: PASS**

Evidence:

Every applied ADR contains sections for:

- Requirements;
- Evidence;
- Assumptions;
- Reconsideration criteria;
- Relationships.

The Article 3 ADRs cite relevant `UMS-*` requirements and reuse Article 3 journal/artifact/authority-map evidence.

The decision system explicitly distinguishes ADR status from requirement evidence state.

Example preserved by Article 4:

```text
ADR-0006: Accepted
UMS-EXE-001: Partial
```

The accepted Moon decision therefore does not falsely advance task-graph correctness to Verified.

---

## REQ-004-07 — Keep the system lightweight

**Result: PASS**

Evidence:

The required ADR template is plain Markdown with a small fixed set of decision-focused sections.

Rich research is linked from journals/experiments/artifacts rather than duplicated into each ADR.

The experiment system explicitly keeps ordinary investigation in the journal instead of creating a standalone record for every trial.

This increment adds no:

- database;
- custom service;
- governance DSL;
- review board;
- approval workflow engine;
- schema enforcement runtime.

The repository remains operable through Git/Markdown alone at this stage.

---

## REQ-004-08 — Define supersession without history rewriting

**Result: PASS for mechanism; real supersession intentionally remains future evidence**

Evidence:

The decision system defines:

1. investigate new evidence;
2. create a new proposed ADR;
3. accept the replacement ADR;
4. update the old ADR only with `Status: Superseded` and `Superseded by: ADR-NNNN` metadata;
5. update current-state architecture separately;
6. implement/migrate/verify the new architecture.

The old decision's context/rationale/consequences/assumptions remain historical.

Important limitation:

No architecture decision was fabricated solely to exercise supersession. Therefore `UMS-GEN-003` appropriately remains Partial until the first genuine superseded ADR/migration exists.

This limitation is a deliberate correctness decision, not a verification failure for Article 4's requirement to define the mechanism.

---

## REQ-004-09 — Apply the system to Article 3 authority decisions

**Result: PASS**

Evidence:

Article 4 creates nine accepted ADRs:

1. `ADR-0001` — adopt lightweight reversible decision records;
2. `ADR-0002` — one authority per repository correctness concern;
3. `ADR-0003` — mise executable/tool version authority;
4. `ADR-0004` — Bun JavaScript package/workspace authority;
5. `ADR-0005` — repository-owned durable project identity;
6. `ADR-0006` — Moon operational project/task/affected/cache authority;
7. `ADR-0007` — Nx transformation-only authority;
8. `ADR-0008` — GitHub Actions hosted-CI-only authority;
9. `ADR-0009` — repository-owned stable root command interface.

The split preserves independent reversibility. For example, replacing Moon can supersede `ADR-0006` without changing `ADR-0005`'s repository-owned identity principle.

`ADR-0005` explicitly preserves the final Article 3 correction separating durable project identity from Moon orchestration.

The ADRs state assumptions/future proof obligations rather than claiming tool integration already works.

---

## REQ-004-10 — Produce the reader-facing Article 4 checkpoint

**Result: PASS for branch checkpoint**

Evidence:

`../../articles/04-reversible-architecture.md` explains:

- why architecture needs reversible historical records;
- ADR scope/significance;
- decision status vs requirement evidence state;
- status lifecycle;
- proposed-time ID allocation;
- historical immutability and supersession metadata;
- current-state architecture vs ADR history;
- journal experiment vs standalone `EXP-NNNN` experiment;
- observation vs interpretation;
- reconsideration criteria;
- independently reversible Article 3 ADR decomposition;
- deliberate absence of a fake experiment/supersession;
- reproduction commands;
- remaining non-claims;
- transition from Arc I to Article 5/Repository Kernel.

`../series.yaml`, `../series-roadmap.md`, `../../articles/README.md`, root `README`, `../README.md`, and `../architecture/README.md` are updated for the draft checkpoint.

The starting commit is recorded consistently as:

`553f457b01d554429f7117f1e2ca824386835e37`

---

# Product-requirement state review

## `UMS-GEN-003 — Consequential architecture changes are traceable`

State remains: **Partial**

Article 4 adds a formal ADR/supersession mechanism and real accepted ADRs, but the requirement's remaining decisive evidence is still:

- first real superseded ADR/migration.

We intentionally do not create a fake supersession merely to advance the requirement state.

## `UMS-EXE-001 — One authoritative repository task graph`

State remains: **Partial**

`ADR-0006` strengthens traceability for the selected authority but does not provide the missing executable task/root-command/CI/generator-isolation evidence.

## Other Article 3 implementation requirements

Remain at their existing states. An Accepted ADR is a decision, not implementation proof.

---

# Overall result

**PASS**

Article 4 satisfies all ten increment requirements at the documentation/architecture level appropriate to this checkpoint.

The repository now has a coherent reversible architecture decision model, real ADRs for its existing consequential authority choices, a bounded standalone experiment model, and an explicit rule preventing accepted decisions from masquerading as verified requirements.

The first real standalone experiment and first real superseding ADR remain future evidence events, as they should.
