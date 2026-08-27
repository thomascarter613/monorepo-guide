# Implementing the Engineering Documentation System

We are in an unusually good starting position.

The repository currently has exactly one commit:

```text
c7bfe90d22ebe89f3a03c39590d5de9dc68c2d1c
```

with commit message:

```text
Init
```

and only two empty files:

```text
LICENSE
README
```

That means we can preserve a genuine **zero-state** before doing anything else.

We will implement the system in two layers:

```text
Initial repository
      │
      ▼
series-start tag
      │
      ▼
Documentation infrastructure
      │
      ▼
docs-v1 checkpoint
      │
      ▼
Increment 001
      │
      ▼
Actual monorepo engineering begins
```

I recommend **not** calling the documentation bootstrap `article-001`. It is infrastructure supporting the series rather than the first architectural increment of the monorepo.

---

# Step 1 — Clone the repository

## YOU DO THIS

Open a terminal in the directory where you want the project to live.

Run:

```bash
git clone https://github.com/thomascarter613/monorepo-guide.git
```

Then:

```bash
cd monorepo-guide
```

Now verify the repository:

```bash
git status
```

You should see something similar to:

```text
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

Now run:

```bash
git log --oneline --decorate --all
```

You should see the initial commit beginning with:

```text
c7bfe90 Init
```

---

# Step 2 — Verify that we really are at the untouched starting point

## YOU DO THIS

Run:

```bash
git rev-parse HEAD
```

Expected:

```text
c7bfe90d22ebe89f3a03c39590d5de9dc68c2d1c
```

Then:

```bash
ls -la
```

Ignoring `.git`, the meaningful repository contents should currently just be:

```text
LICENSE
README
```

Do **not** create `package.json`, `pnpm-workspace.yaml`, `.gitignore`, Node configuration, or anything else yet.

Those choices belong to the engineering series and should happen only after we have established the documentation machinery.

---

# Step 3 — Preserve the absolute starting state with a Git tag

This is important.

We want a permanent Git reference meaning:

> This is what the repository looked like before the series changed anything.

Call it:

```text
series-start
```

## YOU DO THIS

Run:

```bash
git tag -a series-start -m "Repository state before the monorepo build begins"
```

Verify:

```bash
git show series-start --stat
```

Then push the tag:

```bash
git push origin series-start
```

From this point onward, no matter how much the repository changes, this command:

```bash
git checkout series-start
```

will reproduce the original repository.

Return to `main` afterward if you tested that:

```bash
git switch main
```

---

# Step 4 — Create a branch for the documentation infrastructure

We do not want to assemble this directly on `main`.

## YOU DO THIS

Run:

```bash
git switch -c docs/engineering-record-system
```

Verify:

```bash
git branch --show-current
```

Expected:

```text
docs/engineering-record-system
```

---

# Step 5 — Create the documentation directory structure

## YOU DO THIS

Run:

```bash
mkdir -p \
  articles \
  docs/architecture \
  docs/artifacts \
  docs/decisions \
  docs/journal \
  docs/requirements \
  docs/templates \
  docs/verification
```

Your repository should now conceptually look like:

```text
monorepo-guide/
├── .git/
├── LICENSE
├── README
├── articles/
└── docs/
    ├── architecture/
    ├── artifacts/
    ├── decisions/
    ├── journal/
    ├── requirements/
    ├── templates/
    └── verification/
```

Git does not track empty directories, so we are now going to create actual files rather than adding `.gitkeep` everywhere.

---

# Step 6 — Create the documentation system's own README

## YOU DO THIS

Create:

```text
docs/README.md
```

Put this in it:

```markdown
# Engineering Documentation

This directory preserves both the current architecture of the repository
and the historical record of how that architecture was developed.

The documentation is deliberately divided by responsibility.

## Current-state documentation

`architecture/`

Describes how the repository works now.

These documents evolve as the architecture changes.

## Requirements

`requirements/`

Defines the problem, constraints, requirements, and acceptance criteria for
each engineering increment before implementation begins.

Requirements describe what must become true, not which technology must be
used to make it true.

## Engineering journal

`journal/`

Preserves the chronological engineering record for each increment.

Journal entries include assumptions, questions, experiments, failures,
observations, corrections, and conclusions.

Journal entries preserve what was known at the time and should not be
retrospectively rewritten to make the engineering process appear cleaner
than it actually was.

## Architecture Decision Records

`decisions/`

Contains Architecture Decision Records (ADRs) for consequential technical
decisions.

Accepted ADRs are historical records. When a decision changes, create a new
ADR that supersedes the previous ADR rather than rewriting history.

## Verification

`verification/`

Records how the requirements for an increment were verified and whether
their acceptance criteria passed.

Where practical, verification should be executable rather than purely
descriptive.

## Artifacts

`artifacts/`

Stores raw evidence that is useful to preserve but too verbose for the
engineering journal, such as command output, benchmark results, generated
reports, dependency graphs, or failure logs.

Artifacts are organized by increment number.

## Article sources

`../articles/`

Contains the reader-facing article produced from the engineering record.

The article is a curated explanation of the work. It is not the canonical
historical record.

## Core principle

Preserve what we knew at the time.

Document what we know now separately.
```

---

# Step 7 — Create the requirement template

Instead of inventing the format every time, establish templates now.

## YOU DO THIS

Create:

```text
docs/templates/requirement.md
```

Put this in it:

```markdown
# NNN — Increment Title

## Status

Proposed

## Objective

Describe the engineering problem this increment is intended to solve.

Do not prescribe an implementation unless the implementation itself is a
requirement.

## Starting state

Git ref:

`<starting-ref>`

Commit:

`<starting-commit>`

## Problem

Describe what is currently missing, insufficient, incorrect, or unknown.

## Requirements

### REQ-NNN-001 — Requirement title

The repository MUST ...

### REQ-NNN-002 — Requirement title

The repository MUST ...

## Constraints

- Constraint
- Constraint

## Non-goals

- Explicitly excluded concern
- Explicitly excluded concern

## Acceptance criteria

### REQ-NNN-001

- [ ] Criterion
- [ ] Criterion

### REQ-NNN-002

- [ ] Criterion
- [ ] Criterion

## Open questions

- Question that must be resolved during the increment.

## References

Journal:

`../journal/NNN-slug.md`

Verification:

`../verification/NNN-slug.md`
```

Notice the language:

```text
MUST
```

for actual requirements.

We'll use:

```text
MUST
SHOULD
MAY
```

deliberately rather than casually.

---

# Step 8 — Create the engineering journal template

## YOU DO THIS

Create:

```text
docs/templates/journal.md
```

Put this in it:

````markdown
# NNN — Increment Title

## Metadata

Status:

`In progress`

Started:

`YYYY-MM-DD`

Completed:

`—`

Starting Git ref:

`<ref>`

Starting commit:

`<sha>`

Ending commit:

`—`

Checkpoint tag:

`—`

## Objective

See:

`../requirements/NNN-slug.md`

## Initial understanding

Record what we believe to be true before implementation starts.

## Assumptions

- Assumption
- Assumption

## Questions

- Question
- Question

## Investigation

### Investigation 1 — Title

#### Question

What are we trying to determine?

#### Method

What did we inspect, research, compare, or execute?

#### Evidence

Record relevant evidence.

#### Observation

What did the evidence tell us?

#### Conclusion

What do we currently conclude?

---

## Experiments

### Experiment 1 — Title

#### Hypothesis

We expect ...

#### Procedure

```text
command or procedure
````

#### Expected

Describe the expected outcome.

#### Actual

Describe the actual outcome.

#### Evidence

Artifact, command output, measurement, or other evidence.

#### Interpretation

What does the result mean?

#### Outcome

Accepted / Rejected / Inconclusive

---

## Failures

### Failure 1 — Title

#### Context

What were we doing when the failure occurred?

#### Command

```text
command
```

#### Expected

What should have happened?

#### Actual

What happened instead?

#### Evidence

```text
relevant error output
```

Full output, if preserved:

`../artifacts/NNN/<artifact>`

#### Hypotheses

1. Possible explanation.
2. Possible explanation.

#### Diagnosis

What actually caused the failure?

#### Correction

What did we change?

#### Verification

How did we prove the correction worked?

#### Lesson

What did this change in our understanding?

---

## Decisions

### Decision — Title

ADR:

`../decisions/NNNN-title.md`

Summary:

Why did this decision become necessary?

---

## Implementation

Record meaningful implementation stages.

Do not duplicate the Git diff line-by-line. Explain important changes and
their purpose.

## Verification

See:

`../verification/NNN-slug.md`

## Final state

### Ending commit

`<sha>`

### Checkpoint tag

`article-NNN`

### Requirements satisfied

* [ ] REQ-NNN-001
* [ ] REQ-NNN-002

## Retrospective

### What changed from our initial assumptions?

...

### What remains unresolved?

...

### What should the next increment investigate?

...

````

This is the file you keep open while engineering.

---

# Step 9 — Create the ADR template

## YOU DO THIS

Create:

```text
docs/templates/adr.md
````

Put this in it:

```markdown
# ADR-NNNN — Decision Title

## Status

Proposed

## Date

YYYY-MM-DD

## Context

Describe the forces that require a decision.

What problem are we solving?

What constraints matter?

## Decision drivers

- Driver
- Driver
- Driver

## Options considered

### Option A — Name

Description.

Advantages:

- Advantage

Disadvantages:

- Disadvantage

### Option B — Name

Description.

Advantages:

- Advantage

Disadvantages:

- Disadvantage

## Decision

We will ...

## Rationale

Explain why this option best satisfies the requirements and decision
drivers.

## Consequences

### Positive

- Consequence

### Negative

- Consequence

### Neutral / trade-offs

- Consequence

## Verification

How can we determine whether this decision produces the intended result?

## Supersedes

None.

## Superseded by

None.

## References

Requirement:

`../requirements/NNN-slug.md`

Journal:

`../journal/NNN-slug.md`

Relevant artifacts:

`../artifacts/NNN/`
```

---

# Step 10 — Establish ADR numbering rules

We need one subtle distinction.

Article/increment numbers and ADR numbers are separate sequences.

Use:

```text
Increment:
001
002
003
...

ADR:
ADR-0001
ADR-0002
ADR-0003
...
```

Why?

Because one engineering increment could produce several decisions:

```text
Increment 004
├── ADR-0007
├── ADR-0008
└── ADR-0009
```

Do **not** force ADR numbers to correspond one-to-one with article numbers.

---

# Step 11 — Create the verification template

## YOU DO THIS

Create:

```text
docs/templates/verification.md
```

Put this in it:

````markdown
# Verification — NNN Increment Title

## Status

Pending

## Increment

Requirement:

`../requirements/NNN-slug.md`

Journal:

`../journal/NNN-slug.md`

## Verification environment

Operating system:

`<value>`

Architecture:

`<value>`

Runtime versions:

```text
<values>
```

Relevant environment information:

```text
<values>
```

## Preconditions

Describe the required starting state.

## Requirement verification

### REQ-NNN-001 — Requirement title

#### Acceptance criteria

* [ ] Criterion
* [ ] Criterion

#### Procedure

```text
commands
```

#### Expected

Describe the expected result.

#### Actual

Describe the observed result.

#### Result

`PASS | FAIL`

#### Evidence

`../artifacts/NNN/<artifact>`

---

### REQ-NNN-002 — Requirement title

#### Acceptance criteria

* [ ] Criterion

#### Procedure

```text
commands
```

#### Expected

...

#### Actual

...

#### Result

`PASS | FAIL`

## Regression verification

Commands:

```text
commands
```

Result:

`PASS | FAIL`

## Overall result

`PASS | FAIL`

## Verified commit

`<sha>`

## Notes

Anything important that is not captured above.

````

---

# Step 12 — Create the article template

The article is deliberately different from the journal.

## YOU DO THIS

Create:

```text
docs/templates/article.md
````

Put this in it:

````markdown
# Article Title

## Series

Building the Ultimate Monorepo Starter from First Principles

## Increment

`NNN`

## Starting point

```bash
git checkout <previous-checkpoint>
````

## Finished state

```bash
git checkout article-NNN
```

## The problem

Introduce the engineering problem from the reader's perspective.

## Requirements

Explain what the repository needs to accomplish before discussing tools.

## What we thought initially

Explain relevant assumptions without pretending we already knew the answer.

## Investigation

Walk through the meaningful alternatives and evidence.

## What we tried

Show the implementation or experiment.

## What failed

Preserve meaningful failures where they contributed to the architecture.

## What the failure taught us

Explain the change in understanding.

## Decision

Explain the resulting architectural decision and link to the ADR.

## Implementation

Walk through the final implementation.

## Verification

Show how the result was tested against the original requirements.

## Final architecture

Explain what is true at the end of this increment.

## What changed

```bash
git diff <previous-checkpoint>..article-NNN
```

## Next

Explain the unresolved problem that naturally leads to the next increment.

````

Do not feel obligated to use every heading in every published article.

The article should read naturally.

The engineering journal is where structural completeness matters.

---

# Step 13 — Create an architecture README

## YOU DO THIS

Create:

```text
docs/architecture/README.md
````

Put this in it:

```markdown
# Architecture

This directory describes the current architecture of the monorepo.

Unlike the engineering journal and accepted ADRs, architecture documents
are current-state documentation and should be updated when the architecture
changes.

Historical reasoning belongs in:

- `../journal/`
- `../decisions/`

The architecture described here should always reflect the current main
branch.
```

---

# Step 14 — Create an ADR index

## YOU DO THIS

Create:

```text
docs/decisions/README.md
```

Put:

```markdown
# Architecture Decision Records

Architecture Decision Records preserve consequential technical decisions.

## Status values

An ADR may have one of the following statuses:

- Proposed
- Accepted
- Rejected
- Deprecated
- Superseded

## Rules

Accepted ADRs are historical records.

Do not rewrite an accepted ADR merely because the architecture later
changes.

Instead, create a new ADR and mark the earlier ADR as superseded.

## Index

| ADR | Decision | Status | Superseded by |
| --- | --- | --- | --- |
```

We'll update this table as decisions are created.

---

# Step 15 — Create the engineering journal README

## YOU DO THIS

Create:

```text
docs/journal/README.md
```

Put:

```markdown
# Engineering Journal

The engineering journal is the chronological laboratory notebook for the
project.

Create one journal entry for every engineering increment.

Journal entries should preserve:

- initial understanding;
- assumptions;
- questions;
- investigations;
- experiments;
- unexpected behavior;
- failures;
- diagnoses;
- corrections;
- decisions;
- verification;
- changes in understanding.

The journal is intentionally more detailed and less polished than the
published articles.

## Historical integrity

Do not rewrite history to make earlier decisions appear obvious.

If an assumption was wrong, preserve the original assumption and document
what disproved it.

If an experiment failed, preserve the failure and document what was learned.

If an architectural decision changes later, preserve the earlier decision
and reference the ADR that supersedes it.
```

---

# Step 16 — Create the requirements README

## YOU DO THIS

Create:

```text
docs/requirements/README.md
```

Put:

```markdown
# Requirements

Each engineering increment begins with a requirements document.

Requirements define the desired behavior and constraints before an
implementation is chosen.

Prefer:

> The workspace MUST support deterministic installation from a clean clone.

over:

> The workspace MUST use pnpm.

The first describes a requirement.

The second prematurely prescribes an implementation.

Technology choices should normally emerge from investigation and be
recorded in Architecture Decision Records.
```

---

# Step 17 — Create the verification README

## YOU DO THIS

Create:

```text
docs/verification/README.md
```

Put:

````markdown
# Verification

Verification records demonstrate whether an increment satisfies its stated
requirements.

Every requirement should have corresponding acceptance criteria and a
recorded verification result.

Where practical, architectural claims should eventually be backed by
executable checks.

The long-term goal is for the repository to expose a command such as:

```bash
pnpm verify
````

that verifies the repository's important invariants automatically.

````

---

# Step 18 — Create the artifacts README

## YOU DO THIS

Create:

```text
docs/artifacts/README.md
````

Put:

````markdown
# Engineering Artifacts

This directory stores raw evidence produced during engineering work.

Examples include:

- terminal output;
- failure logs;
- benchmark results;
- dependency graphs;
- generated reports;
- before/after snapshots;
- diagnostic output.

Create a directory matching the engineering increment:

```text
artifacts/
├── 001/
├── 002/
└── 003/
````

Do not store artifacts merely because they exist.

Preserve evidence that materially supports an investigation, failure,
decision, or verification result.

Never commit credentials, secrets, tokens, private keys, or sensitive
environment data as engineering artifacts.

````

---

# Step 19 — Create the articles README

## YOU DO THIS

Create:

```text
articles/README.md
````

Put:

```markdown
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
```

---

# Step 20 — Create the series manifest

For now I recommend YAML because humans will maintain it frequently.

## YOU DO THIS

Create:

```text
docs/series.yaml
```

Put:

```yaml
series:
  title: "Building the Ultimate Monorepo Starter from First Principles"
  subtitle: "A tutorial, engineering log, architecture journal, and reproducible build from an empty directory to a production-grade polyglot workspace."

repository:
  initial_ref: "series-start"
  initial_commit: "c7bfe90d22ebe89f3a03c39590d5de9dc68c2d1c"

conventions:
  increment_format: "NNN"
  adr_format: "ADR-NNNN"
  checkpoint_format: "article-NNN"

increments: []
```

Do **not** add Increment 001 yet.

We haven't defined it yet.

---

# Step 21 — Add the project's engineering protocol

This is the most important process document.

## YOU DO THIS

Create:

```text
docs/ENGINEERING.md
```

Put:

```markdown
# Engineering Protocol

This repository is built as a sequence of reproducible engineering
increments.

Every significant increment follows the process below.

## 1. Establish starting state

Record:

- starting Git ref;
- starting commit;
- repository state relevant to the increment.

## 2. Define the problem

Explain the capability, limitation, uncertainty, or failure that motivates
the increment.

## 3. Define requirements

Create:

`requirements/NNN-slug.md`

Requirements describe what must become true before choosing an
implementation.

## 4. Start the engineering journal

Create:

`journal/NNN-slug.md`

Record initial assumptions and questions before implementation begins.

## 5. Investigate

Research alternatives and run experiments when necessary.

Record evidence rather than relying on recollection.

## 6. Capture meaningful failures

When observed behavior materially differs from expected behavior, record:

1. context;
2. expected behavior;
3. actual behavior;
4. relevant evidence;
5. hypotheses;
6. diagnosis;
7. correction;
8. verification;
9. lesson.

Capture the evidence before destroying it by applying the fix whenever
practical.

## 7. Record consequential decisions

Create an ADR when a decision has meaningful architectural consequences or
when future maintainers are likely to ask why the choice was made.

## 8. Implement

Implement the smallest solution that satisfies the accepted requirements
and decisions.

## 9. Verify

Create:

`verification/NNN-slug.md`

Verify every requirement against its acceptance criteria.

Prefer executable verification where practical.

## 10. Update current-state architecture

If the increment changes how the repository currently works, update the
relevant documentation under:

`architecture/`

## 11. Complete the journal

Record:

- final conclusions;
- ending commit;
- requirements satisfied;
- remaining questions;
- changes from initial assumptions.

## 12. Create the checkpoint

Once verification passes, create:

`article-NNN`

The tag represents the complete repository state produced by that
increment.

## 13. Write the article

Create:

`../articles/NNN-slug.md`

The article is a curated narrative generated from the engineering record.

It may omit unimportant detail for readability, but it must not falsify the
history preserved by the journal, ADRs, Git history, or verification
evidence.

## Governing principle

Preserve what we knew at the time.

Document what we know now separately.
```

---

# Step 22 — Look at the complete scaffold

## YOU DO THIS

Run:

```bash
find docs articles -type f | sort
```

You should now see:

```text
articles/README.md
docs/ENGINEERING.md
docs/README.md
docs/architecture/README.md
docs/artifacts/README.md
docs/decisions/README.md
docs/journal/README.md
docs/requirements/README.md
docs/series.yaml
docs/templates/adr.md
docs/templates/article.md
docs/templates/journal.md
docs/templates/requirement.md
docs/templates/verification.md
docs/verification/README.md
```

Now run:

```bash
git status --short
```

You should see the new `articles/` and `docs/` files as untracked.

---

# Step 23 — Inspect what we're about to commit

## YOU DO THIS

Run:

```bash
git diff --no-index /dev/null docs/ENGINEERING.md
```

and inspect it.

Then:

```bash
git status
```

Do not commit until you've at least looked through the files.

This establishes an important habit for the series:

> We inspect what we're committing rather than treating Git as a save button.

---

# Step 24 — Commit the documentation infrastructure

If everything looks correct:

## YOU DO THIS

Run:

```bash
git add articles docs
```

Then:

```bash
git status
```

Make sure only the intended documentation files are staged.

Then commit:

```bash
git commit -m "docs: establish engineering record system"
```

---

# Step 25 — Verify the documentation commit

## YOU DO THIS

Run:

```bash
git status
```

Expected:

```text
nothing to commit, working tree clean
```

Then:

```bash
git log --oneline --decorate -2
```

You should have:

```text
<new sha> docs: establish engineering record system
c7bfe90 Init
```

Now record the new SHA:

```bash
git rev-parse HEAD
```

**Save that SHA.**

---

# Step 26 — Tag the documentation-system checkpoint

This is not an article checkpoint.

Call it:

```text
docs-v1
```

## YOU DO THIS

Run:

```bash
git tag -a docs-v1 -m "Initial engineering documentation system"
```

Verify:

```bash
git tag --list
```

You should see:

```text
docs-v1
series-start
```

---

# Step 27 — Push the branch and tags

## YOU DO THIS

Run:

```bash
git push -u origin docs/engineering-record-system
```

Then:

```bash
git push origin docs-v1
```

You already pushed `series-start` earlier.

---

# Step 28 — Merge the documentation infrastructure

At this point you have two reasonable choices:

```text
A. Open a PR and merge it.
B. Merge the branch locally.
```

For this project, use **A**.

Why?

Because eventually the repository should demonstrate the same professional workflow it teaches.

## YOU DO THIS

Open GitHub.

Create a pull request from:

```text
docs/engineering-record-system
```

into:

```text
main
```

Use the title:

```text
docs: establish engineering record system
```

The PR can explain:

```text
Establishes the documentation system used to preserve requirements,
engineering history, architectural decisions, verification evidence, and
reader-facing articles throughout the monorepo series.

No monorepo architecture or tooling decisions are introduced by this
change.
```

Review the Files Changed tab.

Then merge it.

After merging:

## YOU DO THIS LOCALLY

Run:

```bash
git switch main
```

Then:

```bash
git pull --ff-only
```

Then remove the local bootstrap branch:

```bash
git branch -d docs/engineering-record-system
```

You may also delete the remote branch through GitHub after the PR is merged.

---

# Step 29 — Verify the permanent starting references

## YOU DO THIS

Run:

```bash
git log --oneline --decorate --graph --all
```

Conceptually you should now have:

```text
main
 │
 ● documentation infrastructure
 │
 ● Init
   ↑
 series-start
```

You should also have:

```text
series-start
docs-v1
```

Check:

```bash
git tag --list
```

---

# Step 30 — Now begin Increment 001

Only now do we begin engineering the actual monorepo.

Do **not** create `package.json` yet.

First create the engineering record for Increment 001.

Our likely first problem should be:

> Define the repository foundation and development environment required for a reproducible monorepo.

But we will derive its actual requirements before deciding things such as:

```text
Node version
package manager
Corepack
pnpm
workspace structure
Turborepo
TypeScript
```

Those are **possible answers**, not requirements.

---

# Step 31 — Create the Increment 001 branch

## YOU DO THIS

From updated `main`:

```bash
git switch -c article/001-repository-foundation
```

Record the exact starting commit:

```bash
git rev-parse HEAD
```

Call the resulting SHA:

```text
START_SHA
```

Do not literally put `START_SHA` in the documentation. Use the actual SHA the command returns.

---

# Step 32 — Create the Increment 001 requirement document

## YOU DO THIS

Copy the template:

```bash
cp \
  docs/templates/requirement.md \
  docs/requirements/001-repository-foundation.md
```

Then open:

```text
docs/requirements/001-repository-foundation.md
```

At this point, **stop before writing implementation choices**.

We are going to define together:

```text
Objective
Problem
Requirements
Constraints
Non-goals
Acceptance criteria
Open questions
```

before creating any monorepo files.

---

# Step 33 — Create the Increment 001 journal

## YOU DO THIS

Run:

```bash
cp \
  docs/templates/journal.md \
  docs/journal/001-repository-foundation.md
```

Open it.

Fill in only:

```text
Status
Started
Starting Git ref
Starting commit
Objective
Initial understanding
Assumptions
Questions
```

Leave:

```text
Investigation
Experiments
Failures
Decisions
Implementation
Verification
Final state
Retrospective
```

unfinished.

That incompleteness is intentional.

We're documenting the process while it happens.

---

# Step 34 — Create an artifact directory for Increment 001

## YOU DO THIS

Run:

```bash
mkdir -p docs/artifacts/001
```

Because Git will not track an empty directory, do not commit anything there yet.

The directory becomes relevant as soon as we have evidence worth preserving.

---

# Step 35 — Add Increment 001 to `series.yaml`

Eventually it will look approximately like this:

```yaml
increments:
  - id: "001"
    slug: "repository-foundation"
    title: "Repository Foundation"
    status: "in-progress"
    starting_ref: "docs-v1"
    starting_commit: "<actual SHA>"
    ending_commit: null
    checkpoint: null
    requirements:
      - "REQ-001-001"
    decisions: []
    article: "articles/001-repository-foundation.md"
```

But **do not invent the requirement IDs yet**.

We'll populate this after defining Increment 001's requirements.

---

# Step 36 — The ongoing workflow from this point forward

Once Increment 001 is started, your working loop becomes:

```text
Read requirement
       ↓
Choose next question
       ↓
Record expectation
       ↓
Run command / make change
       ↓
Observe result
       │
       ├── expected ────────────────┐
       │                            │
       └── unexpected               │
              ↓                     │
        capture evidence            │
              ↓                     │
           diagnose                 │
              ↓                     │
          correction                │
              ↓                     │
         verify correction          │
                                    ↓
                          update journal
                                    ↓
                         next engineering step
```

The critical operational rule is:

> **When something surprising happens, don't immediately fix it. Capture it first.**

For example, suppose later we run:

```bash
pnpm install
```

and get an unexpected error.

Do not immediately Google it, change files, rerun commands twenty times, and then try to reconstruct what happened.

Instead:

```bash
pnpm install > docs/artifacts/001/pnpm-install-failure.txt 2>&1
```

Then record in the journal:

```text
Expected
Actual
Environment
Evidence
Initial hypotheses
```

Then investigate.

That is how we get the authentic engineering record you want.

---

# Step 37 — When an architectural decision appears

Suppose our investigation eventually demonstrates that pnpm best satisfies the package-management requirements.

Only then do we create:

```text
docs/decisions/0001-use-pnpm.md
```

by copying:

```bash
cp \
  docs/templates/adr.md \
  docs/decisions/0001-use-pnpm.md
```

Then the relationship becomes:

```text
REQ-001-xxx
      │
      ▼
Investigation
      │
      ▼
Journal evidence
      │
      ▼
ADR-0001
      │
      ▼
Implementation
```

This prevents us from deciding:

> "We're building a pnpm/Turborepo monorepo."

before establishing whether those tools are actually justified.

---

# Step 38 — At the end of every increment

Eventually, when Increment 001 is implemented:

## YOU DO THIS

Run all verification.

Complete:

```text
docs/verification/001-repository-foundation.md
```

Update:

```text
docs/journal/001-repository-foundation.md
```

Update current-state documentation under:

```text
docs/architecture/
```

Update:

```text
docs/series.yaml
```

Then inspect:

```bash
git diff
```

Then commit the final increment.

For example:

```bash
git add .
git commit -m "feat: establish repository foundation"
```

Run verification against the exact committed state.

Then:

```bash
git status
```

It must be clean.

Then:

```bash
git rev-parse HEAD
```

Put that exact SHA in the journal and verification record if it is not already captured.

If adding the SHA itself changes the commit, use the checkpoint tag as the canonical immutable reference rather than trying to create a self-referential commit SHA.

Finally:

```bash
git tag -a article-001 -m "Article 001: Repository Foundation"
```

Push:

```bash
git push origin article-001
```

Then the reader can reproduce the state with:

```bash
git checkout article-001
```

And once `article-002` exists:

```bash
git diff article-001..article-002
```

shows exactly what the second article introduced.

---

# Step 39 — One process rule I want us to follow

From here onward, **do not make architectural repository changes ahead of the documentation**.

For each increment, the order is:

```text
1. Identify problem
2. Create requirement document
3. Create journal
4. Record starting assumptions
5. Investigate
6. Record evidence
7. Make decision
8. Create ADR if warranted
9. Implement
10. Test
11. Verify requirements
12. Update architecture docs
13. Complete journal
14. Commit
15. Create checkpoint tag
16. Write article
```

That is how we prevent retrospective reconstruction.

---

# Your immediate action

Do **Steps 1 through 3 only** right now:

```bash
git clone https://github.com/thomascarter613/monorepo-guide.git
cd monorepo-guide

git status
git log --oneline --decorate --all
git rev-parse HEAD

git tag -a series-start -m "Repository state before the monorepo build begins"
git push origin series-start
```

Then send me the output of:

```bash
git status
git log --oneline --decorate --all
git tag --list
```

**Do not create any other files yet.**

Once those three checks are done, the original state is permanently preserved, and we can implement the documentation scaffold on top of it without losing the true beginning of the project.
