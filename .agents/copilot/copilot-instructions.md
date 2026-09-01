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
mkdir -p .github && ln -s ../.agents/copilot/copilot-instructions.md .github/copilot-instructions.md
```

Both parts matter. `.github/copilot-instructions.md` is gitignored, so `.github/`
may not exist in a fresh clone and `ln -s` will not create it. And the target is
resolved relative to the link's own directory, so it needs the leading `../` —
without it the link is created successfully but points at
`.github/.agents/…`, which does not exist. A dangling link still looks correct in
`ls -l`; `cat .github/copilot-instructions.md` is what proves it resolves.

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

- `docs/features/` — user-facing feature specs
- `docs/specs/components/` — component specs
- `docs/specs/pages/` — page / view specs
- `docs/specs/layouts/` — layout specs

If no spec exists, tell the user to create one — and route by kind, because the
two templates are different shapes and the wrong one produces the wrong document:

| They are describing | Template | It lands in |
|---------------------|----------|-------------|
| Something a **user can do**, and why it matters | `docs/features/_feature-template.md` | `docs/features/` |
| A reusable **component, page, or layout** | `docs/specs/_component-template.spec.md` | `docs/specs/` |

The test: can it be written as *"As a user, I want… so that…"*? If yes it is a
feature. If nobody wants it on its own — only the thing it enables — it is a
component.

Co-located `*.spec.md` files in `src/` are copies written by `/implement`; the
`docs/specs/` version is the source of truth if they ever differ.
