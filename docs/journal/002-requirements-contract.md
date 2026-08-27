# 002 — Requirements Before Tools

Status: **In progress**

## Starting state

Starting branch: `main`

Starting commit:

`3e1c4161f3eb9d0c385430bd32d28a18b91516c2`

Working branch:

`series/02-requirements-contract`

## Objective

See `../requirements/002-requirements-contract.md`.

## What we know at the start

Articles 0 and 1 are now present on `main`.

Article 0 defines the broad properties the finished starter should prove. Article 1 defines the tool-independent platform vocabulary needed to reason about repositories, workspaces, projects, graphs, tasks, toolchains, generation, policy, and delivery.

The repository also has an engineering-record protocol that distinguishes requirements, journal entries, ADRs, verification records, raw artifacts, and current-state architecture documentation.

What is still missing is a stable product-requirements layer between broad properties such as “reproducible” and implementation choices such as “use mise.”

## Initial assumptions

- A living product requirements ledger belongs in current-state architecture rather than in the numbered historical increment-requirement stream.
- Stable requirement IDs should describe concerns, not vendors.
- Requirements should be verifiable without requiring us to know the final implementation tool in advance.
- We can borrow precise normative-keyword semantics from BCP 14 without turning the monorepo starter into an IETF specification.
- ISO/IEC 25010:2023 can help us notice missing quality dimensions, but repository-specific evidence must remain the actual acceptance authority.
- Some useful requirements cannot receive quantitative thresholds yet because representative projects and workload measurements do not exist. In those cases, we should define the measurement/evidence obligation now and select a threshold only when the repository can measure it honestly.

## Questions

- How many product requirements are enough for a useful first ledger without creating requirements theater?
- Should we use only MUST-level requirements, or allow SHOULD/MAY semantics?
- How should a requirement move from accepted → partially verified → verified?
- Where should evidence links live so verification records can evolve without constantly rewriting the requirement statement?
- How should requirements that concern the engineering series itself be separated from requirements of the eventual starter product?
- Do we need a machine-readable requirements file now, or would that create a schema before we have enough evidence about the data model?

## Research note — normative language

RFC 2119 defines common requirement-level keywords such as MUST, SHOULD, and MAY. RFC 8174 updates that convention by clarifying that the special meanings apply when the keywords appear in uppercase.

We can use that semantic discipline where it improves clarity, but the repository does not need to imitate protocol-specification prose everywhere. A requirement can be normative and testable without turning every paragraph into capitalized legal language.

Sources consulted:

- https://www.rfc-editor.org/info/rfc2119/
- https://www.rfc-editor.org/info/rfc8174/

## Research note — quality completeness

ISO/IEC 25010:2023 defines a product quality model with nine characteristics and explicitly describes uses including requirements definition, checking requirement completeness, identifying test objectives, defining acceptance criteria, and establishing product-quality measures.

We will use the existence and purpose of that model as a completeness check, not claim ISO conformance. Our starter has repository-specific concerns—developer feedback loops, affected execution, generator determinism, upgradeability, AI legibility, and engineering-record integrity—that still require our own language and evidence.

Source consulted:

- https://www.iso.org/standard/78176.html

## Failure / process finding — mutable stacked checkpoints

### Context

Article 1 was developed as a stacked PR whose base was `series/00-first-principles` rather than `main`.

### Expected

We expected the stacked PR arrangement to preserve Article 0 and Article 1 as independently reviewable checkpoints until both were accepted.

### Tried

Article 1 PR #3 targeted the Article 0 branch. Article 0 PR #1 targeted `main`.

### Observed

PR #3 was merged first. That advanced the mutable `series/00-first-principles` branch. PR #1 was then merged to `main`, carrying the now-advanced branch state—which included both Article 0 and Article 1.

The content is correct and the Git history preserves the sequence, but the branch name `series/00-first-principles` no longer denotes only the Article 0 state.

### Learned

A branch is a workflow pointer, not an immutable checkpoint identity.

If the series promises reproducible article states, a checkpoint must eventually resolve to an immutable commit SHA and preferably an article tag once the checkpoint is accepted. A later branch may start from that state, but the older checkpoint cannot rely on a branch name remaining frozen.

### Changed

Article 2 will add a requirement for immutable checkpoint evidence. Going forward, once previous work is on `main`, the next article branches from current `main` instead of needing a stacked merge into an older article branch.

### Verification

The Article 2 branch was created directly from current `main` commit `3e1c4161f3eb9d0c385430bd32d28a18b91516c2`.

## Decisions made so far

### Living product ledger location

Tentative decision: `docs/architecture/requirements-ledger.md`.

Reason: the ledger describes the current product contract and is expected to evolve. Numbered files under `docs/requirements/` describe historical increment intent and should not be rewritten merely because the current architecture contract changes.

### Machine-readable schema

Tentative decision: defer a dedicated machine-readable requirements schema.

Reason: `docs/series.yaml` already demonstrates the value of structured metadata, but the product-requirements data model has not yet experienced enough implementation pressure to justify freezing a YAML schema. The first ledger will optimize for clarity and traceability in Markdown. A later self-validation or AI-context increment can extract a machine-readable representation once we know which fields prove useful.

## Experiments

_To be completed as the ledger is drafted and checked against later-arc goals._

## Remaining work

- draft the living requirements ledger;
- audit it against the twelve “ultimate” properties and planned series arcs;
- update current-state documentation to point from broad properties to specific requirements;
- write Article 2 from the engineering evidence;
- create requirement-by-requirement verification;
- update series metadata and checkpoint indexes;
- open the Article 2 draft PR;
- record ending state.
