# Verification — 002 Requirements Before Tools

Status: **PASS for the Article 2 documentation/architecture increment**

Date: 2026-08-27

## Scope

This verification checks the requirements in:

`../requirements/002-requirements-contract.md`

The increment intentionally does not install a repository quality toolchain. Verification is therefore structural/manual plus source review and Git history inspection. Later increments are expected to turn many product requirements introduced here into executable checks.

## Starting state

Base branch: `main`

Starting commit:

`3e1c4161f3eb9d0c385430bd32d28a18b91516c2`

Working branch:

`series/02-requirements-contract`

At the pre-verification inspection, the branch was 9 commits ahead and 0 behind `main`, with nine intended changed paths. The verification record and final metadata updates necessarily add further commits/paths after that observation; the final PR diff is the authoritative review surface.

## External references checked

### BCP 14

Reviewed:

- https://www.rfc-editor.org/info/rfc2119/
- https://www.rfc-editor.org/info/rfc8174/

Observed:

- RFC 2119 defines common requirement-level keywords including MUST, SHOULD, and MAY;
- RFC 8174 updates the convention to clarify that the special meanings apply to uppercase forms;
- the requirements ledger explicitly adopts those semantics only for uppercase normative keywords and does not claim to be an IETF specification.

### ISO/IEC 25010:2023

Reviewed public ISO material:

- https://www.iso.org/standard/78176.html

Observed:

- ISO/IEC 25010:2023 is the published second edition of the SQuaRE product quality model;
- the public abstract describes nine product-quality characteristics;
- the public abstract describes uses including defining requirements, validating requirement comprehensiveness, identifying testing objectives, acceptance criteria, and quality measures;
- the Article 2 materials use the standard as a completeness reference and explicitly do **not** claim certification or conformance.

## Product-ledger structural inspection

File:

`../architecture/requirements-ledger.md`

Observed:

- living product requirements are separated from historical increment requirements;
- the ledger defines explicit evidence states: Accepted, Partial, Verified, Superseded, Rejected;
- tool brands are explicitly excluded from requirement statements merely because they are current preferences;
- the ledger contains 36 stable product requirement IDs across 12 concern groups;
- each requirement contains a class/state, normative statement, acceptance criteria, and planned/current evidence;
- the ledger includes known gaps rather than fabricating unsupported quantitative targets;
- requirement `UMS-EVD-001` captures immutable checkpoint evidence after the real Article 0/1 stacked-branch finding;
- requirement `UMS-EVD-002` establishes requirement-to-evidence traceability.

## Requirement verification

### REQ-002-01 — Separate product requirements from increment requirements

Result: **PASS**

Evidence:

- historical increment requirement: `../requirements/002-requirements-contract.md`;
- living product contract: `../architecture/requirements-ledger.md`;
- `../requirements/README.md` documents the distinction;
- `../architecture/README.md` identifies the living ledger as current-state architecture.

### REQ-002-02 — Give product requirements stable identities

Result: **PASS**

Evidence:

The ledger uses concern-based identifiers such as:

- `UMS-ENV-002`;
- `UMS-WSP-003`;
- `UMS-EXE-004`;
- `UMS-GEN-002`;
- `UMS-SEC-003`;
- `UMS-AI-003`;
- `UMS-EVD-001`.

No product requirement uses Bun/mise/Moon/Nx as its identity.

### REQ-002-03 — Make requirements testable

Result: **PASS for the contract design**

Evidence:

- accepted product requirements include observable acceptance criteria;
- where representative workloads do not exist, the ledger specifies future evidence obligations instead of fabricated thresholds;
- `UMS-OBS-002` explicitly requires measurements before performance/cost claims are accepted.

Note:

This result verifies that the requirements are structured to be testable. It does **not** claim the future product requirements themselves are already satisfied.

### REQ-002-04 — Separate requirements, preferences, assumptions, and decisions

Result: **PASS**

Evidence:

- the ledger has a dedicated “What does not belong here” section;
- Bun, mise, Moon, Nx, Biome, Vitest, Playwright, and GitHub Actions are identified as candidate implementations rather than product requirements;
- Article 2 explains requirement vs preference vs assumption vs decision vs evidence using separate examples;
- tool-authority choices remain deferred to Article 3.

### REQ-002-05 — Define requirement status and evidence semantics

Result: **PASS**

Evidence:

The ledger defines:

- Accepted;
- Partial;
- Verified;
- Superseded;
- Rejected.

It explicitly states that implementation is not synonymous with verification and describes evidence references including verification records, tests, measurements, artifacts, commits, and PRs.

### REQ-002-06 — Cover the whole starter lifecycle

Result: **PASS for v0.1 scope**

Evidence:

The 12 requirement groups cover:

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

The ledger also records known gaps rather than claiming total completeness.

### REQ-002-07 — Use external standards as references, not borrowed authority

Result: **PASS**

Evidence:

- BCP 14 adoption is precisely scoped to uppercase normative-keyword semantics;
- Article 2 and the ledger state that the project is not claiming to be an IETF specification;
- ISO/IEC 25010:2023 is used as a completeness/quality prompt;
- both documents explicitly reject an unsupported ISO conformance/certification claim.

### REQ-002-08 — Preserve the stacked-checkpoint failure as evidence

Result: **PASS**

Evidence:

`../journal/002-requirements-contract.md` records:

- context;
- expected behavior;
- attempted stacked PR flow;
- observed branch advancement;
- lesson that branch identity is mutable;
- resulting requirement `UMS-EVD-001`;
- Article 2 branching directly from accepted current `main`.

`../../articles/README.md` now records immutable checkpoint commit SHAs for Articles 0 and 1.

`../series.yaml` also records those checkpoint commits separately from branch names.

### REQ-002-09 — Produce the reader-facing Article 2 tutorial

Result: **PASS**

Evidence:

`../../articles/02-requirements-before-tools.md` includes:

- requirement vs goal/preference/assumption/decision/evidence;
- normative language and acceptance-criteria guidance;
- the living requirements-ledger design;
- examples that later tool comparisons can consume;
- the real stacked-checkpoint failure;
- repository reproduction and structural verification steps;
- deliberately unresolved gaps and next-step authority selection.

## Regression / scope checks

Observed:

- no package manager, task runner, formatter, compiler, or CI implementation was introduced by this increment;
- Article 3 authority decisions remain unresolved;
- the existing Article 0 definition and Article 1 system model remain intact;
- root/architecture/requirements indexes now point readers to the living ledger;
- `docs/series.yaml` records Articles 0/1 as merged and Article 2 as draft.

## Overall result

**PASS**

Article 2 satisfies its increment requirements at the level of verification currently available to the repository.

This PASS does **not** mean the 36 living product requirements are all satisfied. Most are intentionally Accepted rather than Verified and will accumulate evidence through later implementation increments.

That distinction is one of the primary outputs of this article.
