# 002 — Requirements Before Tools

Status: **Draft**

## Objective

Turn the product goals and architecture properties established in Articles 0 and 1 into a stable, traceable, tool-neutral requirements contract that later implementation decisions can be evaluated against.

This increment must distinguish the living product contract from per-increment historical requirements and must create a repeatable path from requirement → acceptance criteria → implementation evidence → verification status.

## Requirements

### REQ-002-01 — Separate product requirements from increment requirements

Acceptance criteria:

- per-increment files under `docs/requirements/NNN-*.md` remain historical records of what a specific increment intended to accomplish;
- the living requirements for the starter as a product have a separate current-state home under `docs/architecture/`;
- changing a living product requirement does not require rewriting an old increment requirement as though history changed.

### REQ-002-02 — Give product requirements stable identities

Acceptance criteria:

- every product requirement has a stable identifier independent of the tool eventually chosen to satisfy it;
- identifiers are grouped by architectural concern rather than product/vendor name;
- later articles can cite a requirement unambiguously from implementation, ADRs, verification, and measurements.

### REQ-002-03 — Make requirements testable

Acceptance criteria:

- each accepted requirement states an observable condition rather than a vague aspiration;
- each accepted requirement includes concrete acceptance criteria or names the future evidence needed to close it;
- statements such as “fast,” “easy,” “secure,” and “reproducible” are not considered sufficient without a verification interpretation.

### REQ-002-04 — Separate requirements, preferences, assumptions, and decisions

Acceptance criteria:

- tool choices such as Bun, mise, Moon, and Nx do not appear as product requirements unless the product itself truly requires those brands;
- the requirements ledger identifies architectural constraints without turning current implementation preferences into mandatory outcomes;
- assumptions and implementation hypotheses remain explicitly falsifiable.

### REQ-002-05 — Define requirement status and evidence semantics

Acceptance criteria:

- the ledger distinguishes at least proposed/accepted/partially verified/verified/superseded states or equivalent semantics;
- “implemented” is not treated as synonymous with “verified”;
- evidence references can point to verification records, tests, benchmarks, PRs/commits, or preserved artifacts.

### REQ-002-06 — Cover the whole starter lifecycle

Acceptance criteria:

The first ledger version includes requirements covering, at minimum:

- environment/bootstrap;
- workspace/project/dependency behavior;
- execution, affected analysis, and caching;
- generation and migration;
- quality/governance;
- CI/delivery/release;
- polyglot integration;
- security;
- observability/measurement;
- AI-assisted engineering;
- starter distribution and upgrades;
- repository/series evidence integrity where it affects reproducibility of the engineering record.

### REQ-002-07 — Use external standards as references, not borrowed authority

Acceptance criteria:

- RFC 2119 / RFC 8174 (BCP 14) may inform normative-keyword semantics, but the repository states exactly how it adopts them;
- ISO/IEC 25010:2023 may be used as a quality-completeness heuristic, but the repository does not claim certification or conformance it has not established;
- the article distinguishes a useful reference model from a substitute for repository-specific acceptance criteria.

### REQ-002-08 — Preserve the stacked-checkpoint failure as evidence

Acceptance criteria:

- the journal records that merging Article 1 into the mutable Article 0 branch advanced the Article 0 branch before it was merged to `main`;
- the resulting lesson is converted into a product/process requirement that checkpoint identity resolve to immutable commit/tag evidence rather than only a mutable branch name;
- no history is rewritten to make the early checkpoint flow appear cleaner than it was.

### REQ-002-09 — Produce the reader-facing Article 2 tutorial

Acceptance criteria:

The article explains:

- why goals are not yet requirements;
- requirement vs preference vs assumption vs decision vs verification;
- how to write a useful acceptance criterion;
- how the repository’s first requirements ledger is structured;
- how later tool comparisons will consume the ledger;
- the real checkpoint-history failure discovered before this increment;
- reproduction and verification steps for the Article 2 repository delta.

## Out of scope

- choosing the final Bun/mise/Moon/Nx authority boundaries;
- installing or configuring build/package/orchestration tooling;
- automating validation of the requirements schema;
- assigning final quantitative performance thresholds before representative workloads exist;
- creating ADRs for tool choices that have not yet been made;
- claiming ISO/IEC 25010 conformance or certification.

## Verification approach

Structural/manual verification against the current repository plus source review of BCP 14 and ISO/IEC 25010:2023 public material. Future increments will progressively replace manual requirement checks with executable evidence.

See `../verification/002-requirements-contract.md` when this increment is complete.
