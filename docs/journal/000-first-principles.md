# 000 — Define the Series and the Meaning of “Ultimate”

Status: **Open engineering journal**

## Starting state

Absolute series start:

`c7bfe90d22ebe89f3a03c39590d5de9dc68c2d1c` (`series-start`)

The initial repository contained only zero-byte `README` and `LICENSE` placeholders.

The current documentation baseline later became:

`fac6e0bb1779ec5c5e3e09030f520a075ec06505`

which merged the engineering-record system to `main`.

## Objective

See `../requirements/000-first-principles.md`.

## Important chronology note

This journal was not created before the first Article 0 work.

Article 0 was initially drafted directly from the original repository state on branch `series/00-first-principles`. Draft PR #1 was opened with four paths: the root README, the first article, the definition-of-ultimate architecture contract, and the series roadmap.

After that work began, a separate documentation-infrastructure change was merged to `main` through PR #2. It added the engineering protocol, requirements/journal/verification/ADR templates, artifact conventions, and `docs/series.yaml`.

Because the series explicitly values authentic history, this file does not pretend otherwise. The record below reconstructs the earlier reasoning from the Article 0 artifact and Git history, then records subsequent events prospectively from the point the protocol became available.

## Initial assumptions reconstructed from Article 0

- “Ultimate” should describe provable engineering properties rather than a large tool inventory.
- The repository should be treated as a small developer platform rather than a package container.
- TypeScript/Bun can be the first-class initial ecosystem without defining the universal project model.
- Bun, mise, Moon, and Nx appear capable of occupying separate authorities, but those boundaries are provisional.
- Failures and reversals should remain visible in the series.
- The final starter should be upgradeable rather than only generatable.

## Questions

- Which candidate tool capabilities overlap in ways that threaten one-authority-per-concern?
- What vocabulary can survive replacement of a current tool?
- How much repository state can be machine-verified before implementation tooling exists?
- How should the newly introduced engineering-record protocol integrate with work that began before it existed?

## Engineering event — documentation protocol landed concurrently

### Expected

The Article 0 branch was expected to remain four commits ahead of the initial `main` baseline while Article 1 was developed as a stacked increment.

### Tried

During Article 1 verification, we compared `series/00-first-principles` with `main`.

### Observed

`main` had advanced by two commits. PR #2 had merged a substantial engineering-record system containing requirements, journals, ADR templates, verification records, artifacts, and a machine-readable series index.

The Article 0 diff was still the intended four paths, but its branch was now behind and its README had diverged from the new documentation baseline.

### Learned

The new documentation system is not unrelated churn; it implements the exact evidence-preservation workflow the series requires. Treating it as a conflict to overwrite would violate the series' own objective.

### Changed

We restacked the series checkpoints conceptually on top of the new `main` baseline and added explicit requirement/journal/verification records for increment `000`.

The retrospective nature of those records is disclosed instead of being rewritten as if they existed before Article 0 work began.

### Verified

The rebuilt Article 0 tree preserves every file from `main` and adds the Article 0 architecture artifacts plus the engineering-record files for increment `000`.

## Engineering event — Article 1 commit briefly landed on Article 0 branch

While beginning Article 1, the first `system-model.md` commit was accidentally written to `series/00-first-principles`.

The commit was preserved by creating the Article 1 branch from it, then the Article 0 branch ref was moved back to its prior verified head. This mistake is relevant because it demonstrates why stacked checkpoint boundaries must be verified explicitly rather than inferred from branch names.

The later restack onto the new documentation baseline supersedes that temporary branch topology, but the event remains part of the engineering history.

## Decisions made in this increment

No candidate implementation tool is accepted as final authority here.

The durable decisions are about method:

- define testable product properties before selecting configuration;
- record non-goals;
- keep tool choices falsifiable;
- preserve meaningful failures;
- treat the roadmap as an evolving dependency graph, not a fixed publishing calendar.

## Verification

See `../verification/000-first-principles.md`.

## Remaining questions

- whether the Article 0/1 source filenames should later be normalized to the new three-digit increment convention;
- when checkpoint tags should be created relative to draft PR review/merge;
- which repository checks should eventually validate the engineering-record contract automatically.

## Ending state

Draft branch: `series/00-first-principles`

Pull request: #1

Final checkpoint tag: pending review/merge.
