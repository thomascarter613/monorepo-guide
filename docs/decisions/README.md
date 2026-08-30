# Architecture Decision Records

Architecture Decision Records (ADRs) preserve consequential technical/architectural decisions and the evidence/rationale that made them reasonable at the time.

Current decision-system rules are defined in:

`../architecture/decision-system.md`

## Status values

An ADR has one of these statuses:

- **Proposed** — under consideration; not current architecture.
- **Accepted** — current architecture unless superseded.
- **Rejected** — considered and explicitly not selected.
- **Withdrawn** — proposal abandoned before a decision was reached/needed.
- **Superseded** — once accepted, now replaced by a newer ADR.

## Historical rule

After acceptance, substantive ADR content is historical and must not be rewritten to match later thinking.

When architecture changes:

1. create a new ADR;
2. explain what changed and why;
3. accept the new ADR;
4. update the old ADR's status metadata to `Superseded` and link the replacement;
5. update current-state architecture separately.

## Numbering

Use:

```text
ADR-NNNN
```

Allocate the next number when the proposal is created.

## Template

Use:

`ADR-TEMPLATE.md`

The template is deliberately smaller than a research report. Rich investigation belongs in journals, experiments, and artifacts; the ADR links to that evidence.

## Index

| ADR | Decision | Status | Supersedes | Superseded by |
| --- | --- | --- | --- | --- |
| ADR-0001 | Adopt lightweight reversible architecture decision records | Accepted | — | — |
| ADR-0002 | Assign one authority per repository correctness concern | Accepted | — | — |
| ADR-0003 | Use mise as executable/tool version authority | Accepted | — | — |
| ADR-0004 | Use Bun as JavaScript package/workspace authority | Accepted | — | — |
| ADR-0005 | Keep durable project identity repository-owned | Accepted | — | — |
| ADR-0006 | Use Moon as operational project/task/affected/cache authority | Accepted | — | — |
| ADR-0007 | Restrict Nx authority to repository transformations | Accepted | — | — |
| ADR-0008 | Use GitHub Actions for hosted CI orchestration only | Accepted | — | — |
| ADR-0009 | Keep the stable root command interface repository-owned | Accepted | — | — |
