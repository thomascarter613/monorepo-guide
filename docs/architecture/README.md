# Architecture

This directory describes the **current** architecture and product contract of the monorepo starter.

Unlike the engineering journal, numbered increment requirements, and accepted ADRs, architecture documents are current-state documentation and should be updated when the architecture changes.

## Current architecture documents

- `definition-of-ultimate.md` — broad product properties, non-goals, principles, hypotheses, and completion criteria.
- `system-model.md` — tool-independent repository/platform vocabulary and conceptual layers.
- `requirements-ledger.md` — stable product requirement IDs, acceptance criteria, evidence states, and planned/current proof.

These documents answer different questions:

```text
definition-of-ultimate.md
    What kind of product are we trying to build?

system-model.md
    What architectural concepts do we use to describe it?

requirements-ledger.md
    What specific promises must the product prove?
```

Historical reasoning belongs in:

- `../journal/`
- `../decisions/`
- `../requirements/NNN-*.md`
- `../verification/`

The governing rule is:

> Preserve what we knew at the time. Document what we know now separately.

A consequential change to current architecture should therefore update the relevant document here **and** leave historical evidence explaining why the change occurred.
