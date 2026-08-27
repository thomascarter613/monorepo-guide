# Requirements

Each engineering increment begins with a numbered historical requirements document:

```text
docs/requirements/NNN-slug.md
```

These files define the desired behavior and constraints for **that increment** before its implementation is chosen. They are part of the engineering record and should remain faithful to what was known at the time.

The starter’s **living product requirements** are separate current-state architecture documentation:

```text
docs/architecture/requirements-ledger.md
```

That distinction prevents a later product-contract change from forcing us to rewrite old increment requirements as though earlier engineers knew the future.

## Write outcomes, not shopping lists

Prefer:

> The workspace MUST support deterministic installation from a clean clone.

over:

> The workspace MUST use pnpm.

The first describes a requirement.

The second prematurely prescribes an implementation unless the product truly requires that specific technology.

Technology choices should normally emerge from investigation and be recorded in Architecture Decision Records when they have meaningful architectural consequences.

## Traceability

As implementation arrives, numbered increment requirements should connect to:

- the relevant living product requirement IDs;
- experiments/ADRs where needed;
- implementation;
- verification records;
- durable evidence.

Historical increment requirements may be incomplete or later proven wrong. Preserve that honestly; update the living product ledger separately.
