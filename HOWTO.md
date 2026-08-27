HOWTO.md

Do it by making documentation part of the development workflow itself, so you cannot finish an increment without leaving behind the evidence needed for the article.

1. **Create the documentation skeleton first.** In the repository, add:

```text
docs/
├── architecture/
├── decisions/
├── journal/
├── requirements/
├── verification/
└── artifacts/

articles/
```

Also add a root `docs/series.yaml` or `docs/series.json` that tracks every article/increment.

2. **Give every increment a permanent number.** For example, if the first real increment is repository initialization, call it `001`. Everything related to that increment uses the same identifier:

```text
docs/requirements/001-repository-foundation.md
docs/journal/001-repository-foundation.md
docs/verification/001-repository-foundation.md
articles/001-repository-foundation.md
docs/artifacts/001/
```

If it produces an architectural decision:

```text
docs/decisions/0001-use-pnpm.md
```

This makes cross-referencing trivial.

3. **Start each increment with requirements, before touching the implementation.** Write what must become true, without naming the tool you expect to use.

For example:

```markdown
# 001 — Repository Foundation

## Objective

Create the smallest valid workspace from which the rest of the
monorepo can evolve.

## Requirements

### REQ-001
The repository must be reproducibly installable from a clean clone.

### REQ-002
The repository must declare its required runtime and package-manager
versions.

### REQ-003
A clean installation must not depend on undocumented global tools.

## Acceptance criteria

- Clone repository
- Run documented bootstrap command
- Installation succeeds
- Lockfile remains unchanged
- Verification command exits 0
```

That becomes the contract for the increment.

4. **Create the journal before implementation too.** Think of this as your laboratory notebook. Start with:

```markdown
# 001 — Repository Foundation

## Starting state

Commit:
`<commit>`

## Objective

See:
`../requirements/001-repository-foundation.md`

## Assumptions

- We expect ...
- We believe ...
- We have not yet decided ...

## Questions

- Which Node version policy should we use?
- Should Corepack be required?
- Which package manager best satisfies our requirements?

## Experiments

_To be recorded during implementation._

## Failures

_To be recorded as they occur._

## Decisions

_To be added as decisions are made._

## Verification

_To be completed after implementation._

## Ending state

_To be completed._
```

Then leave it open while working.

5. **Develop normally, but record turning points immediately.** You do not need to document every command. Document anything that changes your understanding.

Suppose you try:

```bash
corepack enable
pnpm install
```

and something fails. Before fixing it, record:

```markdown
## Failure 1 — Corepack unavailable

### Expected

`corepack enable` should make the declared package manager available.

### Command

    corepack enable

### Actual

    corepack: command not found

### Environment

Node:
`...`

OS:
`...`

### Initial hypothesis

The Node installation being used does not bundle Corepack.

### Investigation

...

### Conclusion

...

### Correction

...
```

If the terminal output is large, save it:

```text
docs/artifacts/001/corepack-failure.txt
```

and reference it from the journal.

6. **Create an ADR whenever you make a choice that affects future architecture.** Don't create one for every tiny implementation decision. Create one when future contributors could reasonably ask, "Why did we choose this?"

For example:

```markdown
# ADR-0001 — Use pnpm as the workspace package manager

Status: Accepted

## Context

The repository requires ...

## Decision

Use pnpm.

## Alternatives

- npm workspaces
- Yarn
- Bun

## Rationale

...

## Consequences

Positive:

...

Negative:

...

## Evidence

Journal:
`../journal/001-repository-foundation.md`
```

The journal records **how you reached the decision**. The ADR records **the decision itself**.

7. **Turn every important architectural claim into verification where practical.** If you claim:

> A fresh checkout can be bootstrapped reproducibly.

Create a script that proves it.

Eventually you want something like:

```bash
pnpm verify
```

which may run:

```bash
pnpm format:check
pnpm lint
pnpm typecheck
pnpm test
pnpm build
pnpm test:architecture
```

For the early articles, `verify` may be tiny. That's fine. Grow it with the repository.

8. **Write the verification record after implementation.** For example:

```markdown
# Verification — 001 Repository Foundation

## Environment

Node:
`24.x`

pnpm:
`10.x`

## Requirement verification

### REQ-001 — Clean installation

Procedure:

    git clean -xfd
    pnpm install --frozen-lockfile

Result:

PASS

### REQ-002 — Tool versions declared

Checked:

- `package.json`
- `.npmrc`
- ...

Result:

PASS

## Overall

PASS
```

Now the article doesn't have to merely assert that something works.

9. **Create a checkpoint commit and tag only after verification passes.**

For example:

```bash
git add .
git commit -m "feat: establish repository foundation"
git tag article-001
```

Then Article 2 starts from exactly `article-001`.

A reader can later do:

```bash
git checkout article-001
```

or compare chapters:

```bash
git diff article-001..article-002
```

That is enormously useful for a technical tutorial.

10. **Only then write the article.** The article is assembled from evidence you already collected:

```text
requirement
    ↓
initial expectation
    ↓
experiment
    ↓
failure
    ↓
investigation
    ↓
decision
    ↓
implementation
    ↓
verification
    ↓
result
```

So instead of trying to reconstruct your thinking afterward, you can write:

> We initially assumed Corepack would give us a portable way to provision pnpm. That assumption failed on our first clean-environment test...

and then explain the real failure.

That produces the authentic engineering-log style you want without forcing you to write polished prose while you're coding.

A particularly effective rule for this project would be:

```text
No article checkpoint may be tagged until:

requirements exist
+ relevant decisions are recorded
+ implementation is complete
+ verification passes
+ journal is current
```

You can eventually automate that rule.

For example, the finished workflow for each chapter becomes:

```text
Start article 004
        │
        ├── create requirements/004-*.md
        ├── create journal/004-*.md
        │
        ▼
     engineer
        │
        ├── record experiments
        ├── preserve meaningful failures
        ├── create ADRs
        └── implement tests
        │
        ▼
    pnpm verify
        │
        ▼
verification/004-*.md
        │
        ▼
commit + tag article-004
        │
        ▼
write articles/004-*.md
```

For your repository specifically, I would make **the next action** creating this documentation infrastructure as **Increment 001**, before we make any substantive monorepo architecture choices. Then every subsequent change is captured correctly from the beginning rather than retrofitting the documentation later.
