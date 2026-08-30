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

Each finished article corresponds to reproducible Git evidence. Branches are workflow pointers; immutable commit SHAs (and later normalized article tags) identify historical checkpoint state.

## Articles

| Increment | Article | Status | Starting ref | Checkpoint evidence |
| --- | --- | --- | --- | --- |
| 000 | Article 0 — What Does an “Ultimate Monorepo Starter” Actually Mean? | Merged | `series-start` / documentation baseline integrated during restack | `836a511951ca4ceebc0fa2fdd0ace803be4fbb7c` |
| 001 | Article 1 — The Monorepo Is a Platform, Not a Folder Full of Packages | Merged | Article 0 checkpoint | `be876f082eb6111b26b69e409f27f6bad9bf0d78` |
| 002 | Article 2 — Requirements Before Tools: Writing the Monorepo Architecture Contract | Merged | `3e1c4161f3eb9d0c385430bd32d28a18b91516c2` (`main`) | `3c03fdbbf525a06853b283a8fa267b19648966dc` (PR head), mainline merge `bd70c92aad2278cc06844e539cf2dba938f9ad4b` |
| 003 | Article 3 — Choosing Authorities: Who Owns Packages, Tools, Tasks, Generation, and CI? | Draft | `44951cd93646c2fb2dda63f384238401ef2bbc8c` (`main`) | pending review/merge |

### Early-series naming note

Articles 0 and 1 were first drafted before the three-digit engineering-record convention was integrated into the series branches, so their source paths retain `00-...` and `01-...` prefixes. Reader-facing article filenames continue with their article number (`02-...`, `03-...`) while engineering-record files use the permanent three-digit increment identifiers (`002`, `003`).

### Early stacked-branch note

Article 1 was initially reviewed as a PR into the mutable Article 0 branch. Merging it advanced that branch before Article 0 was merged to `main`. The Git history preserved both states, but the branch name no longer represented an immutable Article 0 checkpoint. Article 2 converts that lesson into requirement `UMS-EVD-001`: accepted checkpoints must resolve to immutable Git evidence rather than only mutable branch names.
