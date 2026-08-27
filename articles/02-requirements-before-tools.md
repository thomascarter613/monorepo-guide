# Requirements Before Tools: Writing the Monorepo Architecture Contract

*Building the Ultimate Monorepo Starter from First Principles — Article 2*

*A tutorial, engineering log, architecture journal, and reproducible build from an empty directory to a production-grade polyglot workspace.*

---

In Article 0, we decided what the word **ultimate** should cost.

In Article 1, we stopped letting tools define our vocabulary.

Now we need to make the architecture capable of saying **no**.

That is what requirements are for.

A goal such as:

> The repository should be reproducible.

sounds useful. It is also loose enough that almost any implementation can claim success.

A preference such as:

> We want to use Bun.

is concrete, but it answers a different question. It tells us what we currently like, not what the starter must accomplish.

A decision such as:

> Bun is the JavaScript workspace authority.

would be stronger still—but if we write it before we define the capability we need, we have reversed the architecture process. We would be evaluating the requirement against the tool instead of the tool against the requirement.

So Article 2 is where the series gets a requirements system.

Not a spreadsheet full of ceremonial sentences.

Not a “best practices” checklist copied from another organization.

A living, traceable contract that later articles can use to answer a much harder question:

> **Did this implementation actually satisfy the promise we made before we chose it?**

## The repository state at the start

Articles 0 and 1 have now been accepted into `main`.

The starting commit for this increment is:

```text
3e1c4161f3eb9d0c385430bd32d28a18b91516c2
```

Create the Article 2 branch from there:

```bash
git switch main
git pull --ff-only
git switch -c series/02-requirements-contract
```

The first two files for the increment are not implementation files.

They are the requirements and journal:

```text
docs/requirements/002-requirements-contract.md
docs/journal/002-requirements-contract.md
```

That order matters now. Unlike the earliest Article 0/1 work, the engineering-record protocol already exists. We can follow it prospectively instead of reconstructing our reasoning later.

## A requirement is not a goal

Consider four statements:

```text
We want the repository to be fast.

We prefer Moon for orchestration.

The repository must avoid rerunning unaffected work when correctness permits.

Use Moon as the repository task authority.
```

They are not interchangeable.

The first is a **goal**.

The second is a **preference**.

The third is a **requirement**.

The fourth is an **architecture decision**.

The distinction is not pedantic. It determines which parts of the system are allowed to change independently.

If Moon eventually fails our polyglot or caching experiments, the requirement can survive while the decision changes.

That is what we want.

A useful requirements system lets us replace an implementation without rewriting history to make the new implementation look inevitable.

## Requirement, preference, assumption, decision, evidence

We will use five categories repeatedly through the rest of the series.

### Requirement

A requirement says what must become true for us to consider the product successful.

Example:

> A supported fresh clone MUST have a documented path to a usable development workspace without undocumented manual repair steps.

Notice what is absent:

- mise;
- Nix;
- Dev Containers;
- a shell installer;
- a package manager.

Those may satisfy the requirement. They are not the requirement.

### Preference

A preference describes an implementation we currently favor.

Example:

> We prefer mise as the environment/tool-version entry point.

Preferences are useful. Pretending we do not have them would be dishonest.

But preferences do not get to redefine success.

### Assumption

An assumption is something we currently believe about the world or an implementation, but have not proved.

Example:

> We believe mise can remain the environment authority while Moon retains enough toolchain integration to model ecosystem tasks correctly.

That is intentionally falsifiable.

### Decision

A decision is the architecture we actually choose after evaluating the relevant requirement, alternatives, constraints, and evidence.

Example—later, if the evidence supports it:

> mise owns repository-declared executable versions; Moon owns project/task orchestration and consumes those executables without becoming a competing version authority.

That belongs in an ADR because future maintainers can reasonably ask why.

### Evidence

Evidence is why we believe the requirement is satisfied.

It can include:

- an executable test;
- a fresh-clone run;
- a deliberately broken fixture;
- a benchmark;
- a task-graph inspection;
- a CI run;
- a generator snapshot;
- a migration fixture;
- a preserved failure log;
- a commit or PR showing the exact state tested.

Configuration is evidence that we **attempted** to implement a requirement.

Configuration alone is rarely evidence that the behavior works.

## The anti-pattern: requirements written as tool names

A bad requirements ledger for this repository would look like this:

```text
REQ-001: Use Bun.
REQ-002: Use Moon.
REQ-003: Use mise.
REQ-004: Use Nx generators.
REQ-005: Use Biome.
```

That is not a requirements document.

It is a shopping list with IDs.

The problem becomes obvious when we ask what success means.

Why Bun?

Why Moon?

What must mise prove?

What would make Nx unacceptable?

Could another tool satisfy the same product promise?

If the requirement is the product name, replacement becomes indistinguishable from requirement failure.

That destroys reversibility.

Our ledger therefore uses concern-based IDs such as:

```text
UMS-ENV-002
UMS-WSP-003
UMS-EXE-004
UMS-GEN-002
UMS-SEC-003
UMS-AI-003
```

The tool can change. The concern remains recognizable.

## Stable IDs are more important than stable prose

Requirement prose will improve.

Acceptance criteria will become more precise.

Some requirements will be split, superseded, or rejected.

The useful invariant is the identity and traceability trail.

For example:

```text
UMS-EXE-004 — Cache hits are correctness claims
```

Later we might discover that this requirement needs separate acceptance criteria for:

- source inputs;
- environment variables;
- transitive dependency state;
- compiler/tool versions;
- generated files;
- restored outputs.

We can refine the criteria without inventing a completely new historical identity every time the wording improves.

If the actual product promise changes materially, then we should supersede the requirement and record why.

## Borrowing normative language from BCP 14

Software specifications have wrestled with requirement language for a long time.

RFC 2119 defines the familiar uppercase keywords:

```text
MUST
MUST NOT
SHOULD
SHOULD NOT
MAY
```

RFC 8174 later clarified that their special meanings apply when those words are uppercase.

The combined convention is BCP 14.

References:

- https://www.rfc-editor.org/info/rfc2119/
- https://www.rfc-editor.org/info/rfc8174/

We are adopting that convention inside the living product ledger when uppercase requirement words appear.

We are **not** claiming this monorepo starter is an IETF specification.

The value is semantic discipline.

If we write:

> Hosted CI MUST orchestrate canonical repository commands.

that is a hard product constraint.

If we write:

> The repository SHOULD provide fast feedback paths for common local work.

we are saying there may be a legitimate reason to deviate, but the deviation needs justification.

If we write:

> A project MAY be publishable.

we are describing permitted architecture, not an obligation.

This is especially useful because the ordinary lowercase word “should” appears constantly in engineering prose. RFC 8174's capitalization rule prevents us from accidentally turning every conversational suggestion into a normative requirement.

## But normative words do not make a requirement testable

This is still bad:

> CI MUST be fast.

We capitalized the important word. We did not improve the requirement.

Fast compared to what?

On which workload?

Cold or warm cache?

How many projects?

Which runner class?

Does “fast” mean wall-clock latency, compute minutes, developer wait time, or cost?

The honest answer in Article 2 is that **we do not have enough representative repository workload yet to invent a useful numerical threshold**.

So we do something better than making one up.

We create a requirement for evidence:

> Claims that caching, affected execution, parallelism, or CI optimization improve feedback/cost MUST be supported by measurements from representative repository workloads.

That is `UMS-OBS-002`.

Later, when we actually have TypeScript projects, Rust, Python, tests, caches, and CI, we can define meaningful thresholds based on the thing we built.

A fake number written early is not rigor.

It is numerically formatted guesswork.

## What makes an acceptance criterion useful?

A useful acceptance criterion should tell a future engineer what observation would make the requirement believable.

Compare these.

### Weak

```text
The repository must support affected execution.
```

### Better

```text
The repository MUST be able to limit appropriate validation/build work
without omitting work whose correct result could have changed.
```

### Better still

```text
Acceptance criteria:

- a direct project change affects that project;
- a dependency change affects dependent work where required;
- a repository-global configuration change invalidates the correct scope;
- deliberately constructed change scenarios compare expected and actual
  affected sets.
```

Now we can design a fixture.

The acceptance criterion has started turning architecture into a future test.

## Requirement statements should describe observable outcomes

A useful pattern is:

```text
<actor/system> MUST <observable behavior> under <relevant conditions>.
```

Then add acceptance criteria that answer:

```text
How would we know?
What negative case would prove this is not working?
Which boundary is intentionally excluded?
What evidence can survive the current terminal session?
```

For this series, the negative case is particularly valuable.

A cache test that only demonstrates a cache hit is incomplete.

We also need to mutate a relevant input and prove the cache **does not** reuse stale output.

A dependency-boundary policy that only validates legal projects is incomplete.

We need a fixture that violates the rule and fails.

A generator test that only creates one project successfully is incomplete.

We need invalid names, repeated runs, and deterministic output checks.

Good requirements generate adversarial tests almost automatically.

## A living product contract needs a different home from historical requirements

Our engineering documentation already had:

```text
docs/requirements/000-first-principles.md
docs/requirements/001-platform-mental-model.md
docs/requirements/002-requirements-contract.md
```

Those files mean:

> What did increment N intend to accomplish when we began it?

That is historical information.

Suppose Article 40 teaches us that our original release requirement was wrong.

We should not rewrite `002-requirements-contract.md` to make Article 2 look prescient.

So the living product contract gets a separate file:

```text
docs/architecture/requirements-ledger.md
```

The distinction is:

```text
docs/requirements/NNN-*.md
    historical increment intent

vs

docs/architecture/requirements-ledger.md
    current product contract
```

That directly applies our engineering-record principle:

> Preserve what we knew at the time. Document what we know now separately.

## The first requirements ledger

The initial ledger is versioned as a working **v0.1** contract.

It covers twelve groups of concerns:

1. environment and bootstrap;
2. workspace/project/dependency model;
3. execution, affected analysis, and caching;
4. generation, migration, and evolution;
5. quality and developer feedback;
6. delivery, CI, and releases;
7. polyglot architecture;
8. security;
9. observability and measurement;
10. AI-assisted engineering;
11. starter productization and upgrades;
12. developer interface and evidence integrity.

This is not an attempt to finish requirements engineering in Article 2.

It is the smallest ledger we can use to evaluate the planned architecture without forcing later articles to reconstruct what success was supposed to mean.

## Requirement states are evidence states

The ledger currently recognizes:

```text
Accepted
Partial
Verified
Superseded
Rejected
```

Why not just `todo` and `done`?

Because requirements do not become true in one moment.

Take:

```text
UMS-WSP-001 — Project is not synonymous with package
```

Article 1 already gave us evidence: the architecture vocabulary explicitly defines a project independently of `package.json`.

So the requirement is not completely untouched.

But we have not yet integrated Rust and Python projects through the actual task graph, CI, and toolchain.

Calling it verified would be false.

Its state is therefore:

```text
Partial
```

That small distinction prevents documentation from turning architectural intent into accidental proof.

## “Implemented” is not a requirement state

This deserves its own rule.

Suppose later we add:

```toml
[tools]
node = "..."
bun = "..."
```

and say:

> Tool versions are implemented.

Maybe.

But `UMS-ENV-001` promises more than a configuration file.

It promises that supported development and CI do not depend on undocumented global version choices.

To verify that, we will need fresh-environment behavior and CI evidence.

Implementation is a step in the evidence chain.

It is not the end of the chain.

## The traceability chain

Our intended chain is:

```text
requirement
    ↓
acceptance criteria
    ↓
experiment / ADR when needed
    ↓
implementation
    ↓
executable check / benchmark / inspection
    ↓
verification record
    ↓
durable evidence
```

The important property is that later articles can cite the same requirement ID.

Article 18 can say:

```text
This decision is evaluated primarily against UMS-EXE-001 through UMS-EXE-004.
```

Article 36 can say:

```text
This benchmark closes evidence for UMS-OBS-002 and adds CI-specific evidence
for UMS-EXE-003.
```

Article 58 can say:

```text
This old-version upgrade fixture is the decisive evidence for UMS-PRD-002.
```

The series stops being sixty loosely related tutorials.

It becomes one accumulating proof.

## A completeness checklist is useful; outsourced architecture is not

We already have repository-specific quality goals:

- reproducibility;
- explicitness;
- determinism;
- feedback speed;
- local/CI consistency;
- governance;
- polyglot readiness;
- evolvability;
- observability;
- security;
- AI legibility;
- understandability.

How do we know we have not simply forgotten a broad software-quality dimension?

One useful external reference is ISO/IEC 25010:2023, the current published edition of the SQuaRE product quality model.

Its public abstract says the model contains nine quality characteristics and can be used for activities including:

- defining requirements;
- checking the comprehensiveness of requirements;
- identifying testing objectives;
- identifying acceptance criteria;
- establishing product-quality measures.

Reference:

- https://www.iso.org/standard/78176.html

That is exactly how we use it here: as a **completeness prompt**.

We are not claiming certification.

We are not copying its taxonomy wholesale.

And we are definitely not saying:

> ISO has a quality model, therefore our requirements are correct.

A general quality model cannot know that this particular starter needs:

- correct affected execution;
- deterministic generators;
- an upgrade path across starter versions;
- a one-authority-per-concern model;
- agent-legible repository instructions;
- immutable article/checkpoint evidence.

Those come from our product and our failures.

External models are good at asking:

> What category might you be missing?

They are not a substitute for answering:

> What must **this** repository prove?

## The first ledger deliberately contains gaps

The `v0.1` ledger records known gaps instead of filling them with generic boilerplate.

Examples:

- exact supported operating systems;
- quantitative local/CI latency targets;
- service reliability objectives before we have a representative service;
- detailed artifact provenance/signing requirements;
- release recovery semantics;
- accessibility requirements for a future developer-facing web UI, if we ever build one;
- broader licensing/compliance requirements.

This is important.

A requirements document is not better because every possible heading has text under it.

An explicit unknown is often more useful than a requirement nobody can justify or test.

## A real failure gave us a new requirement before we finished this article

The most interesting requirement added in this increment did not come from ISO, an RFC, or a monorepo tool.

It came from our own Git history.

Article 1 was developed as a stacked PR:

```text
main
  │
  └── series/00-first-principles
        │
        └── series/01-platform-mental-model
```

PR #3 targeted the Article 0 branch.

PR #1 targeted `main`.

We expected that to preserve independently reviewable article checkpoints.

Then the PRs were accepted.

PR #3 was merged first.

That advanced the **mutable Article 0 branch** to include Article 1.

PR #1 was then merged to `main`.

The final content is correct. Git preserved the history. But the branch name:

```text
series/00-first-principles
```

no longer means:

> exactly the Article 0 checkpoint.

It means:

> a branch pointer that continued moving.

That is a subtle but real failure in our checkpoint model.

### Expected

Article branches would serve as reproducible article identities.

### Tried

A stacked PR where Article 1 merged into Article 0 before Article 0 merged to `main`.

### Observed

The Article 0 branch advanced and therefore stopped denoting only Article 0.

### Learned

A branch is workflow state.

A checkpoint is historical identity.

They are not the same abstraction.

### Changed

The requirements ledger now contains:

```text
UMS-EVD-001 — Article/checkpoint identity is immutable evidence
```

The accepted article state must resolve to immutable Git evidence—at minimum a commit SHA, and preferably a stable article tag once we normalize the checkpoint process.

Mutable branches remain useful for development.

They are no longer allowed to be the sole canonical definition of a historical article state.

### Verified

Article 2 branches directly from the accepted current `main` commit:

```text
3e1c4161f3eb9d0c385430bd32d28a18b91516c2
```

The failure did not derail the series.

It improved the requirements.

That is exactly why we are preserving failures.

## Process requirements can be legitimate product requirements

At first glance, `UMS-EVD-001` may look out of place next to caching, security, and workspace requirements.

After all, a downstream starter user does not care how our blog article branches work.

That is true.

But this repository is currently both:

1. the starter implementation under construction;
2. the reproducible engineering record used to prove how it was constructed.

Checkpoint reproducibility is therefore a requirement of **this product-development system**, even if it will not necessarily ship into every generated starter repository unchanged.

This is why the ledger marks requirement classes.

Some requirements constrain runtime/product behavior.

Some constrain the evidence required before we are allowed to claim success.

We should know which is which.

## We are not creating a machine-readable requirements schema yet

This project is explicitly AI-ready and automation-heavy.

It would be easy to jump immediately to:

```text
docs/requirements/catalog.yaml
```

with fields such as:

```yaml
id:
class:
state:
statement:
criteria:
evidence:
owner:
```

We are deliberately not doing that yet.

Why?

Because we do not know which fields will survive contact with implementation.

A premature schema creates two risks:

1. we start optimizing the engineering process around the schema instead of the problem;
2. we create a second representation that can drift from the Markdown humans are actually reviewing.

So v0.1 is structured Markdown.

Later, when repository self-validation or machine-readable agent context actually needs a schema, we will have evidence about which fields matter.

That is a reversible decision.

## The first requirement groups

Here are several representative requirements from the new ledger.

### Environment

```text
UMS-ENV-001 — Repository-declared supported toolchain
UMS-ENV-002 — Fresh-clone bootstrap
UMS-ENV-003 — Environment diagnostics
```

Notice that none says “use mise.”

Article 9 can evaluate mise against these requirements.

### Workspace and dependencies

```text
UMS-WSP-001 — Project is not synonymous with package
UMS-WSP-002 — JavaScript workspace integrity
UMS-WSP-003 — Enforceable dependency boundaries
UMS-WSP-004 — Dependency version policy
```

Article 13 can compare Bun, pnpm, npm, and Yarn against `UMS-WSP-002` without pretending package-manager choice is a moral identity.

### Execution

```text
UMS-EXE-001 — One authoritative repository task graph
UMS-EXE-002 — Explicit task inputs and outputs
UMS-EXE-003 — Correct affected execution
UMS-EXE-004 — Cache hits are correctness claims
```

Those requirements are deliberately capable of rejecting Moon later.

If they cannot reject the preferred implementation, they are not doing enough architectural work.

### Generation and upgrades

```text
UMS-GEN-001 — Deterministic project generation
UMS-GEN-002 — Existing repositories can evolve
UMS-PRD-002 — Upgrade path across starter versions
```

This prevents us from calling the starter “evolvable” merely because Nx can generate a new package today.

### Security

```text
UMS-SEC-001 — Secret detection
UMS-SEC-002 — Dependency and artifact vulnerability visibility
UMS-SEC-003 — Least-privilege automation
```

These requirements will later help us define non-overlapping Gitleaks/Trivy roles and GitHub Actions permissions.

### AI-assisted engineering

```text
UMS-AI-001 — Repository-local agent guidance
UMS-AI-002 — Machine-readable repository context
UMS-AI-003 — Deterministic gates around nondeterministic assistance
```

The third one is the most important.

An AI system can propose changes.

It cannot become the authority that declares its own changes correct.

## Requirements should be capable of killing our favorite architecture

This is the test I want us to keep using.

Suppose Article 18 shows Moon cannot model a required cross-language task relationship without awkward duplication.

If our requirement is:

```text
Use Moon.
```

then the failure becomes a problem to work around.

If our requirement is:

```text
UMS-EXE-001 — One authoritative repository task graph
```

plus:

```text
UMS-PLY-001 — A non-JavaScript project is first-class
```

then the same failure becomes evidence that Moon may be the wrong implementation.

Requirements give evidence somewhere to land.

Without them, architecture discussions easily collapse into defending sunk cost.

## What should happen when a requirement changes?

Requirements are not immutable scripture.

The system we are building is intentionally designed to learn.

A requirement can become:

```text
Superseded
```

or:

```text
Rejected
```

But an important contract change needs a reason.

For example, imagine we later learn that a full local/CI command identity is actively harmful because hosted CI requires a security-isolated operation that should never be runnable in an ordinary developer shell.

We do not hide the exception.

We update the requirement or its acceptance criteria and leave evidence explaining what changed our model.

That is the same principle we apply to ADRs:

> Preserve the old reasoning. Document the current conclusion separately.

## How Article 3 will consume this ledger

The next article is where our preferred tool map gets dangerous—in a productive way.

We currently favor:

```text
Bun  → JS package/workspace authority
mise → environment/tool-version authority
Moon → project/task authority
Nx   → generator/migration authority
```

Article 1 showed that those products overlap heavily.

Article 2 now gives us a way to evaluate the overlap without hand-waving.

Article 3 will ask questions such as:

```text
Which candidate best satisfies UMS-ENV-001 and UMS-ENV-002?

Can Moon satisfy UMS-EXE-001 without its toolchain behavior competing with
our environment authority?

Can Nx satisfy UMS-GEN-001 and UMS-GEN-002 without becoming required for
normal UMS-EXE-001 execution?

Can Bun satisfy UMS-WSP-002 without us accidentally adopting its test runner
or bundler as authorities simply because they are present?
```

That is a much stronger architecture conversation than:

> I like tool X more than tool Y.

## The Article 2 repository increment

This checkpoint adds the living product contract and the engineering evidence around it.

Key paths are:

```text
docs/
├── architecture/
│   ├── definition-of-ultimate.md
│   ├── requirements-ledger.md
│   └── system-model.md
├── journal/
│   └── 002-requirements-contract.md
├── requirements/
│   └── 002-requirements-contract.md
├── verification/
│   └── 002-requirements-contract.md
└── series.yaml

articles/
├── README.md
└── 02-requirements-before-tools.md
```

We also update the root entry point and series roadmap so the current state is discoverable.

## Reproduce the checkpoint

Start from the accepted mainline used by this increment:

```bash
git clone https://github.com/thomascarter613/monorepo-guide.git
cd monorepo-guide
git switch --detach 3e1c4161f3eb9d0c385430bd32d28a18b91516c2
```

To inspect the work branch while it is under review:

```bash
git switch series/02-requirements-contract
```

Inspect the product ledger:

```bash
sed -n '1,240p' docs/architecture/requirements-ledger.md
```

Inspect the historical increment contract:

```bash
cat docs/requirements/002-requirements-contract.md
```

Compare them deliberately.

One is current product state.

The other is historical increment intent.

That difference is part of the architecture.

## Verification

We still do not have the repository quality toolchain that later articles will build.

So Article 2 cannot honestly claim a formatter/linter/type/test suite that does not exist.

Its verification is structural and traceability-oriented.

From the Article 2 branch:

```bash
git diff --stat main...HEAD

git diff main...HEAD -- \
  docs/requirements/002-requirements-contract.md \
  docs/journal/002-requirements-contract.md \
  docs/architecture/requirements-ledger.md \
  docs/verification/002-requirements-contract.md \
  articles/02-requirements-before-tools.md
```

Then inspect requirement coverage:

```bash
grep -n '^## UMS-' docs/architecture/requirements-ledger.md
```

We verify that:

- product requirements and increment requirements have distinct homes;
- product requirements have stable concern-based IDs;
- the first ledger covers the full planned starter lifecycle;
- acceptance criteria describe observable future evidence;
- tool brands remain implementation candidates rather than product requirements;
- BCP 14 semantics are adopted precisely rather than vaguely;
- ISO/IEC 25010 is used as a reference model, not a false certification claim;
- the mutable stacked-checkpoint failure is preserved and converted into a requirement;
- the article and verification record cite the same architecture contract.

The detailed requirement-by-requirement record lives in:

```text
docs/verification/002-requirements-contract.md
```

## What we learned

The most important lesson is not “write requirements before tools.”

That slogan is too easy.

The harder lesson is:

> **Write requirements that remain useful when your favorite tool fails.**

That means requirements need stable identities.

They need observable acceptance criteria.

They need evidence states.

They need to distinguish implementation from verification.

They need room to be superseded without deleting the old reasoning.

And they need to admit what we cannot measure yet.

We also learned something about our own series process.

A branch is not a checkpoint just because we named it after an article.

That mistake is now part of the product contract rather than an embarrassing detail hidden from the tutorial.

Good.

The requirements system is already doing its job.

## Next: assign authority without creating competing sources of truth

Article 3 is:

**Choosing Authorities: Who Owns Packages, Tools, Tasks, Generation, and CI?**

Now we can finally evaluate the responsibility map with something stronger than intuition.

We will take the overlapping capabilities discovered in Article 1 and test them against the requirements established here.

The question will not be:

> Which tool can do this?

Several of them can.

The question will be:

> Which tool should be authoritative for this concern, which capabilities must the others deliberately *not* own, and what evidence would force us to redraw that boundary?

That is where preferences start becoming architecture decisions.
