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