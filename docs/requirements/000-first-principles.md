# 000 — Define the Series and the Meaning of “Ultimate”

Status: **Draft / retrospectively captured after protocol adoption**

## Objective

Define the product we are building before implementation tooling is selected. Establish what “ultimate” must mean in testable engineering terms, what the starter deliberately will not try to be, how provisional architecture hypotheses will be challenged, and what evidence the series must preserve.

## Historical status

The first Article 0 draft and architecture contract were created before the repository's engineering-record protocol was merged to `main`. This requirements file was therefore created while restacking that work onto the new documentation baseline.

It is a reconstruction of the requirements that actually governed the Article 0 work, not a claim that this file existed beforehand.

## Requirements

### REQ-000-01 — Define measurable product properties

The repository must define the engineering properties the finished starter is expected to demonstrate rather than using “ultimate” as an untestable label.

Acceptance criteria:

- required properties are written down;
- properties include reproducibility, explicit authority, determinism where possible, feedback speed, local/CI consistency, governance, polyglot readiness, evolvability, observability, security, AI legibility, and understandability;
- the overall starter has explicit completion criteria.

### REQ-000-02 — Define non-goals

The repository must constrain the design space by identifying things the starter deliberately does not optimize for or promise.

Acceptance criteria:

- non-goals are documented;
- the non-goals reject tool-count theater and untested portability claims;
- the starter does not require every ecosystem to surrender its native authority.

### REQ-000-03 — Separate requirements from provisional tool choices

Early candidate tools must be recorded as hypotheses rather than irreversible requirements.

Acceptance criteria:

- the candidate responsibility map is explicit;
- assumptions have tests or conditions that can force reconsideration;
- future evidence is allowed to change the architecture.

### REQ-000-04 — Define an evidence standard

The series must define how meaningful experiments and failures are recorded.

Acceptance criteria:

- the evidence sequence includes Expected, Tried, Observed, Learned, Changed, and Verified;
- failures are preserved as engineering evidence rather than removed from the narrative.

### REQ-000-05 — Publish an intentional curriculum

The repository must contain a roadmap that takes the starter from first principles through implementation, proof, polyglot expansion, AI-native engineering, and productization/upgrades.

Acceptance criteria:

- the roadmap is present in the repository;
- the roadmap is explicitly allowed to change when evidence changes the dependency order;
- every future article is expected to leave a coherent repository checkpoint.

## Out of scope for this increment

- selecting or installing the package manager;
- pinning runtimes;
- configuring the task orchestrator;
- choosing final directory taxonomy;
- implementing automated repository validation;
- finalizing overlapping tool authorities.

## Verification approach

At this stage there is no repository quality toolchain. Verification is therefore structural/manual and is recorded in `../verification/000-first-principles.md`.
