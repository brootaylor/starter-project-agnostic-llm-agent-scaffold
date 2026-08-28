# Claude Code — Agent Config

> Read `docs/project-brief.md` before doing anything else.
> All project conventions, workflow rules, and context live there.
> This file only contains additions specific to Claude Code.

Shared cross-tool instructions live in `AGENTS.md`, imported below along with the
context files Claude Code keeps loaded each session.

@AGENTS.md

@blueprint/context/project-overview.md
@blueprint/context/coding-standards.md
@blueprint/context/ai-interaction.md
@blueprint/context/current-feature.md

---

## Setup note

Claude Code expects this file at the project root as `CLAUDE.md`. It lives here
instead at `.agents/claude/CLAUDE.md` as part of this project's agent-agnostic
structure. Symlinking is one option, though it isn't guaranteed to work across all
operating systems or agent versions:

```bash
ln -s .agents/claude/CLAUDE.md CLAUDE.md
```

The workflow skills need two more pointers:

```bash
ln -s ../.agents/skills .claude/skills
ln -s ../.agents/claude/commands .claude/commands
```

All three are gitignored, so they never travel with the repo.

---

## Claude Code-specific notes

Workflow skills are in `.agents/skills/` and are shared with every other agent.
Custom slash commands are in `.agents/claude/commands/`. Model and context file
settings are in `.agents/claude/settings.json`.

When a custom command exists for a task (e.g. `/create-component`), use it rather
than improvising — the commands encode the required workflow.

Spec files live in:

- `docs/features/` — user-facing feature specs
- `docs/specs/components/` — component specs
- `docs/specs/pages/` — page / view specs
- `docs/specs/layouts/` — layout specs

Always read the relevant spec before generating or editing anything. Co-located
`*.spec.md` files in `src/` are copies; the `docs/specs/` version is the source of
truth if they ever differ.
