# 004 — Reversible Architecture

Status: **Complete — verification passed, pending review checkpoint**

## Starting state

Starting branch: `main`

Starting commit:

`553f457b01d554429f7117f1e2ca824386835e37`

Working branch:

`series/04-reversible-architecture`

Pull request:

`#6`

## Objective

See `../requirements/004-reversible-architecture.md`.

## What we know at the start

Articles 0–3 are accepted on `main`.

The repository already distinguishes:

- historical increment requirements;
- an engineering journal;
- current-state architecture;
- verification records;
- raw evidence/artifacts;
- reader-facing articles;
- an ADR directory with a minimal status/index description.

Article 3 made the first substantial architecture choices: a provisional per-concern authority map for mise, Bun, a repository-owned project contract, Moon, Nx, GitHub Actions, the root command interface, and future ecosystem-native tools.

Those choices are intentionally falsifiable. The authority map already contains reconsideration criteria, but the repository does not yet have a complete decision lifecycle explaining how such architecture becomes accepted, how experiments support it, or how it is superseded later without rewriting history.

## Initial assumptions

- ADRs should remain small historical decision records rather than becoming architecture specifications.
- Current-state architecture must remain separate from ADR history.
- Experiments produce evidence; they do not automatically make decisions.
- Most small experiments belong in the increment journal rather than requiring another document.
- Standalone experiment records are useful when evidence is reusable, consequential, or larger than one journal section.
- Exit criteria belong with the decision they can falsify, not in a disconnected risk list.
- An accepted ADR should be immutable in its substantive historical content, but status/link metadata may need carefully defined maintenance when the ADR is superseded.
- The Article 3 authority map should produce real ADRs now so this increment proves the lifecycle against an actual consequential decision instead of only writing templates.

## Questions

- What exact threshold makes a decision ADR-worthy?
- Should a rejected proposal receive an ADR number, or should numbering occur only once a decision is accepted?
- Should `Deprecated` remain an ADR status, or does `Superseded` communicate architecture history more precisely?
- How do we update the old ADR's status to `Superseded` without violating the rule that accepted ADRs are historical records?
- How much evidence belongs inside an ADR versus linked from journals/experiments/artifacts?
- Should Article 3's authority model become one umbrella ADR or multiple concern-specific ADRs?
- Should experiment records have stable IDs independent of increment numbers?
- How should an ADR express assumptions and exit criteria without creating fake certainty?

## External reference review

### ADR concept

The established ADR definition is intentionally small: an ADR captures an important architecture decision together with its context and consequences. That reinforces our existing methodology rather than suggesting a heavyweight architecture-governance system.

Reference reviewed:

- https://github.com/architecture-decision-record/architecture-decision-record

### MADR

MADR provides a richer Markdown ADR structure with considered options and decision outcome while retaining a file-based docs-as-code workflow. Its current project also uses explicit status metadata and supports links among ADRs.

References reviewed:

- https://github.com/adr/madr
- https://adr.github.io/madr/

### Conclusion from references

We did not copy a generic ADR template wholesale. Our repository already has requirements, journal, verification, and artifact layers that carry information many generic ADR templates need to embed directly.

Our ADR therefore stays relatively small while adding two repository-specific concepts that matter to this series:

1. **evidence/requirement links** — because decisions are evaluated against an existing product contract;
2. **reconsideration criteria** — because the series explicitly wants falsifiable, reversible architecture.

## Decision 1 — ADR significance uses the future-maintainer test

We adopted this practical threshold:

> Would a future maintainer reasonably need to know why the repository is constrained this way and what would justify changing it?

Strong triggers include authority assignment, repository-wide invariants, foundational tools with migration cost, stable interfaces, security/trust boundaries, durable schema/migration choices, cross-project/language constraints, and non-obvious decisions likely to be relitigated.

Routine local implementation detail, mechanical refactoring, formatting/naming, and temporary experiments do not normally justify an ADR.

## Decision 2 — ADR IDs are allocated while Proposed

We considered allocating IDs only after acceptance because that produces a tidier accepted sequence.

We rejected that approach because journals, experiments, PRs, and artifacts need a stable decision identity while the proposal is still being evaluated.

Therefore the ID is allocated when the proposal record is created. Rejected and Withdrawn records keep their IDs as honest history.

## Decision 3 — remove Deprecated from the ADR lifecycle

The initial decision directory allowed:

```text
Proposed
Accepted
Rejected
Deprecated
Superseded
```

`Deprecated` is useful for APIs/artifacts that remain temporarily supported, but it is ambiguous for architecture decisions: is the decision still current or not?

The final lifecycle is:

```text
Proposed -> Accepted -> Superseded
Proposed -> Rejected
Proposed -> Withdrawn
```

Rejected, Withdrawn, and Superseded are terminal historical states.

## Decision 4 — accepted ADR substance is frozen; supersession metadata is not

A strict “never edit an accepted ADR” rule collides with discoverability after supersession.

We resolved the tension by distinguishing substantive history from lifecycle metadata.

After acceptance, do not rewrite:

- context;
- decision;
- rationale;
- alternatives;
- consequences;
- assumptions;
- original evidence interpretation;
- original reconsideration criteria.

Later maintenance may:

- change `Accepted` to `Superseded`;
- add `Superseded by: ADR-NNNN`;
- repair broken links;
- fix non-semantic typos.

The new ADR owns the changed rationale/current decision.

## Decision 5 — experiment records are exceptional, not mandatory

Most experiments remain inside the increment journal.

Standalone records use `EXP-NNNN` only when the evidence is reusable, consequential, reproduction-sensitive, compares major alternatives, or directly tests an ADR assumption/reconsideration criterion.

The experiment template separates:

```text
hypothesis/question
method + controlled inputs
expected observation
actual observation
interpretation
limitations
raw/durable evidence
```

Experiments use `Planned`, `Running`, `Concluded`, and `Abandoned`; they are not Accepted/Rejected because experiments produce evidence rather than architecture decisions.

No `EXP-0001` was created by this increment. Article 3 capability research remains an artifact because it was documentation research rather than a controlled executable experiment.

## Decision 6 — Article 3 authority choices become separate ADRs

A single umbrella ADR such as:

```text
Use mise + Bun + Moon + Nx + GitHub Actions
```

would couple choices that have different evidence and different exit criteria.

We therefore created:

- `ADR-0001` — lightweight reversible architecture decision records;
- `ADR-0002` — one authority per repository correctness concern;
- `ADR-0003` — mise executable/tool version authority;
- `ADR-0004` — Bun JavaScript package/workspace authority;
- `ADR-0005` — repository-owned durable project identity;
- `ADR-0006` — Moon operational project/task/affected/cache authority;
- `ADR-0007` — Nx transformation-only authority;
- `ADR-0008` — GitHub Actions hosted-CI-only authority;
- `ADR-0009` — repository-owned stable root command interface.

This lets a later decision replace one authority without forcing unrelated decisions to move.

## Decision 7 — decision state remains independent from requirement evidence state

We explicitly preserve cases such as:

```text
ADR-0006: Accepted
UMS-EXE-001: Partial
```

An accepted architecture decision identifies what we are currently choosing to implement/prove. It does not prove the product requirement already works.

No Article 3 implementation requirement was advanced merely because an ADR now exists.

## Decision 8 — do not fabricate a supersession

`UMS-GEN-003` remains Partial because its remaining decisive evidence includes the first real superseded ADR/migration.

We considered the process implication: Article 4 could manufacture a trivial decision and supersede it immediately to demonstrate mechanics.

We rejected that as evidence gaming.

The supersession mechanism is defined and structurally verified, but the first genuine architecture reversal will provide the remaining real-world evidence.

## Outputs

Created:

- `../../articles/04-reversible-architecture.md`;
- `../architecture/decision-system.md`;
- `../decisions/ADR-TEMPLATE.md`;
- `../experiments/README.md`;
- `../experiments/EXP-TEMPLATE.md`;
- `../requirements/004-reversible-architecture.md`;
- `../journal/004-reversible-architecture.md`;
- `../verification/004-reversible-architecture.md`;
- `ADR-0001` through `ADR-0009` under `../decisions/`.

Updated:

- root `README`;
- `../../articles/README.md`;
- `../README.md`;
- `../architecture/README.md`;
- `../decisions/README.md`;
- `../series.yaml`;
- `../series-roadmap.md`.

## Verification

See:

`../verification/004-reversible-architecture.md`

Result: **PASS for all ten Article 4 increment requirements**.

Important limitations preserved by verification:

- no monorepo implementation toolchain was installed/configured;
- no Accepted ADR is treated as executable requirement proof;
- no standalone experiment was invented without an actual experiment;
- no superseded ADR was invented solely to close `UMS-GEN-003`.

## Changes from initial assumptions

The main assumptions survived with four refinements:

1. `Deprecated` was removed from the decision lifecycle; `Superseded` is the unambiguous replacement state for previously accepted architecture.
2. ADR identity is allocated at proposal time, not acceptance time.
3. accepted ADR immutability has a narrow, explicit supersession/link-metadata exception.
4. Article 3 required multiple independently reversible ADRs rather than one stack-level decision.

The assumption that standalone experiments should be exceptional was strengthened: Article 4 intentionally creates the experiment schema without creating an artificial experiment instance.

## Remaining questions

- At what decision volume should ADR/experiment indexes become generated rather than hand-maintained?
- Should machine-readable frontmatter be added after real automation needs reveal stable metadata fields?
- What smallest durable project-identity representation should Article 5 introduce under `ADR-0005`?
- When the first real supersession occurs, what automation should verify that the old ADR, new ADR, architecture docs, migration, and verification remain mutually linked?
- Should future performance experiments standardize hardware/runner/workload metadata further once representative workloads exist?

## Review checkpoint

Draft pull request:

`#6 — Article 4: establish reversible architecture decisions`

After PR-number bookkeeping, the branch remains based directly on accepted `main` commit `553f457b01d554429f7117f1e2ca824386835e37` with no stacked dependency on the Article 3 branch.

The final branch comparison should be treated as PR metadata because any attempt to write a commit SHA/count into the commit that contains itself creates the same recursive metadata problem identified earlier in the series.

## Next

Article 5 begins Arc II — Repository Kernel:

**Building the Smallest Useful Repository Contract**

Its first architectural pressure point is now clear: `ADR-0005` requires durable project identity to remain repository-owned, but deliberately does not dictate its representation. Article 5 should determine the smallest project/root metadata contract that earns that responsibility without creating an unnecessary universal ontology.
