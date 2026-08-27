METHODOLOGY.md

The best method is to treat the **repository itself as the primary historical record**, and make the article series a curated narrative generated from that record—not the other way around.

For *Building the Ultimate Monorepo Starter from First Principles*, I’d use a **docs-as-code + engineering journal + ADR + Git checkpoint** model. That gives you both authenticity and enough structure that six months later you can reconstruct exactly why the repository looks the way it does.

### The documentation stack

I’d keep five distinct kinds of documentation, because they answer different questions:

| Artifact            | Answers                                          | Lifetime                       |
| ------------------- | ------------------------------------------------ | ------------------------------ |
| `README.md`         | What is this repository and how do I use it now? | Continuously updated           |
| Architecture docs   | How does the system work now?                    | Continuously updated           |
| ADRs                | Why did we make this architectural decision?     | Immutable historical record    |
| Engineering journal | What actually happened while we built it?        | Immutable chronological record |
| Articles            | How do I teach this evolution to someone else?   | Published narrative            |

The important distinction is **current-state documentation vs. historical documentation**.

Your README and architecture docs describe *truth today*. ADRs and engineering logs describe *truth at that moment*. Articles turn those raw materials into a comprehensible story.

---

## I would structure the repo something like this

```text
monorepo-guide/
├── README.md
├── docs/
│   ├── architecture/
│   │   ├── overview.md
│   │   ├── repository-structure.md
│   │   ├── dependency-model.md
│   │   ├── task-execution.md
│   │   └── ...
│   │
│   ├── decisions/
│   │   ├── 0001-use-pnpm.md
│   │   ├── 0002-use-turborepo.md
│   │   ├── 0003-package-boundaries.md
│   │   └── ...
│   │
│   ├── journal/
│   │   ├── 001-foundation.md
│   │   ├── 002-package-management.md
│   │   ├── 003-task-runner.md
│   │   └── ...
│   │
│   ├── requirements/
│   │   ├── 001-foundation.md
│   │   ├── 002-package-management.md
│   │   └── ...
│   │
│   └── verification/
│       ├── 001-foundation.md
│       ├── 002-package-management.md
│       └── ...
│
├── articles/
│   ├── 001-from-an-empty-directory.md
│   ├── 002-...
│   └── ...
│
└── ...
```

I would **not** put everything directly into the articles.

That's tempting, but it makes the published prose responsible for preserving engineering history. Eventually you'll edit an article for readability and accidentally erase some of the historical fidelity.

Instead, preserve the evidence separately.

---

# The fundamental unit should be an "increment"

Every meaningful change to the monorepo becomes an increment:

```text
Increment 007
    ↓
Requirement
    ↓
Investigation / experiment
    ↓
Decision(s)
    ↓
Implementation
    ↓
Failure(s)
    ↓
Correction(s)
    ↓
Tests
    ↓
Verification
    ↓
Git checkpoint
    ↓
Article
```

This fits extremely well with the workflow you already described.

For example:

```text
Article 7
"Adding a Task Runner"

Requirement
We need deterministic orchestration of package tasks.

Questions
- Do we actually need a monorepo task runner?
- npm scripts?
- pnpm recursive?
- Turborepo?
- Nx?

Experiment
Try pnpm recursive execution first.

Result
Works, but doesn't give us the caching/execution model
we want.

ADR-0012
Adopt Turborepo for task orchestration.

Implementation
Add turbo.json, root scripts, package tasks.

Failure
Cache misses unexpectedly.

Investigation
Environment variable changed the hash.

Correction
Declare environment inputs explicitly.

Verification
Run clean build twice and verify cache hit.

Checkpoint
git tag article-007

Article
Explain the whole journey.
```

That is much more powerful than simply documenting the finished `turbo.json`.

---

# The engineering journal is probably the most important addition

For this series, I'd create **one journal entry per article/increment**.

It should be deliberately less polished than the article.

A journal entry might contain:

```markdown
# 007 — Task orchestration

## Starting state

Commit:
`abc1234`

Previous checkpoint:
`article-006`

## Objective

Introduce repository-wide task orchestration without coupling
packages unnecessarily.

## Initial assumptions

We believe pnpm recursive commands might be sufficient.

We expect:

- dependency-aware execution
- parallel execution
- deterministic behavior
- reasonable CI performance

## Experiment 1 — pnpm recursive

Command:

    pnpm -r build

### Expected

Packages should execute in dependency order.

### Actual

...

### Observation

...

### Conclusion

...

## Experiment 2 — Turborepo

...

## Failure — stale generated output

Command:

...

Output:

...

## Diagnosis

...

## Resolution

...

## Decisions produced

- ADR-0012 — Adopt Turborepo
- ADR-0013 — Define build outputs explicitly

## Final verification

Commands:

...

Results:

...

## Ending state

Commit:
`def5678`

Tag:
`article-007`
```

Notice that this contains things you may eventually remove from the article because they're too tedious for a reader.

**Don't remove them from the journal.**

That's your laboratory notebook.

---

# Record failures as evidence, not anecdotes

Because you want the series to be genuinely authentic, I'd establish a simple rule:

> When reality differs materially from our expectation, capture the evidence before fixing it.

Capture things like:

```text
command
exit code
relevant stdout/stderr
environment assumptions
file state
expected result
actual result
diagnosis
fix
verification
```

But don't dump fifty pages of terminal output into Markdown.

Instead:

```markdown
## Failure

Running:

    pnpm build

failed with:

    TS2307: Cannot find module '@repo/config'

Full output:
`./artifacts/007/failed-build.txt`
```

If you want especially strong reproducibility, keep raw evidence under something like:

```text
docs/
└── journal/
    └── artifacts/
        └── 007/
            ├── failed-build.txt
            ├── dependency-tree-before.txt
            └── dependency-tree-after.txt
```

Then your journal is readable while the evidence remains preserved.

---

# ADRs should be small and immutable

Don't turn ADRs into essays.

A useful ADR for this project only needs something like:

```text
# ADR-0012: Use Turborepo for task orchestration

Status: Accepted
Date: 2026-08-27

## Context

What problem forced us to make a decision?

## Decision

What are we doing?

## Alternatives considered

- pnpm recursive commands
- Nx
- custom orchestration
- Turborepo

## Rationale

Why did this choice win?

## Consequences

What becomes easier?

What becomes harder?

What constraints have we accepted?

## References

Journal: 007-task-orchestration.md
Article: 007-...
Commit: ...
```

Most importantly:

**Don't rewrite an accepted ADR when your thinking changes.**

Instead:

```text
ADR-0012 — Accepted
ADR-0037 — Supersedes ADR-0012
```

Now readers can see architectural evolution instead of a fictional version in which you always knew the answer.

---

# Git should become part of the documentation system

For your particular project, I would make each article reproducible from Git.

At minimum:

```text
article-001
article-002
article-003
...
```

as tags.

Even better, record both start and finish commits inside each journal entry.

Then an article can say:

```text
Starting point:
git checkout article-006

Finished state:
git checkout article-007
```

A reader can literally reproduce the tutorial increment.

You can also provide:

```bash
git diff article-006..article-007
```

which gives them the exact implementation associated with the chapter.

This is one of the strongest features you could give the series.

---

# Separate requirements from decisions

This is another distinction that will make the series unusually rigorous.

Before selecting technology, document the **capability you need**.

For example, don't begin with:

> We need Turborepo.

Begin with:

```text
REQ-014: Dependency-aware task execution

The workspace must be able to execute tasks across packages
in dependency order.

Acceptance criteria:

- Package B's build cannot start before dependency A completes.
- Independent packages may execute concurrently.
- Failure in a prerequisite prevents dependent execution.
- Behavior must work locally and in CI.
```

Only *afterward* evaluate tools.

That makes the architecture genuinely "from first principles."

It demonstrates:

```text
problem
→ requirements
→ constraints
→ alternatives
→ decision
```

instead of:

```text
popular technology
→ justification after the fact
```

That's a major difference.

---

# Make verification executable wherever possible

I'd go one step further and make the articles' claims machine-verifiable.

Rather than documenting:

> The monorepo prevents undeclared cross-package dependencies.

Have an automated test demonstrating that invariant.

Your documentation can then say:

```text
Invariant:
Packages may only import declared workspace dependencies.

Verification:
pnpm test:architecture
```

Over time you could have:

```text
pnpm verify
```

run:

```text
formatting
linting
type checking
unit tests
integration tests
architecture tests
dependency constraints
build
documentation checks
```

The beautiful property here is that your written architectural claims become backed by executable evidence.

---

# I'd use a standard article lifecycle

For every article:

```text
1. Snapshot starting state
2. Define the problem
3. State requirements
4. State assumptions
5. Investigate alternatives
6. Run experiment
7. Record evidence
8. Record failures
9. Diagnose
10. Make architectural decision
11. Implement
12. Test
13. Verify requirements
14. Update current-state architecture docs
15. Record final commit
16. Create Git tag
17. Write article from those materials
```

That gives the series an extremely consistent rhythm without forcing every article itself to have exactly the same narrative structure.

---

# The article should be the "compiled output"

Conceptually, I'd think of it this way:

```text
                 REQUIREMENTS
                      │
                      ▼
ENGINEERING LOG → IMPLEMENTATION ← ADRs
       │              │             │
       │              ▼             │
       └──────── VERIFICATION ──────┘
                      │
                      ▼
                 GIT CHECKPOINT
                      │
                      ▼
                    ARTICLE
```

The article synthesizes all of it.

So when writing Article 12, you're not trying to remember:

> "Why did we do this again?"

You have:

* the initial requirement;
* the alternatives;
* the failed experiment;
* the terminal evidence;
* the ADR;
* the working implementation;
* the tests;
* the before/after commits.

That is an extraordinarily strong basis for technical writing.

---

## One additional thing I'd add: a series manifest

At the repository root, maintain something like:

```text
docs/series.yaml
```

Conceptually:

```yaml
articles:
  - id: 1
    title: Starting From an Empty Directory
    status: published
    start: ...
    end: ...
    tag: article-001
    requirements:
      - REQ-001
      - REQ-002
    decisions:
      - ADR-0001

  - id: 2
    title: Choosing the Package Manager
    status: in-progress
    start: ...
    requirements:
      - REQ-003
    decisions: []
```

Now the entire project becomes navigable and, later, automatable.

You could eventually generate indexes, article navigation, architecture timelines, ADR lists, and release/checkpoint pages from it.

---

## The guiding principle

I'd put one rule at the top of the project's contributor documentation:

> **Preserve what we knew at the time. Document what we know now separately.**

That single distinction prevents one of the biggest problems with engineering writing: retrospective certainty.

The README can eventually say:

> "We use X because..."

But the journal can preserve:

> "At this stage we didn't know whether X, Y, or Z was correct. We initially chose Y. It broke under this condition. That discovery caused us to reconsider the architecture and adopt X."

For the kind of series you're building, **that historical trail is arguably as valuable as the final monorepo itself**.

My preferred system, therefore, is:

**Requirements → engineering journal → ADRs → implementation → executable verification → Git checkpoint → article**, with current-state architecture documentation maintained alongside it.
