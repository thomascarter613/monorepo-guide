# 002 — Requirements Before Tools

Status: **Implementation and verification complete; awaiting PR acceptance**

## Starting state

Starting branch: `main`

Starting commit:

`3e1c4161f3eb9d0c385430bd32d28a18b91516c2`

Working branch:

`series/02-requirements-contract`

## Objective

See `../requirements/002-requirements-contract.md`.

## What we knew at the start

Articles 0 and 1 were present on `main`.

Article 0 defined the broad properties the finished starter should prove. Article 1 defined the tool-independent platform vocabulary needed to reason about repositories, workspaces, projects, graphs, tasks, toolchains, generation, policy, and delivery.

The repository also had an engineering-record protocol that distinguishes requirements, journal entries, ADRs, verification records, raw artifacts, and current-state architecture documentation.

What was still missing was a stable product-requirements layer between broad properties such as “reproducible” and implementation choices such as “use mise.”

## Initial assumptions

- A living product requirements ledger belongs in current-state architecture rather than in the numbered historical increment-requirement stream.
- Stable requirement IDs should describe concerns, not vendors.
- Requirements should be verifiable without requiring us to know the final implementation tool in advance.
- We can borrow precise normative-keyword semantics from BCP 14 without turning the monorepo starter into an IETF specification.
- ISO/IEC 25010:2023 can help us notice missing quality dimensions, but repository-specific evidence must remain the actual acceptance authority.
- Some useful requirements cannot receive quantitative thresholds yet because representative projects and workload measurements do not exist. In those cases, we should define the measurement/evidence obligation now and select a threshold only when the repository can measure it honestly.

## Questions we started with

- How many product requirements are enough for a useful first ledger without creating requirements theater?
- Should we use only MUST-level requirements, or allow SHOULD/MAY semantics?
- How should a requirement move from accepted → partially verified → verified?
- Where should evidence links live so verification records can evolve without constantly rewriting the requirement statement?
- How should requirements that concern the engineering series itself be separated from requirements of the eventual starter product?
- Do we need a machine-readable requirements file now, or would that create a schema before we have enough evidence about the data model?

## Research note — normative language

RFC 2119 defines common requirement-level keywords such as MUST, SHOULD, and MAY. RFC 8174 updates that convention by clarifying that the special meanings apply when the keywords appear in uppercase.

We adopted that semantic discipline where it improves clarity, but the repository does not imitate protocol-specification prose everywhere. A requirement can be normative and testable without turning every paragraph into capitalized legal language.

Sources consulted:

- https://www.rfc-editor.org/info/rfc2119/
- https://www.rfc-editor.org/info/rfc8174/

## Research note — quality completeness

ISO/IEC 25010:2023 defines a product quality model with nine characteristics and explicitly describes uses including requirements definition, checking requirement completeness, identifying test objectives, defining acceptance criteria, and establishing product-quality measures.

We use the existence and purpose of that model as a completeness check, not claim ISO conformance. Our starter has repository-specific concerns—developer feedback loops, affected execution, generator determinism, upgradeability, AI legibility, and engineering-record integrity—that still require our own language and evidence.

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

If the series promises reproducible article states, a checkpoint must resolve to immutable commit evidence and preferably a stable article tag once the checkpoint process is normalized. A later branch may start from that state, but the older checkpoint cannot rely on a branch name remaining frozen.

### Changed

Article 2 added `UMS-EVD-001 — Article/checkpoint identity is immutable evidence`.

The article index and `docs/series.yaml` now identify the exact reviewed Article 0 and Article 1 states with immutable commit SHAs.

Going forward, once previous work is accepted on `main`, the next article branches from current `main` instead of needing a stacked merge into an older article branch.

### Verification

The Article 2 branch was created directly from current `main` commit `3e1c4161f3eb9d0c385430bd32d28a18b91516c2`.

## Decision — living product ledger location

Decision: use `docs/architecture/requirements-ledger.md`.

Reason: the ledger describes the current product contract and is expected to evolve. Numbered files under `docs/requirements/` describe historical increment intent and should not be rewritten merely because the current architecture contract changes.

This distinction was also added to:

- `docs/architecture/README.md`;
- `docs/requirements/README.md`;
- the root `README`.

## Decision — machine-readable requirements schema

Decision: defer a dedicated machine-readable requirements schema.

Reason: `docs/series.yaml` demonstrates the value of structured metadata, but the product-requirements data model has not yet experienced enough implementation pressure to justify freezing a YAML schema. The first ledger optimizes for clarity and traceability in structured Markdown.

A later repository self-validation or AI-context increment can introduce a machine-readable representation once implementation shows which fields are actually stable/useful.

## Experiment — how much ledger is enough?

### Expected

A small set of broad requirements might be enough to evaluate the planned tool choices.

### Tried

We started from the twelve “ultimate” properties and mapped the planned series arcs into concrete product promises.

### Observed

Broad statements such as “reproducible,” “secure,” and “polyglot-ready” were not specific enough to generate future verification directly. Splitting them by concern produced 36 requirements across 12 groups.

The groups are:

1. environment/bootstrap;
2. workspace/project/dependencies;
3. execution/affected/caching;
4. generation/migration/evolution;
5. quality/feedback;
6. delivery/CI/releases;
7. polyglot architecture;
8. security;
9. observability/measurement;
10. AI-assisted engineering;
11. starter productization/upgrades;
12. developer interface/evidence integrity.

### Learned

The useful unit is not “one requirement per slogan.” The useful unit is one promise with acceptance criteria that can produce meaningful evidence.

Thirty-six requirements is already enough to expose gaps and evaluate later authority decisions without trying to enumerate every future implementation detail.

### Changed

The ledger records known gaps rather than growing until every possible software-quality topic has boilerplate text.

### Verified

`../verification/002-requirements-contract.md` checks the first ledger against the Article 2 increment requirements and planned lifecycle scope.

## Experiment — MUST vs SHOULD

### Expected

Using only MUST statements might keep the ledger simple.

### Tried

We applied BCP 14 semantics to representative requirements.

### Observed

Some qualities are genuine hard constraints (`MUST`) while others are strong defaults that may legitimately yield to evidence (`SHOULD`). For example, fast feedback tiers are important, but the exact split may vary with measured cost and workload.

### Learned

`SHOULD` is useful when deviation is permitted only with a reason. It must not become a euphemism for an unmeasured preference.

### Changed

The ledger allows BCP 14 MUST/SHOULD/MAY semantics and separately labels implementation preferences as non-requirements.

## Verification

Created:

`../verification/002-requirements-contract.md`

Overall result: **PASS for the Article 2 increment**.

Important distinction:

The PASS means Article 2 successfully created and verified the requirements system it promised. It does **not** mean the 36 living product requirements are already satisfied. Most remain in `Accepted` state and are deliberately waiting for implementation evidence from later articles.

## Outputs

Created:

- `../../articles/02-requirements-before-tools.md`;
- `../architecture/requirements-ledger.md`;
- `../requirements/002-requirements-contract.md`;
- `../journal/002-requirements-contract.md`;
- `../verification/002-requirements-contract.md`.

Updated:

- root `README`;
- `../../articles/README.md`;
- `../architecture/README.md`;
- `../requirements/README.md`;
- `../series.yaml`;
- `../series-roadmap.md` at finalization.

## Changes from initial assumptions

The initial assumptions mostly survived, with three refinements:

1. The ledger needed more concrete requirements than the twelve broad product properties suggested; the first usable cut reached 36.
2. Process/evidence constraints such as immutable checkpoint identity legitimately belong in this repository’s current product-development contract even when they are not runtime starter features.
3. We adopted `SHOULD` semantics for evidence-sensitive defaults rather than forcing every quality statement into MUST/MUST NOT.

## Remaining questions

- When should the product requirements ledger become machine-readable?
- Which requirement fields remain stable after the first real implementation/ADR cycle?
- How should article tags be normalized without rewriting early history?
- Which accepted product requirements need to be split once representative projects expose hidden dimensions?
- Which quantitative thresholds become meaningful once task timing and CI data exist?

## Ending state

Implementation/verification branch:

`series/02-requirements-contract`

Pre-finalization verification commit:

`432d079f2448fb2baafb7cf9e223437e4747cad0`

The final review head will necessarily move as the journal, roadmap, PR number, and review metadata are recorded. The immutable accepted checkpoint must therefore be recorded after acceptance in `docs/series.yaml`/the article index (and later as a normalized article tag), rather than attempting to make a file contain the SHA of the commit that contains itself.

That recursive limitation is another reason checkpoint identity should be treated as release/review metadata, not hand-maintained prose pretending to know its own future commit hash.
