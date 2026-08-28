---
name: fix
description: Document an ad-hoc bug fix or small change (one with no spec of its own) into context/current-feature.md so it runs through the same build loop. Writes a short fix spec and stops; then /implement builds it and /complete logs it to context/history/fixes/ and commits it. Use when the user runs /fix, reports a bug, or asks to fix or change something that has no spec of its own.
---

# fix - document an ad-hoc fix, then build it like anything else

Where this sits in the workflow:

    /fix  ->  /implement  ->  /complete  ->  back to your features
    (spec     (build it,      (log to context/history/fixes/
     the fix)  reviewed)       + commit)

A fix is a bug or small change with no spec of its own. It runs through the same
loop as a feature (build with review gates, iterate, then commit); it just starts
here instead of `/feature`, and is logged separately.

## Input

A description of the bug or change, for example `/fix "password reset email never
sends"`. If the user just reported the problem in chat, use that.

The input may also be a finding ID from `context/findings.md`, alone
or with a description, for example `/fix F-03`. Pull the problem statement from
that ledger entry. Use this form only between work items, when
`current-feature.md` is the reset stub: this skill overwrites that file, so
while a spec is active, repair its findings through `/implement` instead.

## Step 1 - write the fix spec

Pull context from `docs/project-brief.md` and `docs/project-brief.md`,
then write a short spec to `context/current-feature.md` (this file holds whatever
is being built now, feature or fix). Keep it lighter than a feature spec:

- **Title** - the bug or change in a few words.
- **Type:** Fix  (so `/complete` logs it to `context/history/fixes/`, not `context/history/features/`).
- **Status:** not started - `/implement` updates this durable workflow state as
  work and verification progress.
- **Fixes:** `<finding id>` - only when the fix targets a ledger finding. The
  stamp makes the repair traceable: `/implement` marks that finding `fixed`
  when the repairing step lands, and `/audit` re-reviews it before it closes.
- **The problem** - what's wrong or what needs to change, and where.
- **The fix** - the approach, and anything it must not break.
- **Build steps** - usually one small step; split only if the diff would be too
  big to read. Each ends with an observable "done when".
- **Verify** - how to confirm it's fixed (what to click or test).

Then stop. Tell the user to review the fix spec, then run `/implement` to build it.

## Rules

- Keep it small. If it's really a new feature, write a spec in `docs/features/`
  or `docs/specs/`, mark it `Ready`, and use `/feature` instead.
- Same conventions as everything else (`docs/project-brief.md`).

## Formatting

Format the output to match the project's conventions in `AGENTS.md`: concise,
scannable markdown, with lists for enumerations and tables for matrices rather
than dense paragraphs.
