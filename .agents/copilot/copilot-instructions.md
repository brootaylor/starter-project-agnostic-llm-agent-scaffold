# GitHub Copilot — Agent Config

> Read `docs/project-brief.md` before doing anything else.
> All project conventions, workflow rules, and context live there.
> This file only contains additions specific to GitHub Copilot.

---

## Setup note

Copilot expects this file at `.github/copilot-instructions.md`. It lives here
instead at `.agents/copilot/copilot-instructions.md` as part of this project's
agent-agnostic structure. Symlinking is one option, though it isn't guaranteed
to work across all operating systems or agent versions:

```bash
ln -s .agents/copilot/copilot-instructions.md .github/copilot-instructions.md
```

---

## Read these alongside this file

Cross-tool instructions live in `AGENTS.md` — the spec statuses, the build loop,
and the rules every agent in this project follows. Read it in full.

Workflow skills are in `.agents/skills/`, shared with every other agent. When a
skill covers the task, follow its `SKILL.md` rather than improvising — the skills
encode the review gates. `AGENTS.md` lists them in the order you'd run them.

The loop's working state lives in `context/`:

- `current-feature.md` — the work order in flight, or the stub when idle
- `findings.md` — the review ledger `/audit` writes
- `history/` — archived work orders, the record of what was built

Two rules hold regardless: never promote a spec from `Draft` to `Ready` (that is
the human's signal), and never create, switch, merge, or delete a branch.

---

## Copilot-specific notes

Before generating or editing anything, check whether a spec exists in the
relevant directory:

- `docs/specs/components/` — component specs
- `docs/specs/pages/` — page / view specs
- `docs/specs/layouts/` — layout specs

If no spec exists, tell the user to create one using
`docs/specs/_component-template.spec.md`.
