---
name: status
description: "Show where the project stands: the spec queue by status, the current work order's checked and unchecked steps, git state, drift warnings, and the exact next action. Read-only. Use when the user runs /status, asks where things stand, what's next, what's in progress, or is picking work back up after a break or a context clear."
---

# status - where the project stands right now

Where this sits in the workflow:

    any time  ->  [status]  ->  reads the spec queue + current-feature + git
                  (read-only)   prints a short "you are here"

This skill answers one question: *where am I?* It reads the files that already
track progress and prints a short orientation. It is the fast way back in after a
break, a context clear, or a day away. It never changes anything: no edits, no
commits, no installs, no builds, no branch changes.

Progress in this workflow lives in files, not the chat, so everything this skill
reports comes from disk and git. That is the point: a fresh session can run
`/status` and know exactly as much as the last one did.

For setup problems, missing files, placeholder plans, adapter drift, or questions
about whether the Blueprint is installed correctly, run `/doctor` instead.

## Input

None. `/status` takes no argument.

## What it reads

Gather these, then summarize. Don't dump file contents; report the distilled
state.

1. **Project configuration** - read `blueprint/config.json` when present. Report
   `project settings`, `built-in defaults`, or `invalid, using defaults`. A
   missing file is healthy and means defaults. Invalid JSON, schema versions,
   keys, values, non-file paths, or symbolic links are a warning and make
   `/doctor` the next action before any mutating workflow command. Report the
   effective regular and Continuous audit, check, and try-guide policies.
   Before recommending `/overview`, check whether `AGENTS.md` still contains a
   `<!-- blueprint:onboarding-required -->` marker. When it does, onboarding is
   incomplete and `/onboard` is the next action.
2. **The spec queue** - the `**Status:**` line of every spec in `docs/features/`
   and `docs/specs/`. **This is the project's real progress tracker.** Count them
   by status and name the next `Ready` spec, the same target `/feature` would
   pick. Skip `docs/specs/_component-template.spec.md`; it is the template, not a
   work item.
   Call out a `Ready` feature spec blocked by a `Draft` dependency, since
   `/feature` will refuse it.
3. **Build plan** - `blueprint/build-plan.md` when it has real content. It is an
   optional high-level roadmap, **not** the work queue. Report it as context only.
   Where a build-plan checkbox disagrees with a spec `**Status:**` line, the spec
   wins and the disagreement is drift worth flagging.
4. **Current work** - `blueprint/context/current-feature.md`. Is something in
   progress, or is it the reset stub? If a work order is present, report its type
   and name, the source spec named on its `Spec:` line, which build steps are
   checked, and the first unchecked step where `/implement` resumes.
5. **Findings** - `blueprint/context/findings.md`. Count findings by status and
   report open and fixed counts next to the spec queue. Call out any P0 or
   P1 still `open` or `fixed` by ID, since those block `/complete`. A missing
   file means no findings.
6. **Overview freshness** - if `blueprint/context/project-overview.md` exists but
   `project-plan.md` or `build-plan.md` appears newer by filesystem time, mention
   that `/overview` could be refreshed. A missing overview is not a blocker: the
   spec plus `docs/project-brief.md` is enough to build from.
7. **Git** - current branch, whether the working tree is clean or has uncommitted
   changes, roughly how many files changed, last commit subject, and whether the
   branch is ahead of its remote. If the directory is not a git repo, say so and
   skip this part rather than failing.
8. **Progress drift** - flag active work on `main`, a work order in progress but
   no branch matching the configured feature, fix, or rollback prefix, all steps
   checked but not completed, a `Spec:` line pointing at a spec that no longer
   exists, a spec marked `Complete` with nothing implemented, or a spec still
   `Ready` after its work was merged. A rollback legitimately targets a
   `Complete` spec until `/complete` resets it, so do not flag that as drift.
9. **Dashboard activity** - read `blueprint/.state/run.json` when it exists.
   Report the command, mode, status, progress, boundary, and safe resume command.
   A missing file simply means no activity has been recorded. Invalid activity
   state is a warning, not a blocker for the underlying workflow.

## Output

A short, scannable summary, not a wall of text. Aim for something like:

    Status: Building docs/specs/components/button.spec.md
    Config: Project settings.
    Gates: regular audit manual, check when-behavioral, try guide manual;
           Continuous audit always, check always, try guide when-user-facing.
    Specs: 1 Complete, 1 Ready, 4 Draft. Next Ready: theme-toggle.
    Current work: Step 2 of 3 done. Next step: focus and hover states.
    Activity: /autopilot ready, 3/3 build steps, reviewed boundary.
    Findings: 1 open P2 (F-04), 1 fixed P1 awaiting re-review (F-02).
    Git: branch feature/button, 3 uncommitted files, last commit "feat: base button".
    Watch: dark-mode.md is Ready but theme-toggle.spec.md is still Draft, so
           /feature will refuse it.

    Next action: run /implement for Step 3.

End with a single suggested next action, chosen in this order:

- The project configuration is invalid -> `/doctor`.
- A work order is in progress with unchecked steps -> `/implement` and name the step.
- A work order is in progress and all implementation steps are checked -> `/check` if
  proof is not recorded, `/try` if the user wants a manual review path,
  `/implement` when a P0 or P1 finding is still `open` (the repair is an extra
  reviewed step), `/audit` when one is `fixed` and awaiting re-review (both
  block `/complete`), otherwise `/complete`.
- `current-feature.md` is the reset stub and a P0 or P1 finding is `open` ->
  `/fix <finding id>`; when one is `fixed`, `/audit` to re-review and close it.
- `current-feature.md` is the reset stub and a spec is `Ready` -> `/feature` and
  name that spec.
- `current-feature.md` is the reset stub and every spec is `Draft` -> say the
  queue is blocked on human review, name the `Draft` specs, and suggest `/brief`
  to see what each one still needs before it can be promoted.
- Every spec is `Complete` -> say the current milestone is complete; suggest
  hardening, release, or docs when appropriate, or writing the next spec.

If something is off, include a `Watch:` line before the next action. Catching
drift is half the value of the command.

## Rules

- **Read-only, always.** This skill never writes a file, never commits, never runs
  installs, never runs builds or tests, and never switches branches. If the user
  wants to act on what it reports, they run the relevant skill next.
- **Prefer exact next actions.** Do not end with vague advice like "continue the
  workflow". Name the command and, when useful, the file or step.
- **Distill, don't dump.** Report the state in a few lines. Do not paste file
  contents back unless the user asks for them.
- **Be honest about gaps.** If a file is missing or the repo is not initialized,
  say that plainly instead of guessing.

## Formatting

Format the output to match the project's conventions in
`blueprint/context/ai-interaction.md`: concise, scannable markdown, with lists for
enumerations and tables for matrices rather than dense paragraphs.
