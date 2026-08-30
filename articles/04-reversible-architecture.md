# Reversible Architecture: ADRs, Experiments, and Exit Criteria

*Building the Ultimate Monorepo Starter from First Principles — Article 4*

*A tutorial, engineering log, architecture journal, and reproducible build from an empty directory to a production-grade polyglot workspace.*

---

In Article 3, we finally made architecture decisions.

We gave different concerns to different authorities:

- mise for executable/tool versions;
- Bun for JavaScript package/workspace state;
- the repository for durable project identity;
- Moon for operational project/task/affected/cache semantics;
- Nx for generators and migrations;
- GitHub Actions for hosted CI orchestration;
- the repository again for the stable root command interface.

And then we did something more important than choosing tools.

We wrote down what would make us change our minds.

That creates a new problem.

If architecture is intentionally falsifiable, how do we preserve a decision without turning it into dogma?

How do we say:

> This is the architecture we have chosen.

while also saying:

> This is not eternal truth.

That is what Article 4 solves.

We need a decision system that can preserve three things at once:

1. what we decided;
2. why the decision was reasonable at the time;
3. how future evidence is allowed to replace it.

The answer is not more prose inside `architecture.md`.

And it is not a giant governance database.

For this repository, the answer is a deliberately small combination:

```text
requirements
    ↓
engineering journal / experiments
    ↓
ADR
    ↓
current-state architecture
    ↓
implementation + verification
    ↓
new evidence
    ↓
new ADR supersedes old ADR
```

The key word is **supersedes**.

Not edits.

Not erases.

Not “we always meant this.”

Supersedes.

# The repository state at the start

Article 4 begins from accepted `main` at:

```text
553f457b01d554429f7117f1e2ca824386835e37
```

That commit is after Article 3 was merged and its accepted checkpoint metadata was normalized on `main`.

Create the working branch from that exact state:

```bash
git switch main
git pull --ff-only
git switch -c series/04-reversible-architecture
```

The increment begins, as usual, with intent and history rather than implementation:

```text
docs/requirements/004-reversible-architecture.md
docs/journal/004-reversible-architecture.md
```

At this point we still have no installed Bun/mise/Moon/Nx monorepo implementation stack.

That is intentional.

Article 4 is finishing the architecture-decision machinery before the repository kernel starts turning these decisions into executable configuration.

# We already had ADRs—sort of

The repository already contained:

```text
docs/decisions/README.md
```

and our methodology already said something sensible:

> Accepted ADRs are historical records. When the architecture changes, create a new ADR rather than rewriting the old one.

That was directionally correct.

But it left several questions unanswered.

What actually deserves an ADR?

What is the difference between an experiment and a decision?

Can an accepted ADR be edited later?

What does `Deprecated` mean for a decision?

When do we allocate an ADR number?

Where do exit criteria live?

Does an accepted ADR mean the underlying requirement is verified?

How do we update the current architecture after a decision changes?

And—most importantly—Article 3 already contains real decisions. If Article 4 only writes a template and never applies it to those choices, then we have built process documentation without proving the process is usable.

So this increment has two jobs:

```text
1. define the decision lifecycle;
2. use it on the architecture we already chose.
```

# What an ADR is actually for

A common definition of an Architecture Decision Record is refreshingly small:

> a record of an important architecture decision together with its context and consequences.

That framing appears throughout the established ADR literature and template ecosystem.

We reviewed two useful references while designing our version:

```text
https://github.com/architecture-decision-record/architecture-decision-record
https://github.com/adr/madr
```

MADR is especially interesting because it gives decision records explicit options/outcome structure while remaining plain Markdown.

But we do **not** need to copy a generic ADR template wholesale.

Our repository already has artifact types that many generic ADR templates have to absorb themselves:

```text
requirements
engineering journals
raw artifacts
verification records
current-state architecture
Git checkpoints
articles
```

That lets our ADR stay small.

The ADR does not need to become the laboratory notebook.

It only needs to answer:

```text
What problem forced a decision?
What did we decide?
Why?
What consequences did we accept?
What evidence supported it?
What assumptions does it depend on?
What should make us reconsider it?
```

That last question is the repository-specific addition that matters most.

# The future-maintainer test

We need a rule for deciding when an ADR is worth creating.

The wrong rule is:

> Every technical decision gets an ADR.

That produces documentation theater.

We also do not want:

> Only enormous architecture changes get ADRs.

That loses the decisions most likely to surprise a future maintainer.

Our test is:

> **Would a future maintainer reasonably need to know why the repository is constrained this way and what would justify changing it?**

Strong ADR triggers include decisions that:

- assign architectural authority or source-of-truth responsibility;
- define a repository-wide boundary or invariant;
- introduce a foundational tool whose replacement has migration cost;
- define a stable human/CI/agent/package/schema/release interface;
- establish a security or trust boundary;
- choose a durable data/schema/migration strategy;
- affect many projects or languages;
- materially constrain future design;
- reject a plausible alternative for architectural reasons;
- would otherwise be repeatedly relitigated because the rationale is invisible.

Things that normally do **not** need ADRs include:

- local implementation details;
- formatting and naming choices without durable consequences;
- mechanical refactors;
- temporary experiments;
- routine updates that do not change the architecture;
- every file written to implement an already-recorded decision.

This is intentionally judgment-based.

We are building an engineering record, not a decision-ticket quota.

# Decision status and evidence status are different systems

This distinction may be the most important one in the article.

Suppose we write:

```text
ADR-0006 — Use Moon as operational project/task authority
Status: Accepted
```

Does that mean:

```text
UMS-EXE-001 — One authoritative repository task graph
State: Verified
```

No.

Not even close.

The ADR answers:

> Which architecture have we chosen to implement and test?

The requirement state answers:

> How much evidence do we have that the product promise is actually satisfied?

So this is perfectly legitimate:

```text
ADR-0006: Accepted
UMS-EXE-001: Partial
```

Why?

Because Article 3 gave us enough evidence to choose Moon provisionally as the task authority.

But we have not yet proved:

- real task definitions;
- root command delegation;
- local/CI parity;
- affected correctness;
- cache invalidation correctness;
- Nx isolation from routine execution;
- Rust/Python behavior.

Decision and verification are different axes.

If we collapse them, architecture prose becomes fake evidence.

# The ADR lifecycle

Our first draft inherited statuses including:

```text
Proposed
Accepted
Rejected
Deprecated
Superseded
```

The problem was `Deprecated`.

Deprecation makes sense for APIs and artifacts that remain temporarily supported while callers migrate.

For an architecture decision, however, it creates a fuzzy state:

> Is this still the architecture or not?

We do not need that ambiguity.

Article 4 therefore defines five states:

```text
Proposed
Accepted
Rejected
Withdrawn
Superseded
```

The lifecycle is:

```text
Proposed ──► Accepted ──► Superseded
   │
   ├──────► Rejected
   │
   └──────► Withdrawn
```

The difference between `Rejected` and `Withdrawn` is useful.

`Rejected` means:

> We considered this proposal and deliberately chose not to adopt it.

`Withdrawn` means:

> We stopped pursuing the proposal before we needed/reached a decision.

That preserves useful history without pretending every abandoned idea lost a formal architecture contest.

# Allocate the ADR ID while it is proposed

Another question was when to assign the number.

We could wait until acceptance.

That looks tidy, but it creates an awkward evidence problem.

While investigating a proposed decision, we want journals, experiments, PR discussions, and artifacts to refer to it consistently.

So we allocate the ID when the proposal is created:

```text
ADR-0017 — Proposed
```

Then evidence can say:

```text
EXP-0006 tests assumption A in ADR-0017.
```

If the decision is rejected, `ADR-0017` remains a rejected record.

We accept gaps/non-accepted ADR numbers as part of honest history.

The number is identity, not a trophy awarded only to accepted decisions.

# Accepted ADRs are immutable—with one narrow exception

We keep saying accepted ADRs are historical records.

But supersession itself creates a paradox.

If `ADR-0006` is accepted today and `ADR-0031` replaces it next year, how can readers tell that `ADR-0006` is no longer current without modifying the file?

Our rule is:

> **Substantive historical content freezes at acceptance. Supersession metadata may be added later.**

After acceptance, do not rewrite:

- context;
- decision;
- alternatives;
- rationale;
- consequences;
- assumptions;
- original evidence interpretation;
- original reconsideration criteria.

Later we may change:

```text
Status: Accepted
```

to:

```text
Status: Superseded
Superseded by: ADR-0031
```

We may also repair a broken repository link or an obvious typo that cannot change meaning.

That is metadata maintenance, not historical rewriting.

The new ADR explains the changed architecture.

# Current-state architecture is not an ADR log

This gives us a clean separation.

Imagine that Article 41 proves Moon is a poor fit for a critical polyglot case and we replace it.

The history might become:

```text
ADR-0006
Use Moon as operational project/task authority
Status: Superseded
Superseded by: ADR-0031

ADR-0031
Use <replacement> as operational project/task authority
Status: Accepted
```

But a developer trying to understand the repository today should not have to reconstruct that chain before running a command.

The current architecture document should simply say what is true now.

For this concern:

```text
docs/architecture/authority-map.md
```

would be updated to the replacement authority.

So:

```text
ADRs              -> why architecture changed over time
architecture/     -> what architecture is now
```

Both are necessary.

Neither should impersonate the other.

# Experiments are not decisions

We also need a place for evidence that is too structured or reusable to live comfortably inside one journal section.

That does **not** mean every command trial deserves a new file.

Most experimentation should still look like this:

```text
docs/journal/018-task-runner.md

Experiment — change global config
Expected: all dependent targets become affected
Observed: ...
Learned: ...
```

The journal is our default laboratory notebook.

A standalone experiment becomes useful when:

- multiple ADRs/increments may cite it;
- it compares consequential alternatives;
- reproducing it requires important controlled setup;
- it is expensive, benchmark-sensitive, or security-sensitive;
- it tests an ADR assumption or exit criterion directly;
- raw evidence needs a stable semantic identity.

Those records live under:

```text
docs/experiments/
```

and use:

```text
EXP-NNNN
```

# Do not use Accepted/Rejected for experiments

This sounds minor, but language shapes thinking.

An experiment is not architecture.

So experiment statuses are:

```text
Planned
Running
Concluded
Abandoned
```

Not:

```text
Accepted
Rejected
```

A hypothesis may be supported or weakened.

An observation may pass or fail an explicitly defined criterion.

But the experiment itself does not get to decide the architecture.

Humans and the repository's decision process interpret evidence against requirements and constraints.

# Separate observation from interpretation

The experiment template makes another distinction explicit:

```text
Expected observation
Actual observation
Interpretation
Limitations
```

Why separate `Observed` from `Interpretation`?

Because this:

```text
Moon took 1.3 seconds on workload W.
```

is not the same statement as:

```text
Moon is too slow for this repository.
```

The first is an observation under stated conditions.

The second is a judgment that also depends on:

- acceptable threshold;
- competing tools;
- cold vs warm execution;
- hardware;
- repository size;
- value provided by the orchestration;
- repeatability.

Keeping evidence and interpretation separate helps future maintainers reinterpret old evidence under new constraints without rewriting what was measured.

# Exit criteria belong with the decision

Article 3 already did something valuable accidentally before Article 4 formalized it.

For each authority choice, the authority map included statements such as:

```text
Reconsider Moon if Python integration requires unacceptable workarounds.

Reconsider Nx if generator use requires routine Nx task execution.

Reconsider mise if Moon cannot consume environment-provided tools safely.
```

Those are more than caveats.

They are architecture **reconsideration criteria**.

We make them mandatory in the ADR template.

That does not mean every decision can predict its eventual failure.

Sometimes the honest section will say:

```text
No concrete reconsideration criterion is currently known beyond a material
change in the requirements or constraints.
```

That is better than silently omitting the question.

The purpose is to prevent this transformation:

```text
current choice
    ↓
configuration exists
    ↓
team gets used to it
    ↓
choice becomes tradition
    ↓
tradition becomes "architecture"
    ↓
no one remembers what evidence could change it
```

# Exit criteria do not automatically reverse decisions

Suppose an ADR says:

> Reconsider Moon if Python integration remains materially second-class.

Then a Python experiment exposes serious friction.

Do we automatically uninstall Moon?

No.

The criterion means:

```text
new evidence has crossed the threshold where this decision deserves review
```

That review may conclude:

- the problem is fixable;
- the requirement should change;
- the trade-off is still worthwhile;
- another tool is now better;
- our project model was wrong;
- Moon should be superseded.

Exit criteria trigger engineering work.

They do not bypass it.

# One giant Article 3 ADR would be a mistake

Once the lifecycle was defined, we had to apply it to Article 3.

The tempting record would be:

```text
ADR-0002: Use mise + Bun + Moon + Nx + GitHub Actions
```

That would be terrible architecture history.

Why?

Because the choices have different reasons and different failure conditions.

We may discover:

- mise is wrong but Moon is right;
- Bun is wrong but the Nx boundary is right;
- Nx is unnecessary but the package/task split is sound;
- GitHub Actions is replaced while every repository authority remains unchanged.

An umbrella ADR would couple unrelated reversibility.

So Article 4 produces independent decisions.

# The first ADR set

The accepted decision index now begins with:

```text
ADR-0001
Adopt lightweight reversible architecture decision records

ADR-0002
Assign one authority per repository correctness concern

ADR-0003
Use mise as executable/tool version authority

ADR-0004
Use Bun as JavaScript package/workspace authority

ADR-0005
Keep durable project identity repository-owned

ADR-0006
Use Moon as operational project/task/affected/cache authority

ADR-0007
Restrict Nx authority to repository transformations

ADR-0008
Use GitHub Actions for hosted CI orchestration only

ADR-0009
Keep the stable root command interface repository-owned
```

These do not replace:

```text
docs/architecture/authority-map.md
```

They explain why its important boundaries exist.

# ADR-0005 preserves the Article 3 review correction

One record deserves special attention.

The first Article 3 authority map draft said, effectively:

```text
repository project identity / project graph -> Moon
```

During final review we caught the coupling problem.

Article 1 already defined a project independently of Moon, Nx, Bun, or `package.json`.

So the accepted architecture became:

```text
durable project identity -> repository-owned
operational graph         -> Moon
```

Article 4 records that explicitly as:

```text
ADR-0005 — Keep durable project identity repository-owned
ADR-0006 — Use Moon as operational project/task authority
```

That split is a direct example of why independently reversible ADRs matter.

If Moon disappears, `ADR-0006` may be superseded.

`ADR-0005` may survive untouched.

# We deliberately do not invent the project identity schema yet

ADR-0005 says **who owns the concern**.

It does not say the representation must be:

```text
project.yaml
.repo/projects.toml
JSON-LD
SQLite
UUIDv7
URI identifiers
```

We have not earned that decision.

The repository-kernel increment is where the smallest useful project contract should be designed.

This is another useful property of ADR scope:

> decide only what the evidence requires us to decide now.

Reversibility improves when we resist smuggling future implementation into today's decision.

# No standalone experiment yet

Article 4 creates:

```text
docs/experiments/README.md
docs/experiments/EXP-TEMPLATE.md
```

but it creates **no actual `EXP-0001`**.

That is intentional.

Article 3's capability work was first-party documentation research, already preserved as:

```text
docs/artifacts/003-authority-capability-evidence.md
```

Article 4 itself is defining the process, not running a controlled executable comparison.

Creating an empty/artificial experiment solely because we now have an experiment directory would violate the lightweight rule we just established.

The first real standalone experiment should appear when the engineering work actually earns one.

Likely candidates include:

- Moon affected correctness fixtures;
- cache invalidation experiments;
- task-runner comparisons;
- fresh-environment reproducibility runs;
- CI cache economics;
- generator determinism;
- Rust/Python polyglot falsification tests.

# We also do not fake a superseded ADR

The product ledger already contains:

```text
UMS-GEN-003 — Consequential architecture changes are traceable
State: Partial
```

One remaining evidence obligation is:

```text
first real superseded ADR/migration
```

Article 4 gives us the machinery to satisfy that later.

We could manufacture a trivial decision, supersede it immediately, and declare victory.

We will not.

That would optimize the evidence state instead of the engineering objective.

So `UMS-GEN-003` remains Partial.

The first genuine architecture reversal will close that proof when it actually happens.

This is the same discipline we want AI agents to follow later:

> do not game the metric; satisfy the underlying requirement.

# The complete decision/evidence model

We can now draw the architecture-process model more precisely.

```text
                 CURRENT PRODUCT CONTRACT
                        requirements
                            │
                            ▼
                    problem / uncertainty
                            │
                ┌───────────┴───────────┐
                ▼                       ▼
        engineering journal      standalone experiment
                │                       │
                └───────────┬───────────┘
                            ▼
                     ADR (Proposed)
                            │
                 evaluate evidence/tradeoffs
                            │
             ┌──────────────┼──────────────┐
             ▼              ▼              ▼
          Accepted       Rejected       Withdrawn
             │
             ▼
     current-state architecture
             │
             ▼
       implementation
             │
             ▼
        verification
             │
             ▼
      operational evidence
             │
             ▼
new evidence / violated assumption / exit trigger
             │
             ▼
       new investigation
             │
             ▼
        new ADR accepted
             │
             ├── old ADR -> Superseded
             │
             └── update current architecture
```

There is no single document that owns all of this.

That is a feature.

Each artifact owns one kind of truth.

# The minimum ADR is intentionally small

Our template contains:

```text
Identity/title
Status/date
Context
Decision
Rationale
Consequences
Requirements
Evidence
Assumptions
Reconsideration criteria
Relationships
```

That looks like a lot of headings.

In practice, a good ADR can still be short.

The expensive analysis should usually live elsewhere.

For example, `ADR-0006` does not need to reproduce every Moon capability we researched.

It links to:

```text
docs/artifacts/003-authority-capability-evidence.md
docs/journal/003-authority-boundaries.md
docs/architecture/authority-map.md
```

The ADR is the decision node in the evidence graph—not the entire graph flattened into one Markdown file.

# A proposed future supersession

We do not have a real supersession yet, but we can define exactly what one should look like.

Imagine later evidence produces:

```text
EXP-0012
Python polyglot orchestration comparison
```

and that evidence causes us to replace Moon.

We would create:

```text
ADR-0031: Use X as operational project/task authority
Status: Proposed
Supersedes: ADR-0006
```

After review:

```text
ADR-0031: Accepted
```

Then we make the narrow allowed metadata update to the old record:

```text
ADR-0006
Status: Superseded
Superseded by: ADR-0031
```

We do **not** rewrite `ADR-0006` to say Moon was obviously a mistake.

Maybe it was the best choice given the evidence available in August 2026.

The new ADR explains what changed.

Then:

```text
docs/architecture/authority-map.md
```

moves to the new current state.

The migration implementation and verification happen after the decision.

That is reversible architecture without historical amnesia.

# Why this matters for AI-assisted engineering

This series is AI-native but not AI-dependent.

Decision records become more important—not less—as AI generation accelerates.

An agent can cheaply propose:

- a new dependency;
- a cache layer;
- a task runner;
- a repository restructure;
- a different build strategy;
- a new permission model.

The danger is not that the proposal exists.

The danger is that implementation speed silently converts a proposal into architecture.

Our lifecycle inserts explicit semantic boundaries:

```text
proposal ≠ decision
experiment ≠ decision
decision ≠ verification
implementation ≠ verification
accepted today ≠ permanent forever
```

Those distinctions create better handoff state for humans and agents alike.

An agent can be told:

> ADR-0006 is accepted. You may implement within it, but you may not silently replace the operational task authority. If evidence suggests an exit criterion has been crossed, open a decision proposal instead.

That is much safer than relying on the agent to infer unwritten architectural tradition from existing configuration.

# Reproducing the Article 4 checkpoint

Start from:

```bash
git checkout 553f457b01d554429f7117f1e2ca824386835e37
```

Inspect the starting decision directory:

```bash
find docs/decisions -maxdepth 1 -type f -print | sort
```

At the start, the directory has only its earlier lightweight index.

The Article 4 branch adds the decision system, templates, experiment convention, and actual ADRs.

Inspect the resulting architecture/process files:

```bash
cat docs/architecture/decision-system.md
cat docs/decisions/README.md
cat docs/decisions/ADR-TEMPLATE.md
cat docs/experiments/README.md
cat docs/experiments/EXP-TEMPLATE.md
```

List actual decisions:

```bash
find docs/decisions -maxdepth 1 -name 'ADR-[0-9][0-9][0-9][0-9]-*.md' -print | sort
```

Expected Article 4 decision IDs:

```text
ADR-0001
ADR-0002
ADR-0003
ADR-0004
ADR-0005
ADR-0006
ADR-0007
ADR-0008
ADR-0009
```

Verify the deliberate absence of a fake standalone experiment:

```bash
find docs/experiments -maxdepth 1 -name 'EXP-[0-9][0-9][0-9][0-9]-*.md' -print
```

Expected result for this increment:

```text
(no experiment records)
```

That is not missing work.

It is evidence that our process did not create artifacts merely because a template existed.

# What Article 4 does not prove

Article 4 proves a documentation/architecture process.

It does **not** prove:

- mise can reproduce the final supported toolchain;
- Bun's workspace behavior satisfies our future package fixtures;
- Moon's affected/cache semantics are correct;
- Nx can remain transformation-only in a real generator;
- GitHub Actions and local commands are actually equivalent yet;
- durable project identity has the right physical schema;
- the ADR system scales to hundreds of decisions;
- the supersession mechanism has been exercised by a real architecture reversal.

Those are deliberately later evidence obligations.

# What changed from our initial assumptions

Most of our starting assumptions survived, but the investigation sharpened them.

First, `Deprecated` was removed from the ADR lifecycle because it blurred whether a decision remained current.

Second, ADR identity is allocated at proposal time so evidence can reference the decision before acceptance.

Third, we formalized the narrow metadata exception to historical immutability: accepted ADR substance is frozen, but supersession status/link metadata may be added later.

Fourth, we decided against one umbrella Article 3 ADR because independently falsifiable boundaries need independently supersedable records.

Fifth, we created an experiment artifact type but intentionally created no fake experiment instance.

And sixth, we refused to upgrade `UMS-GEN-003` to Verified before a real supersession happens.

That last decision may look small.

It is exactly the behavior this entire engineering system is supposed to encourage.

# Arc I is now complete

The first five articles have done something unusual for a monorepo tutorial.

We still have not installed the stack.

Instead we established:

```text
Article 0 -> what "ultimate" means
Article 1 -> tool-independent repository/platform vocabulary
Article 2 -> testable product requirements
Article 3 -> explicit concern-by-concern authorities
Article 4 -> reversible decision/evidence lifecycle
```

Only now do we enter the repository kernel.

That ordering costs us some early gratification.

It gives the rest of the series a much stronger property:

> Configuration can now be judged against architecture that existed before the configuration.

# Next: the smallest useful repository contract

Article 5 begins Arc II:

> **Building the Smallest Useful Repository Contract**

That is where we finally start turning the architecture into the repository's executable/structural kernel.

We will establish things such as:

- root-level contracts;
- required documentation/metadata entry points;
- source-of-truth conventions;
- the smallest durable project identity representation we have deferred deliberately;
- initial machine-verifiable repository invariants.

And Article 5 will have something Articles 0–3 did not have:

an explicit set of accepted ADRs constraining what it is allowed to invent.

That is the point.

We did not build a decision log to make change harder.

We built it so we can change **without forgetting why the old architecture existed**.
