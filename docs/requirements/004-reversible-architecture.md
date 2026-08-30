# 004 — Reversible Architecture

Status: **Draft**

## Objective

Define a lightweight decision system that lets the repository make consequential architecture choices without pretending those choices are permanent truths.

This increment must formalize the relationship among requirements, assumptions, experiments, ADRs, exit criteria, current-state architecture, verification evidence, and supersession. It must preserve historical rationale while allowing the current architecture to change cleanly when later evidence falsifies an earlier choice.

## Requirements

### REQ-004-01 — Define when an ADR is required

Acceptance criteria:

- the repository distinguishes consequential architecture decisions from ordinary implementation detail;
- an ADR trigger is based on significance/reversibility/cross-cutting effect rather than file size or tool novelty;
- trivial/local decisions do not require ADR ceremony;
- a future maintainer can determine whether a decision should receive an ADR.

### REQ-004-02 — Define an explicit ADR lifecycle

Acceptance criteria:

- ADR statuses and allowed transitions are documented;
- accepted ADRs are preserved as historical records rather than rewritten to match later architecture;
- a superseding ADR identifies the decision it replaces;
- rejected/withdrawn proposals remain distinguishable from decisions that were once accepted architecture.

### REQ-004-03 — Separate current architecture from historical decisions

Acceptance criteria:

- ADRs answer why a decision was made at a point in time;
- current-state architecture documents answer how the repository works now;
- superseding an ADR requires updating affected current-state architecture separately;
- historical ADR text is not treated as the current source of truth merely because it remains in Git.

### REQ-004-04 — Define the role of experiments

Acceptance criteria:

- experiments are evidence-producing investigations rather than decisions by themselves;
- small experiments may remain inside an increment journal;
- reusable, consequential, or multi-decision experiments have a durable standalone record format;
- experiment records distinguish hypothesis/expected result, method, observation, interpretation, and limitations.

### REQ-004-05 — Make assumptions and falsification explicit

Acceptance criteria:

- consequential ADRs state the assumptions on which the decision depends where those assumptions are material;
- ADRs identify evidence or conditions that should force reconsideration when known;
- exit/reconsideration criteria are not presented as proof that the decision is currently wrong;
- Article 3 authority choices can be represented without losing their existing falsification points.

### REQ-004-06 — Trace decisions to requirements and evidence

Acceptance criteria:

- ADRs can cite relevant `UMS-*` product requirements and/or increment requirements;
- ADRs can cite journal entries, standalone experiments, verification records, artifacts, PRs, and commits;
- a decision does not become verified merely because an ADR says it was accepted;
- later verification can close evidence obligations independently of ADR status.

### REQ-004-07 — Keep the system lightweight

Acceptance criteria:

- the mandatory ADR template remains short enough to use for routine consequential decisions;
- optional detail does not become mandatory ceremony;
- no separate governance service, database, or custom DSL is introduced by this increment;
- the system remains usable by a solo developer while scaling to richer review later.

### REQ-004-08 — Define supersession without history rewriting

Acceptance criteria:

- the old ADR remains readable after supersession;
- the new ADR explains what changed and why;
- indexes expose superseded status and replacement relationship;
- current-state architecture follows the new decision rather than requiring readers to infer chronology manually.

### REQ-004-09 — Apply the system to Article 3 authority decisions

Acceptance criteria:

- the Article 3 authority map is converted into one or more actual ADRs or an explicitly justified equivalent decision record;
- the records preserve the distinction between accepted architecture and future implementation proof;
- the repository-owned project identity correction from final Article 3 review is represented correctly;
- existing Article 3 journal/evidence remains referenced rather than duplicated wholesale.

### REQ-004-10 — Produce the reader-facing Article 4 checkpoint

Acceptance criteria:

- the Article 4 article explains reversible architecture, ADR scope, experiment scope, evidence, exit criteria, and supersession;
- the engineering journal records changes from our initial assumptions;
- verification maps every Article 4 increment requirement to durable evidence;
- series/root/index documentation identifies the increment without claiming later implementation proof;
- the checkpoint starts from immutable `main` commit `553f457b01d554429f7117f1e2ca824386835e37`.

## Out of scope

- installing/configuring Bun, mise, Moon, Nx, or GitHub Actions;
- implementing a database-backed architecture knowledge system;
- defining the durable project identity schema deferred by Article 3;
- making every coding decision an ADR;
- formal architecture review boards or mandatory multi-person approvals;
- automated policy enforcement of ADR content before the repository kernel exists;
- proving Article 3 tool choices through executable integration tests.

## Verification approach

Structural/manual verification of the decision lifecycle, templates, indexes, actual ADR application, cross-links, and current-state architecture. Later repository-self-validation work may automate parts of this contract.
