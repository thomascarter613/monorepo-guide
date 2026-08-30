# 004 — Reversible Architecture

Status: **In progress**

## Starting state

Starting branch: `main`

Starting commit:

`553f457b01d554429f7117f1e2ca824386835e37`

Working branch:

`series/04-reversible-architecture`

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

### Initial conclusion from references

We should not copy a template wholesale. Our repository already has requirements, journal, verification, and artifact layers that carry information many generic ADR templates need to embed directly.

Our ADR can therefore stay relatively small while adding two repository-specific concepts that matter to this series:

1. **evidence/requirement links** — because decisions are evaluated against an existing product contract;
2. **reconsideration criteria** — because the series explicitly wants falsifiable, reversible architecture.

## Working decision model

The current hypothesis is:

```text
requirement / problem / observed failure
        ↓
assumptions + alternatives
        ↓
experiment(s), when uncertainty requires evidence
        ↓
ADR proposal
        ↓
accepted decision
        ↓
current-state architecture + implementation
        ↓
verification / operational evidence
        ↓
new evidence violates assumptions or exit criteria
        ↓
new ADR supersedes old ADR
        ↓
current-state architecture changes
```

Important distinctions:

- **ADR status** answers whether the architecture decision is in force historically/currently.
- **Requirement state** answers how much evidence exists that the product promise is satisfied.
- **Experiment conclusion** answers what the experiment observed under its stated conditions.
- **Current-state architecture** answers how the repository is intended to work now.

These states must not collapse into one another.

## Next investigation

1. Define the minimal ADR fields and status transitions.
2. Decide whether Article 3's authority map is one ADR or several.
3. Define standalone experiment-record rules/template.
4. Apply the model to Article 3 decisions.
5. Verify cross-linking and supersession mechanics structurally.
