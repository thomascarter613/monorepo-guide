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
```

that verifies the repository's important invariants automatically.