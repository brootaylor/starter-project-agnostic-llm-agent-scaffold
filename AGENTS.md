# AGENTS.md

Instructions for AI coding agents working in this project. This is the cross-tool
entry point: Codex, OpenCode, Cursor, GitHub Copilot, Gemini CLI, Aider, Zed,
Windsurf, and others read `AGENTS.md`. Claude Code reads `CLAUDE.md`, which imports
this file, so there is a single source of truth.

## What this is

<!-- blueprint:onboarding-required -->
A description of your project and the problem it solves. Replace this paragraph.
`/onboard` or `/adopt` will fill it in from the real project if you would rather
not write it by hand.

This project is spec-first: a spec defines interface, behaviour, states,
accessibility, and test cases before any implementation exists, so the spec is
the contract a human or an agent is held to. Tool choice comes last. The stack
is selected in `docs/project-brief.md`, not assumed here.

The build loop is a workflow layer, not an app skeleton. Scaffold the app first
in an empty folder (create-next-app, Vite, Eleventy, Astro, and so on), then work
through `WORKFLOW.md`. Never run a framework scaffolder inside a directory that
already holds these workflow files; it fails because the directory isn't empty.

The workflow is defined by the local skills and context files below.

## Read these for full context

- `blueprint/config.json` - deterministic project workflow settings
- `blueprint/context/project-overview.md` - the project's source of truth
- `blueprint/context/coding-standards.md` - conventions to follow
- `blueprint/context/ai-interaction.md` - how to work with the user on this project
- `blueprint/context/current-feature.md` - the one feature, fix, or rollback being built right now

This project also carries its own documentation set, which predates the Blueprint
and remains authoritative:

- `docs/project-brief.md` - **the single source of truth.** Read it in full
  before doing anything else: stack selection, browser targets, accessibility
  standard, coding conventions, and agent behaviour rules
- `docs/modern-platform-guide.md` - read before writing any HTML, CSS, or JS
- `docs/design-tokens.md` - read before writing any CSS
- `docs/security.md` - read before generating HTML or deployment config
- `docs/service-worker.md` and `docs/storybook.md` - optional features, only when
  marked active in `docs/project-brief.md`
- `WORKFLOW.md` - the ten-step human guide from setup through to deployment

## Specs are contracts

Specs live in `docs/features/` (user-facing) and `docs/specs/` (components,
pages, layouts). Each carries a status line:

| Status | Meaning | Who acts |
|--------|---------|----------|
| `Draft` | Incomplete - do not implement | Human only |
| `Ready` | Complete - proceed with implementation | Human + agent |
| `Complete` | Implemented and tested | Human only |

**The `**Status:**` line is the work queue.** `/feature` builds the next `Ready`
spec, `/status` reports the queue by status, and `/complete` writes `Complete`
back when the work merges.

**Agents read specs and never edit them.** Do not implement a `Draft` spec; stop
and ask. Do not re-implement or overwrite a `Complete` spec; the human resets it
to `Ready` first. New specs follow `docs/specs/_component-template.spec.md`.

Two edits are the only exceptions in the whole workflow:

- `/complete` sets a finished spec's `**Status:**` and `**Last updated:**` lines,
  and nothing else.
- `/feature` may draft a brand-new spec, on explicit approval, and only as
  `**Status:** Draft`. **Promoting a spec to `Ready` is a human act** - that is
  the signal the contract is settled, and an agent that grants itself that signal
  has removed the gate the project is built around.

### Which file wins

| Question | Authority |
|----------|-----------|
| Stack, conventions, browser targets, accessibility, agent rules | `docs/project-brief.md` |
| What one thing must do, and when it is done | its spec in `docs/features/` or `docs/specs/` |
| What is built, in progress, or not started | the specs' `**Status:**` lines |
| Product context: problem, users, data model | `blueprint/context/project-overview.md` |
| What to write a spec for next | `blueprint/build-plan.md` (optional roadmap) |
| Build steps for the one thing in flight | `blueprint/context/current-feature.md` (disposable) |

Higher rows win. A generated file never overrides a human-owned contract.

## Agent configuration

**Nothing tool-specific is committed to this repository.** `.agents/<agent>/` is
the only committed home for agent config. Every tool's hardwired location is a
gitignored pointer created during setup. Before adding or un-ignoring any path,
ask whether it names a specific tool. If it does, it belongs in `.agents/` with a
pointer, not in the repo.

That rule is the whole premise: committing one tool's config forces that tool on
everyone who clones the template.

### Structure

Each supported agent has its own directory:

```bash
.agents/
├── claude/     # Claude Code (Anthropic)
├── cursor/     # Cursor
├── copilot/    # GitHub Copilot
├── skills/     # the shared workflow skills, read by any capable agent
└── ...         # add any agent that has a config file convention
```

Every file inside an agent directory does two things only:

1. Points the agent at the context files listed above, starting with
   `docs/project-brief.md`
2. Adds anything genuinely specific to that agent (custom commands, model
   settings)

Nothing else belongs in them. `.agents/skills/` is shared, not tool-specific:
Codex, Claude Code, GitHub Copilot, and OpenCode all read the same tree.

### How the pointers are wired

Most agents are hardwired to look for their config in a fixed location and offer
no way to change it. Since the real file lives in `.agents/`, each needs a pointer
at the location it expects.

| Location | Points to |
|----------|-----------|
| `CLAUDE.md` | `.agents/claude/CLAUDE.md` |
| `.claude/skills` | `../.agents/skills` |
| `.claude/commands` | `../.agents/claude/commands` |
| `.cursor/rules` | `.agents/cursor/rules` |
| `.github/copilot-instructions.md` | `.agents/copilot/copilot-instructions.md` |

**macOS and Linux** - symlink:

```bash
ln -s .agents/claude/CLAUDE.md CLAUDE.md
```

**Windows** - `ln -s` needs Developer Mode or an elevated terminal. If neither is
available, copy the file instead and keep the two in sync by hand:

```bash
copy .agents\copilot\copilot-instructions.md .github\copilot-instructions.md
```

**Every pointer is gitignored**, so a Windows copy and a macOS symlink never
collide in git, and no one inherits another developer's agent choice. Symlink
where you can: a copy drifts from its source, which is how the shared skills tree
ended up symlinked rather than duplicated per tool.

### Switching between agents

There is no project-level switch. Every agent reads the same
`docs/project-brief.md` and the same specs, so they always share one
understanding of the project. To use a different agent, create its pointer and
open it.

### Adding a new agent

1. Create `.agents/<agent-name>/`
2. Create the agent's required config file inside it
3. In that file, tell the agent to read `docs/project-brief.md` first. See
   `.agents/claude/CLAUDE.md` for a working example
4. Add any agent-specific config below that instruction
5. Create the pointer at the location the agent expects, per the table above
6. Add that pointer path to `.gitignore`

All project conventions are already in `docs/project-brief.md`, so there is
nothing else to duplicate.

### Removing an agent

Delete the pointer and the directory:

```bash
rm CLAUDE.md
rm -rf .agents/claude
```

Nothing else changes.

### Troubleshooting

**The agent isn't reading `docs/project-brief.md`.** Check the pointer exists
where the agent expects it. `ls -la` should show an entry like
`CLAUDE.md -> .agents/claude/CLAUDE.md`. If it's missing, recreate it.

**The agent reads its config but ignores the project brief.** Some agents need an
explicit instruction to read external files; a path alone isn't always enough.
Check the agent's documentation and copy how the existing configs in `.agents/`
handle it.

**The agent produces output that contradicts the project brief.** The brief is
probably incomplete or ambiguous in that area. Clarify the relevant section and
re-run. Don't hand-edit the agent's output to paper over a brief that needs
fixing.

**The agent implemented the wrong thing.** Check the spec it built against. The
spec is the contract, so a wrong result usually means a spec that was promoted to
`Ready` before it was settled.

## Project configuration

`blueprint/config.json` is the user-owned, machine-readable workflow policy for
this project. Workflow skills read the relevant settings before acting. A
missing file means built-in defaults. An invalid file falls back to defaults for
read-only status reporting, but mutating workflow commands stop and point to
`/doctor` instead of guessing.

Configuration can make review or verification stricter and can tune local
branch names and automated-mode limits. It never grants permission to commit,
merge, push, deploy, publish, send, delete data, waive a failing check, or accept
a finding. Those approval and safety boundaries are not configurable.

`qualityGates.regular` controls automatic audit, check, and try-guide behavior
for the normal workflow and Autopilot. `qualityGates.continuous` controls the
same per-feature gates for Continuous Mode. Every gate defaults to `manual`, so
the named skill runs only when explicitly requested. The conditional modes are
`when-sensitive` for audit, `when-behavioral` for check, and `when-user-facing`
for try guides. `always` runs the gate for every work item in that workflow.

## Workflow

Build one feature, fix, or rollback at a time, behind review gates. Each step's instructions
are plain markdown skills any capable agent can read and follow. The workflow is
exposed through tool-specific adapters:

- Codex: `.agents/skills/<skill>/SKILL.md`
- Claude Code: `.claude/skills/<skill>/SKILL.md`
- GitHub Copilot: `AGENTS.md` plus `.agents/skills/<skill>/SKILL.md`
- OpenCode: `AGENTS.md` plus the compatible `.agents/skills/` or
  `.claude/skills/` tree already installed for the selected tools

Unused adapters can be removed. Codex, GitHub Copilot, and OpenCode can share
`.agents/`. OpenCode can also reuse `.claude/` when Claude Code is selected.
Codex-only, Copilot-only, or OpenCode-only projects can delete `CLAUDE.md` and
`.claude/`. Claude Code-only projects can delete `.agents/`, but should keep
`AGENTS.md` because `CLAUDE.md` imports it. Do not duplicate the same Blueprint
skills under `.opencode/skills/`; OpenCode already discovers the compatible
trees.

When changing shared workflow behavior, update the matching skill in both
adapter folders so Codex, Claude Code, GitHub Copilot, and OpenCode stay aligned.

Core skills:

- `onboard` - tune commands, standards, visibility, ignore rules, and tool adapters after overlaying the Blueprint onto a freshly scaffolded or early project
- `discovery` - optional deep, multi-turn planning conversation that drafts the two user-owned plans only after review and approval; direct plan writing remains fully supported
- `doctor` - read-only Blueprint health check for setup, adapters, plans, overview freshness, and workflow drift
- `adopt` - bootstrap the Blueprint into an existing brownfield app with shipped features
- `overview` - distill the two planning docs into `blueprint/context/project-overview.md`
- `brief` - read-only briefing on an upcoming build-plan feature (scope, dependencies, size) before you spec it
- `feature` - turn a build-plan item into a spec, or propose a reviewed plan addition for a genuinely new feature
- `debug` - reproduce and isolate a failure without editing code, then hand the evidence to `fix` or `implement`
- `fix` - document an ad-hoc bug or change into `blueprint/context/current-feature.md`
- `tests` - add or normalize unit testing and turn on the test gate
- `ci` - explicitly set up one project-specific Verify command and matching automatic GitHub checks
- `implement` - build the current spec one small, reviewed step at a time
- `check` - prove the current spec against the running app
- `try` - read-only manual review guide: where to go, what to click, what to expect
- `audit` - branch-aware or full-project review across all concerns or a focused quality, security, performance, or tests lens; records findings with durable IDs and statuses in `blueprint/context/findings.md`, where open or fixed P0/P1 findings block `complete`
- `rollback` - plan a safe reversal of a completed feature from its archive and exact git commit, with later-dependency review before code changes
- `complete` - run the final safety pass, log features, fixes, or rollbacks under `blueprint/history/`, then merge with approval
- `release` - optional Render or Vercel deployment readiness, local config, env review, and smoke-test planning
- `prototype` - optional, pre-build static mockups to lock the look
- `status` - read-only progress summary, workflow drift warning, and suggested next action

In Codex, invoke these as skills (`$onboard`, `$discovery`, `$overview`, `$feature`,
`$implement`, and so on) or ask naturally, such as "run the overview." In Claude
Code, use the slash commands (`/onboard`, `/discovery`, `/overview`, `/feature`,
and so on). In OpenCode or other tools without a dedicated invocation syntax,
ask the agent to run the matching skill or follow its `SKILL.md` manually. The
conventions in `blueprint/context/` apply however a step is invoked. `/discovery`
is never required: users may write detailed plans directly or develop them
through any conversation before running `/overview`.

Optional explicit-only skill: `autopilot` can run one bounded spec/build pass
when directly invoked, including the configured regular quality gates. It may
create checkpoint commits on the feature or fix branch after passing steps and
repair confirmed P0/P1 findings when its audit gate runs. It stops before
`/complete`, merge, push, deploy, or destructive actions.

Optional explicit-only skill: `continuous` can resume or select the next planned
feature and repeat the complete local feature lifecycle through the configured
limit or end of the build plan. It creates one branch and one local main commit
per feature, applies the Continuous quality gates, archives and merges serially,
and stops on decisions or failed safety gates. It never pushes, deploys,
publishes, sends, or performs destructive actions.

Deployment is also explicit. `/release` can prepare local Render or Vercel config
and run readiness checks, but it must stop before deploy, remote service changes,
push, or publish unless the user gives a separate yes in the current chat.

## Dashboard activity

The dashboard can show the active or most recent substantial Blueprint command
from `blueprint/.state/run.json`. This file is generated local state, ignored by
Git, and never part of a feature commit.

Commands with meaningful progress or a durable handoff should write it when the
state directory exists: `onboard`, `adopt`, `discovery`, `overview`, `feature`,
`fix`, `rollback`, `implement`, `debug`, `check`, `audit`, `tests`, `ci`,
`prototype`, `autopilot`, `continuous`, `complete`, and `release`. Short
read-only orientation commands such as `brief`, `try`, `status`, and `doctor`
do not need activity state.

Writing the initial activity record is the first action of a tracked command,
before project inspection, preflight, or other tool calls. This one generated
state write does not authorize product changes or bypass any safety check. Set
status to `running`, use the command name and a truthful initial summary, then
replace the record at meaningful milestones. On a preflight stop or another
blocker, set it to `blocked` with the exact recovery command. Leave the final
state in place for the next session; the next tracked command replaces it. Use
this schema:

```json
{
  "schemaVersion": 1,
  "command": "continuous",
  "status": "running",
  "summary": "Completing the remaining build plan",
  "detail": "Implementing feature 3.",
  "boundary": "local-only",
  "startedAt": "<ISO-8601 timestamp>",
  "updatedAt": "<ISO-8601 timestamp>",
  "resumeCommand": "/continuous resume",
  "progress": { "current": 2, "total": 5, "label": "features" },
  "feature": { "id": "3", "title": "Export reports" }
}
```

`status` must be `running`, `blocked`, `ready`, or `completed`. Use `ready` when
the command reached its intended review handoff, such as Autopilot waiting for
review before `/complete`. Use `blocked` with the exact recovery command when
work can resume. `boundary` must be `read-only`, `reviewed`, or `local-only`.
The progress, feature, detail, boundary, and resume fields are optional. Never
put secrets, raw logs, prompts, or user content in this file. Activity tracking
must not change a command's approval boundaries or turn a reporting failure into
a workflow failure.

## Automatic verification

Automatic GitHub checks are a separate explicit setup. `/onboard` and `/adopt`
only report existing checks and point to `/ci` or `$ci` when none exist. Running
`/ci` inspects the real project and defines one `Verify` command from checks that
already exist. Use this order when available: typecheck, tests, then build. Never
invent a test runner or another check just to fill the command.

For JavaScript and TypeScript projects, prefer a package script such as `verify`
and use the detected package manager. For other stacks, use the native task
runner or exact combined command. Record the exact command under Commands below.

The optional `.github/workflows/verify.yml` must run that same command for pull
requests and pushes to the default branch. Preserve existing workflows, use the
project's real runtime and install command, and grant only `contents: read` by
default. This setup does not add local git hooks, coverage, browser tests,
security scans, or version matrices. Those remain later project choices.

GitHub branch protection or a ruleset can require the check after the repository
is pushed, but that is a separate remote setting. Missing automatic GitHub
checks do not make the Blueprint unusable.

## Commands

<!-- blueprint:onboarding-required -->
Fill these in for your stack, from the selections in `docs/project-brief.md`.
`/onboard` or `/adopt` will detect and write them for you. Delete any row that
does not apply, and do not invent a command to fill a gap.

- Dev server: `<command>` (http://localhost:<port>)
- Build: `<command>`
- Production server: `<command>`
- Lint: `<command>`

Testing is opt-in. If this project does not already have a unit test runner, run
`/tests` or `$tests` to add one and update this section with the real test
commands. The presence of a `test` command here is the single switch that turns
the testing gate on.

Automatic GitHub checks are a separate opt-in. Run `/ci` or `$ci` to define one
`Verify` command and the matching workflow.
