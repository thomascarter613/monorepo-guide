# Experiments

Standalone experiment records preserve consequential evidence that deserves a stable identity outside an increment journal.

Most experiments **do not** need a standalone file. Keep ordinary investigation in the relevant `docs/journal/NNN-*.md` entry.

Create an `EXP-NNNN` record when the experiment is reusable, consequential, expensive/reproducibility-sensitive, compares major alternatives, or directly tests an ADR assumption/reconsideration criterion.

## Identity

Use:

```text
EXP-NNNN
```

## Status

- **Planned** — method/hypothesis defined; execution not complete.
- **Running** — evidence collection in progress.
- **Concluded** — observations and limitations recorded.
- **Abandoned** — experiment intentionally stopped; reason recorded.

An experiment is not `Accepted` or `Rejected`; those words belong to decisions. An observation can support or weaken a hypothesis without becoming architecture by itself.

## Required distinctions

Every standalone record must distinguish:

- question/hypothesis;
- method and controlled inputs;
- expected observation;
- actual observation;
- interpretation;
- limitations/threats to validity;
- durable/raw evidence;
- decisions/requirements informed.

## Template

Use:

`EXP-TEMPLATE.md`

## Index

No standalone experiments have been created yet. Article 3 capability research remains an artifact because it was documentation research rather than a controlled executable experiment.
