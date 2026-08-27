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
```

Do not store artifacts merely because they exist.

Preserve evidence that materially supports an investigation, failure,
decision, or verification result.

Never commit credentials, secrets, tokens, private keys, or sensitive
environment data as engineering artifacts.