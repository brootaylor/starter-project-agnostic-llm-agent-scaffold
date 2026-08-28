---
name: autopilot
description: Optional explicit mode that runs one bounded pass of the build loop without pausing at every review point. It can pick or resume the current work order, write it when needed, implement small steps, run verification, apply the check, audit, and try-guide gates, make checkpoint commits, repair confirmed high-severity findings, and stop with a review packet. It never completes, pushes, deploys, publishes, sends, or performs destructive actions without explicit approval. Use only when the user explicitly runs /autopilot or directly asks for it.
---

# autopilot - run the loop without stopping at every gate

Where this sits in the workflow:

    /status  ->  [autopilot]  ->  review packet  ->  /complete
    (where       (work order,      (human review,     (log, commit,
     are we?)     build, gates)     fixes if needed)   reset)

Autopilot is an explicit opt-in path. It uses the same files and the same quality
gates as the rest of the loop, but it does not stop after every normal review
point. A single user request is permission to run one bounded pass until the work
is ready for review, blocked, or unsafe to continue.

It does **not** replace the normal workflow. `/feature`, `/implement`, `/check`,
and `/complete` remain the conservative default, and the human review between
each of them is the point of a spec-first loop. Do not suggest Autopilot as the
default next action. Use it only when the user explicitly asks for it.

The explicit request is permission to make checkpoint commits on the current
branch after passing implementation steps. It is not permission to push, deploy,
publish, send, delete data, or run destructive actions.

## Input

Common forms:

- No argument: resume the current work order if one exists, otherwise target the
  next `Ready` spec in `docs/features/`, then `docs/specs/`. Never a `Draft`
  spec: stop and report which specs need human review instead. Never
  re-implement a `Complete` one. Promoting a spec to `Ready` is a human act and
  is not available to this mode.
- A name or path: target that spec, for example `/autopilot button`.
- `fix "<issue>"`: write and build an ad-hoc fix work order.
- `resume`: continue the work order already in `context/current-feature.md`.

If the requested target conflicts with work already in progress, stop and ask
which one should win. Do not overwrite `context/current-feature.md` silently.

Rollback is intentionally excluded. If the request is a rollback, or
`current-feature.md` is marked `Type: Rollback`, stop and direct the user to the
reviewed `/implement` path. Reversing completed work requires the explicit
dependency and conflict gates in `/rollback` and `/implement`.

## Step 1 - preflight like /status

Read the same state `/status` reads:

- `AGENTS.md`
- `docs/project-brief.md`
- the target spec and any specs it depends on
- `context/current-feature.md`
- `context/findings.md`
- git branch, status, and recent log

Then decide whether it is safe to run. Stop before changing files when:

- the repo is not a git repo
- the working tree is dirty and there is no work order tying those changes to
  this run
- `current-feature.md` has real work and the user requested a different target
- the target spec is `Draft`, or a `Ready` feature spec depends on a `Draft`
  component spec
- the work is visual or replication-heavy and no design reference exists
- the task needs product, data, auth, billing, or destructive decisions that
  `docs/project-brief.md` and the spec do not answer

## Step 2 - choose or write the work order

If `context/current-feature.md` already contains active work, resume it. Read the
checked steps and continue from the first unchecked one.

If there is none:

1. Use the `/feature` behaviour for a planned spec, or `/fix` behaviour for a
   requested fix.
2. Write `context/current-feature.md`.
3. Red-team it before building:
   - missing unhappy paths
   - oversized steps
   - undefined contracts
   - missing design reference
   - scope creep
   - vague done-whens
   - missing testing plan when `AGENTS.md` declares a test command
4. Apply the fixes.

Autopilot may continue past this gate because the user explicitly invoked it.
Still report what the critique changed in the final packet.

## Step 3 - record where this work starts

Before the first product edit, write the current `HEAD` to the work order's
`**Base commit:**` line, exactly as `/implement` does. `/audit` uses it to find
what this work changed, and `/complete` uses it to scope the work commit.

Autopilot does not create, switch, merge, or delete branches. It works on
whatever branch is checked out.

## Step 4 - implement in small steps

Work through the build steps in order. Each step must stay reviewable. Unlike
`/implement`, do not pause for user approval after each passing step; the review
happens at the final packet unless a hard stop is hit.

For every step:

1. Implement only that step.
2. Run the relevant verification:
   - the exact `Verify` command from `AGENTS.md`, when declared
   - otherwise the build, relevant test, lint, and typecheck commands the project
     already documents
   - browser, CLI, API, or app-level evidence for behavioural done-whens
3. If UI is involved, inspect the running app when possible. Capture screenshots
   when they add useful evidence. Check for console errors and failed requests.
4. Self-review the diff for the step:
   - does it match the spec?
   - did it add scope?
   - is the error path handled?
   - does it follow the coding conventions in `docs/project-brief.md`?
   - are tests present for new in-scope logic when the project has a runner?
5. Fix obvious issues and rerun the failed checks.
6. Tick the step in `current-feature.md` only after it passes, and keep
   `**Work status:**` current.
7. Make a checkpoint commit on the current branch for the passing step. Include
   the code, tests, and the updated `current-feature.md`. Use a conventional
   message about the step, not about Autopilot - `feat: button focus and hover
   states`, not `feat: autopilot step 2`. These are cheap rollback points; the
   work-level commit is still `/complete`'s job.

Do not batch the whole thing into one large diff. If a step gets too large, split
it in `current-feature.md` and continue with the first smaller half.

## Step 5 - quality gates

After every implementation step is checked, apply the same fixed policy
`/complete` uses, in this order:

- **Check** - run `/check` when any "done when" needs observed runtime
  behaviour: a click, request, CLI command, download, background job, or
  multi-screen flow.
- **Audit** - run `/audit current` when the work touched a security boundary:
  authentication, authorization, payments, secrets, personal or user data,
  migrations, destructive operations, or external side effects.
- **Try guide** - run `/try` when the change affects UI, navigation, copy, a
  public API or CLI, output, or another workflow a person uses directly.

Reuse adequate evidence already produced during this run instead of repeating it.
A gate that is needed but cannot run is a hard stop, not something to note and
move past. `/try` only generates instructions for human review; never claim the
user performed them.

For pure library or CLI work, a build plus tests and representative command
output may be enough. Be explicit about the evidence used.

## Step 6 - repair what the audit found

When the audit gate runs, it examines the active work, its diff, and the nearby
code the change affects. This is a targeted audit, not a repository-wide cleanup
pass. Findings are recorded in `context/findings.md` with durable IDs and
statuses, as `/audit` defines.

For every finding:

1. Validate it against the actual code, the spec, the tests, the conventions in
   `docs/project-brief.md`, and local project patterns. A finding is evidence to
   investigate, not an automatic instruction to edit.
2. Repair confirmed P0 and P1 findings when the fix stays inside the approved
   scope and needs no product or architecture decision. Set the repaired finding
   to `fixed`, never `closed`.
3. Report P2 and P3 findings in the packet. Fix one only when the change is
   small, directly caused by this work, and clearly required by the project's
   conventions.
4. If a confirmed P0 or P1 cannot be repaired safely within scope, stop and
   report it. Do not present the work as ready for `/complete`.

After any repair:

1. Rerun the documented `Verify` command, or the affected build, lint, typecheck,
   and test commands.
2. Rerun the acceptance evidence the repair affected.
3. Recheck the repaired area against the same audit criteria. When that recheck
   confirms the defect is gone and the repair introduced no new one, move the
   `fixed` finding to `closed` under the `/audit` close conditions and name it in
   the packet. An unrelated new finding gets its own entry and does not keep the
   repaired one open.
4. Make a checkpoint commit only after the repair and its checks pass.

Stop after two failed repair attempts on the same issue. Do not widen the work
into a general refactor, silently suppress a finding, or turn this into a
whole-project hardening pass. A broader cleanup is a separate `/audit` followed
by planned `/fix` work.

## Step 7 - final review packet

Stop with a concise review packet - useful enough for `/complete`, not a full
audit report:

- target spec or fix, and whether the work order was created or resumed
- what the spec critique changed
- changed files and why each changed
- build, test, and check commands run, with pass or fail
- which gates ran and which did not apply
- screenshots or output paths, when relevant
- how to try it manually, or a pointer to `/try` for the full walkthrough
- checkpoint commits made
- self-review findings
- audit scope, findings, repairs made, and checks rerun, when the gate ran
- P0/P1 findings still `open` or `fixed` in `context/findings.md`, which block
  `/complete`
- unresolved risks or skipped checks
- exact next action

If everything is green, the next action is usually: review the diff, run `/try`
if a walkthrough is wanted, then `/complete`. If something failed, name the
failing check and the next fix target.

## Hard stops

Stop immediately and report instead of continuing when Autopilot would need to:

- push, deploy, publish, merge, or send anything
- delete data, reset a database, run irreversible migrations, kill processes, or
  change system settings
- install dependencies or use network access without the current tool's approval
  flow
- make a product decision the spec and `docs/project-brief.md` do not cover
- promote a `Draft` spec to `Ready` to give itself work
- continue after two failed fix attempts on the same issue
- hide, skip, or hand-wave a failing check

## Rules

- One Autopilot run handles one feature or one fix.
- Autopilot stops before `/complete`. Writing a spec's `**Status:**` back to
  `Complete` stays a reviewed act.
- When the audit gate runs, it audits the active work and the code it affects,
  not the entire project.
- A P0 or P1 finding left `open` or `fixed` in `context/findings.md` blocks
  readiness for `/complete`. The ledger is what makes this enforceable.
- `context/current-feature.md` is the state machine. Keep it accurate as steps
  complete, so an interrupted run can be resumed by any later session.
- Follow `AGENTS.md` and the conventions in `docs/project-brief.md`.
- Prefer fewer, higher-quality changes over broad coverage.
- Report uncertainty plainly. A blocked run is useful if it tells the truth.

## Formatting

Format the output to match the project's conventions in `AGENTS.md`: concise,
scannable markdown, with lists for enumerations and tables for matrices rather
than dense paragraphs.
