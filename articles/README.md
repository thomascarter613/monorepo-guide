# Building the Ultimate Monorepo Starter from First Principles

A tutorial, engineering log, architecture journal, and reproducible build
from an empty directory to a production-grade polyglot workspace.

## Relationship to the engineering record

Articles are reader-facing explanations assembled from the repository's
engineering record.

The underlying historical evidence lives in:

- `../docs/requirements/`
- `../docs/journal/`
- `../docs/decisions/`
- `../docs/verification/`
- `../docs/artifacts/`

Each finished article corresponds to a reproducible Git checkpoint.

## Articles

| Increment | Article | Status | Starting ref | Ending ref |
| --- | --- | --- | --- | --- |
| 000 | Article 0 — What Does an “Ultimate Monorepo Starter” Actually Mean? | Draft | `series-start` / documentation baseline integrated during restack | pending `article-000` |
| 001 | Article 1 — The Monorepo Is a Platform, Not a Folder Full of Packages | Draft | `series/00-first-principles` | pending `article-001` |

### Early-series naming note

Articles 0 and 1 were first drafted before the three-digit engineering-record convention was integrated into the series branches, so their source paths currently retain `00-...` and `01-...` prefixes. The mismatch is recorded rather than silently rewritten; a later repository-contract increment may normalize article-source naming if we decide the migration is worth doing.
