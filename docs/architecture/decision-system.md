# Reversible Architecture Decision System

Status: **Current architecture contract**

This document defines how consequential architecture decisions are proposed, evidenced, accepted, reconsidered, and superseded in this repository.

The goal is not to make architecture permanent. The goal is to make change **legible**.

> Preserve the decision we made, preserve why we made it, and change current architecture explicitly when new evidence earns the change.

## The artifact roles

These artifacts answer different questions and must remain distinct.

| Artifact | Primary question | Historical/current |
| --- | --- | --- |
| Product requirement | What must the starter prove? | Current contract with preserved state history |
| Increment requirement | What did this increment intend to accomplish? | Historical |
| Engineering journal | What actually happened while we investigated/built it? | Historical |
| Experiment record | What did a controlled investigation observe? | Historical evidence |
| ADR | What consequential architecture decision did we make, and why? | Historical decision record |
| Current-state architecture | How is the repository intended to work now? | Current |
| Verification record | What evidence supports the requirement claim? | Historical evidence |
| Article | How do we teach the increment accurately? | Curated narrative |

None of these substitutes for another.

An accepted ADR does not verify a product requirement. An experiment does not make an architecture decision. A current-state architecture document does not replace the historical ADR that explains why the design exists.

---

# When an ADR is required

Create an ADR when a decision is consequential enough that a future maintainer is likely to need the answer to:

> Why is the repository constrained this way, and what would justify changing it?

Strong ADR triggers include decisions that:

- assign architectural authority or a source of truth;
- establish or change a repository-wide boundary/invariant;
- select a foundational tool or external dependency whose replacement has migration cost;
- define a public/stable developer, CI, agent, package, schema, or release interface;
- establish a security/trust/permission boundary;
- choose a data/schema/migration strategy with durable consequences;
- affect multiple projects/domains/languages;
- materially constrain future implementation choices;
- deliberately reject a plausible alternative for architecture reasons;
- are costly, risky, or non-obvious to reverse;
- would otherwise invite repeated relitigation because the rationale is not visible in the code.

## An ADR is usually not required for

- local implementation details already permitted by accepted architecture;
- naming/formatting choices without durable architectural effect;
- mechanical refactors that preserve accepted boundaries;
- a temporary experiment before a decision exists;
- routine dependency patch/minor updates that do not change architecture;
- every file/configuration change made to implement an already-recorded decision.

When uncertain, prefer an ADR if the expected cost of **forgetting why** is higher than the cost of writing a short record.

---

# ADR identity and status

ADR IDs use:

```text
ADR-NNNN
```

Allocate the ID when the proposed record is created so discussion and evidence can refer to a stable identity before acceptance.

Supported statuses:

- **Proposed** — decision is under consideration and not current architecture.
- **Accepted** — decision is current architecture unless superseded.
- **Rejected** — proposal was considered and explicitly not selected.
- **Withdrawn** — proposal was abandoned before a decision was required/reached.
- **Superseded** — decision was once accepted but a newer ADR now replaces it.

`Deprecated` is intentionally not an ADR status. Deprecation is useful for APIs/artifacts with transition periods, but it is ambiguous for architecture decisions: either the decision remains current or a newer decision replaces it.

## Allowed lifecycle

```text
Proposed ──► Accepted ──► Superseded
   │
   ├──────► Rejected
   │
   └──────► Withdrawn
```

Rejected, Withdrawn, and Superseded are terminal historical states.

---

# Historical immutability rule

Once an ADR is Accepted, its substantive historical record is frozen.

Do not rewrite its context, alternatives, rationale, assumptions, consequences, or decision to match later thinking.

The following later metadata maintenance is allowed:

- change `Status: Accepted` to `Status: Superseded`;
- add `Superseded by: ADR-NNNN`;
- repair broken repository links without changing historical meaning;
- fix an obvious typographical error that cannot alter interpretation.

A material correction or changed decision requires a new ADR.

The superseding ADR must link back to the earlier ADR and explain what evidence/assumption changed.

---

# Minimal ADR contents

Every ADR must contain:

1. **Identity/title** — stable ADR ID and concise decision title.
2. **Status/date** — lifecycle state and decision date when accepted/rejected/withdrawn.
3. **Context** — the problem/constraint forcing a decision.
4. **Decision** — the architecture chosen, stated plainly.
5. **Rationale** — why this option was selected over relevant alternatives.
6. **Consequences** — important positive/negative constraints introduced.
7. **Requirements** — relevant `UMS-*` and/or increment requirement references where applicable.
8. **Evidence** — journal/experiment/artifact/verification/PR/commit evidence supporting the decision.
9. **Assumptions** — material beliefs on which the decision depends.
10. **Reconsideration criteria** — observable conditions that should trigger review.
11. **Relationships** — supersedes/superseded-by links when applicable.

Alternatives considered should be included when more than one plausible option materially influenced the decision. They can remain concise and link to richer research evidence.

## Why reconsideration criteria are mandatory here

This series explicitly treats architecture as falsifiable.

A decision without a known exit condition can still be valid, but it is too easy for it to become accidental dogma. If no concrete reconsideration criterion is currently known, the ADR must say so rather than omit the section.

Reconsideration criteria are **triggers for review**, not automatic migration commands. Crossing a criterion means we investigate and potentially create a superseding ADR.

---

# Experiments and evidence

An experiment answers an uncertainty. It does not choose architecture by itself.

Small experiments should remain inside the increment journal when their evidence is understandable there.

Create a standalone `EXP-NNNN` record when one or more are true:

- the method/result is likely to be cited by multiple ADRs or increments;
- the experiment compares consequential alternatives;
- reproducing it requires enough setup that a journal paragraph would lose important controls;
- the evidence is expensive, performance-sensitive, security-sensitive, or likely to be challenged;
- raw evidence needs a stable semantic home beyond a generic artifact filename;
- the result directly tests an ADR assumption or reconsideration criterion.

Standalone experiment records live under `docs/experiments/`.

An experiment record distinguishes:

```text
question / hypothesis
method + controlled inputs
expected observation
actual observation
interpretation
limitations
raw/durable evidence
```

Do not collapse `Observed` and `Interpretation`. Measurements are evidence; conclusions are our explanation of that evidence.

---

# Decision workflow

The normal path is:

```text
requirement / problem / failure
        ↓
initial assumptions + plausible alternatives
        ↓
experiment / research when needed
        ↓
ADR (Proposed)
        ↓
review against requirements + evidence
        ↓
ADR (Accepted / Rejected / Withdrawn)
        ↓
update current-state architecture
        ↓
implementation
        ↓
verification / operational evidence
```

When later evidence challenges the accepted design:

```text
new evidence / violated assumption / reconsideration trigger
        ↓
engineering journal / experiment
        ↓
new ADR (Proposed)
        ↓
new ADR (Accepted)
        ↓
old ADR metadata -> Superseded by new ADR
        ↓
update current-state architecture
        ↓
migrate / implement / verify
```

The repository never needs to pretend the original decision was irrational simply because a later environment makes another choice better.

---

# Decision status is not requirement evidence state

These statements are intentionally independent:

```text
ADR-0006: Accepted
UMS-EXE-001: Partial
```

They can coexist legitimately.

`Accepted` means we have chosen an architecture to try/use.

`Partial` means the product requirement still lacks all promised implementation/verification evidence.

Likewise, an ADR can later be superseded even if the original implementation passed every test available at the time. Architecture changes because requirements, constraints, tools, workloads, or evidence change.

---

# Relationship to Article 3 authority choices

Article 3 established several independently reversible boundaries. They are recorded as separate ADRs because one authority can later change without forcing all others to change.

The authority map remains the canonical current-state view:

`docs/architecture/authority-map.md`

The Article 3 journal and capability artifact remain the historical evidence:

- `docs/journal/003-authority-boundaries.md`
- `docs/artifacts/003-authority-capability-evidence.md`

The ADRs summarize the actual choices and link back to those records instead of duplicating the research.

---

# Review checklist

Before accepting an ADR, ask:

- What requirement/problem forces a decision now?
- Is this actually architectural, or merely implementation detail?
- What plausible alternatives were considered?
- What evidence supports the choice?
- Which assumptions remain unproved?
- What consequences/constraints are we accepting?
- What observation would make us reconsider?
- Does current-state architecture need an update?
- Can this decision be independently superseded, or have we bundled unrelated choices?

That last question is important. Smaller independently reversible ADRs are preferable to umbrella decisions when components have different evidence and exit criteria.
