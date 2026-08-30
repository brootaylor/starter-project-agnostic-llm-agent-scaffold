# WORKFLOW.md

A step-by-step guide to using this scaffold to build a web project — by hand or with an "Ai" coding agent.

---

## The whole thing at a glance

Set up once, then loop. The ten steps below are the long form of this:

```
SETUP  ·  once per project

  clone the template
      │
      ├─  Step 1   configure agent ····  .agents/<tool>/, linked to it
      ├─  Step 2   project-brief.md ····  describe it, pick your stack
      └─  Step 3   set up the stack ····  dependencies and configuration
                                          + /tests  /ci
      │
      ▼
SPEC  ·  per feature, and always human work

      ├─  Step 4   feature spec ·······  docs/features/<name>.md
      ├─  Step 5   component specs ····  docs/specs/**/<name>.spec.md
      └─  Step 6   design tokens ······  docs/design-tokens.md
      │
      │           every one of them written as   Status: Draft
      ▼
  ┌────────────────────────────────────────────────────────────┐
  │ HUMAN GATE     you promote   Draft ──▶ Ready               │
  │ No skill ever does this. It is how you say the             │
  │ contract is settled and building may begin.                │
  └────────────────────────────────────────────────────────────┘
      │
      ▼
BUILD LOOP  ·  per spec, one spec at a time      ← Steps 7 and 8

      ├─  + /brief      preview a spec before committing to it
      │
      ├─  /feature ───▶  context/current-feature.md      « review gate »
      │                 the work order: small build steps
      │
      ├─  /implement ─▶  src/** + checkpoint commits     « gate per step »
      │                 diff + plain-English explanation
      │
      ├─  /check ─────▶  evidence against each "done when"
      │
      ├─  /audit ─────▶  context/findings.md
      │                 the worst two severities block /complete
      │
      ├─  + /try        manual walkthrough to click through yourself
      │
      └─  /complete ──▶  Status: Complete  ·  archived to context/history/
                        one commit covering code + bookkeeping
      │
      └──────────────▶  next Ready spec, back to the top of the loop
                        (or back to Step 4 for a whole new feature)


Optional routes  ·  each replaces work above rather than adding to it

  /discovery   ▶  Steps 2 and 4, drafted from a conversation
  /prototype   ▶  Step 6, arrived at visually instead of abstractly

Anytime         /status  where things stand    /fix       bug with no spec
                /debug   why is this failing   /rollback  undo a feature
```

`+` marks an optional addition — run it as well, or not at all. The two
optional routes are worth knowing about properly:

**`/discovery` is another way through Steps 2 and 4.** Instead of filling in the
brief and your first feature specs from a blank page, it interviews you — one
question at a time, over as many turns as the project needs — then drafts both
files from that conversation and shows you everything before it writes. What it
writes is always `Draft`, so promotion stays yours. Use it when you'd rather
talk the product through than write it cold; writing by hand is equally valid,
and skipping it changes nothing downstream.

**`/prototype` is another way through Step 6.** Instead of choosing colour,
spacing, and type values abstractly, it writes static mockups of your real
screens into `prototypes/`, all sharing one `theme.css`, so you can look at
actual screens and adjust until the look is right. It runs once you have a
feature spec (Step 4) to prototype against. The mockups are throwaway — the
theme is the keeper, and becomes `docs/design-tokens.md`.

Everything from `/feature` down is the build loop — see
[AGENTS.md](./AGENTS.md) for the full skill reference.

---

## Prerequisites

| Tool | Notes |
|------|-------|
| A terminal | macOS and Linux have one built in. On Windows, [Git Bash](https://gitforwindows.org) or [Windows Terminal](https://aka.ms/terminal) are good options |
| [Git](https://git-scm.com) | Required to clone this scaffold. Recommended for version control throughout development |
| [Node.js](https://nodejs.org) | Required by most tooling — install the LTS version |
| [npm](https://www.npmjs.com) | Comes with Node.js |
| A code editor | [VS Code](https://code.visualstudio.com) is a good option |

> A GitHub account is only required if you want to use the **"Use this template"** button. Cloning or downloading the ZIP does not require one.

---

## Before you start

The scaffold comes with a full set of starter files: root config (`README.md`, `package.json`, `.gitignore`, `.nvmrc`), docs (`project-brief.md`, `design-tokens.md`, feature and spec examples), default source files (`src/index.html`, `src/scripts/main.js`), agent configs under `.agents/`, and the build loop's empty working state under `context/`.

The spec and feature files are illustrative examples — replace or modify them to suit your project.

> **Commit everything before making any changes.** This gives you a clean baseline to return to.

---

## Step 1 — Configure your agent

> **Agent-only step.** Skip this if you're building by hand.

Make sure your agent has what it needs:

| Agent | Prerequisites |
|-------|--------------|
| Claude Code | Node.js + an [Anthropic API key](https://console.anthropic.com) |
| Cursor | The [Cursor app](https://cursor.sh) |
| GitHub Copilot | A GitHub account with [Copilot access](https://github.com/features/copilot) + the relevant IDE extension |

Every agent is hardwired to look for its configuration file at one fixed filename — `CLAUDE.md` in the project root for Claude Code, `.cursor/rules` for Cursor, `.github/copilot-instructions.md` for Copilot — and most give you no way to change it.

This scaffold keeps the real configuration files in `.agents/` instead, so cloning it never forces one developer's tool on everybody else. Your one setup task is to create a link at the filename your agent expects, pointing back into `.agents/`. For Claude Code:

```bash
ln -s .agents/claude/CLAUDE.md CLAUDE.md
mkdir -p .claude && ln -s ../.agents/skills .claude/skills
```

> [!IMPORTANT]
> `.claude/` is gitignored, so it does not exist in a fresh clone. Without the `mkdir -p`, that second command fails with `No such file or directory` and none of the workflow skills are available to you.

Every one of those links is gitignored, so your choice of agent never travels with the repository. On Windows, where `ln -s` needs Developer Mode or an elevated terminal, copy the file instead and keep the two in sync by hand.

See `AGENTS.md` for the full table of filenames each agent expects, and the notes on adding an agent that isn't listed here.

---

## Step 2 — Fill in `project-brief.md`

Open `docs/project-brief.md` and complete two things before anything else:

**Describe your project** — replace the placeholder under "What this project is" with a plain description of what you're building and who it's for.

**Choose your stack** — mark exactly one option per category as `[active]`:

- Framework
- Language
- Styles
- Unit testing
- E2E testing
- Build
- Service worker *(optional)*
- Storybook *(optional)*
- Linting *(optional)*
- Security *(optional)*

`project-brief.md` is the first thing the agent reads. Getting it right before writing any specs avoids problems later.

> **Not sure yet?** `/discovery` runs a guided interview — one question at a time — and drafts this file and your first feature specs from the conversation, showing you everything before it writes. It's optional, and it never promotes a spec past `Draft`. Writing them by hand is equally valid.

---

## Step 3 — Set up your stack

With your stack selected, `package.json` needs to be populated with the correct dependencies.

**By hand:**

- Set up `package.json` and any required config files (e.g. `vite.config.js`, `jest.config.js`) based on your active stack selections
- Update `.nvmrc` with the Node.js version your framework recommends
- Update the stack-specific section of `.gitignore` (e.g. `dist/` for Vite, `_site/` for Eleventy, `.astro/` for Astro)

**With an agent:**

```
Read `docs/project-brief.md` and complete the initial project setup.
```

The agent will populate `package.json`, generate any required config files, and update `.nvmrc` and `.gitignore`. It covers setup only — specs and design tokens come in later steps.

**Starting files:** `src/index.html` and `src/scripts/main.js` are included for Vanilla, React, and Svelte stacks. For React and Svelte, `main.js` needs to be updated to mount the app. For Astro and Eleventy, remove both files — those frameworks manage their own pages and templating.

> **Testing and automatic checks are opt-in.** `/tests` adds a unit test runner for your active stack and turns the testing gate on; `/ci` defines one `Verify` command and the matching GitHub Actions workflow. Run either now or later — the loop works without them.

> **Commit your work** once setup is complete and dependencies are in place.

---

## Step 4 — Write a feature spec

Pick the first feature you want to build and create a spec for it in `docs/features/`.

A feature spec describes what a user can do, not how it's built. Use `docs/features/dark-mode.md` as a reference — it covers the overview, user stories, acceptance criteria, and which components are required.

Finish the feature spec before moving on to component specs.

> `docs/features/` is for user-facing feature specs only. Technical configuration docs (service worker, Storybook) live directly in `docs/`.

---

## Step 5 — Write your component specs

Look at the "Components required" section of your feature spec. For each item listed, create a spec using `docs/specs/_component-template.spec.md` as your starting point. This step covers components primarily, but the same process applies to pages and layouts too:

| Spec type | Write the spec here | Code will be generated here |
|-----------|--------------------|-----------------------------|
| Component | `docs/specs/components/` | `src/components/<Name>/` |
| Page | `docs/specs/pages/` | `src/pages/<Name>/` |
| Layout | `docs/specs/layouts/` | `src/layouts/<Name>/` |

See `docs/specs/components/button.spec.md` for a complete worked example.

> [!IMPORTANT]
> Set the status to `Draft` while writing, and change it to `Ready` only when every section is complete. An agent will not proceed with a `Draft` spec, and it will never promote one for you — that decision is yours alone, and it is how you say the contract is settled.

---

## Step 6 — Define your design tokens

`docs/design-tokens.md` is a template for defining your project's visual language — colours, spacing, typography, and other design constants. This step doesn't have to happen before writing specs — it just needs to be done before the agent can write any styles. It can also be revisited at any stage as the project evolves, and run multiple times if you're using an agent to generate the style files.

**By hand:**

- Create `src/styles/tokens.css` (or `tokens.scss` if Sass is active) and implement the values from `docs/design-tokens.md`
- Create `src/styles/main.css` (or `main.scss`) and import the token file at the top

**With an agent:**

```
Read `docs/design-tokens.md` and create the token and main style files.
```

If `docs/design-tokens.md` is empty, the agent will stop and ask you to fill it in first.

> **Want to settle the look first?** `/prototype` writes throwaway static mockups to `prototypes/` that share one set of theme variables. It runs once a feature spec exists (Step 4) and before you build — its one durable output is that theme, which becomes this file. Nothing else it writes is meant to ship.

> **Commit your work** once your tokens are defined and implemented.

---

## Step 7 — Build

Once a spec's status is `Ready`, it's time to build.

**By hand:**

- Use the spec as your blueprint
- Work through it in order: interface first, tests next, then implementation

**With an agent** — two commands, in order:

```
/feature button
```

`/feature` reads the spec, sizes the work, and writes small reviewable build steps to `context/current-feature.md`. It refuses a `Draft` spec, so promote it to `Ready` first. Read what it wrote before continuing — that's the review gate.

```
/implement
```

`/implement` builds those steps one at a time. For each step it shows the diff, explains it in plain English, runs whatever checks the project has, and waits for you to approve before moving on. It commits to whatever branch you're on and never creates or merges branches.

To preview a spec before committing to it, run `/brief` — it explains what the spec involves, what it depends on, and what would block it, without writing anything.

If the agent stops to ask a question, the spec is likely ambiguous in that area. Go back, clarify the relevant section, and re-run.

> **`/autopilot`** runs that whole pass — work order, build steps, verification, gates, checkpoint commits — without pausing at each review point, then stops with a review packet for you. It never runs `/complete`, pushes, or deploys. It's opt-in only: the step-at-a-time path above is the default, because the review between steps is the point of a spec-first loop.

> These commands live in `.agents/skills/` and work the same in Claude Code, Cursor, and Copilot. Prefer them to freehand prompts — they encode the review gates.

---

## Step 8 — Review the output

Generated code appears in `src/` under the relevant directory (see the table in Step 5). Check it against the spec:

- Does every `TC-##` in the spec have a corresponding passing test?
- Does the implementation match the behaviour described?
- Are the accessibility requirements met?

**With an agent**, three commands cover this:

```
/check     prove each "done when" against the running app
/audit     review the code against the project's standards
/try       get a step-by-step manual walkthrough to click through yourself
```

**If something is wrong**, there are two likely causes:

- **The spec is ambiguous** — update the spec first, then ask the agent to fix the implementation
- **You can't tell why it's failing** — `/debug` reproduces the symptom, isolates the failing path, and hands the evidence to `/fix` or `/implement`. It edits nothing itself
- **The spec is clear but the output is wrong** — re-prompt with the relevant section highlighted:
  ```
  Re-read the Behaviour section of `docs/specs/components/button.spec.md` and correct the implementation.
  ```

> [!IMPORTANT]
> Don't edit the implementation directly without also updating the spec. The spec is the source of truth — if the two drift apart, the agent's output becomes unpredictable.

Once everything checks out, update the spec status to `Complete` — or run `/complete`, which writes that status back for you, archives the work order to `context/history/`, and makes one commit covering the code and the bookkeeping.

> **Commit your work** once the spec is marked `Complete` and all tests are passing.

---

## Step 9 — Run it locally or deploy

**Most frameworks** (Vanilla + Vite, React, Svelte, Astro):

```bash
npm install
npm run dev
```

**Eleventy:**

```bash
npm install
npx @11ty/eleventy --serve
```

For deployment, [Netlify](https://www.netlify.com), [Vercel](https://vercel.com), and [Render](https://render.com) all work well with these frameworks. Connect your Git repository, set the build command and output directory for your framework, and they handle the rest.

**With an agent**, `/release` covers Render and Vercel specifically: it checks build, start command, output directory, env vars, and health checks, and can write `render.yaml` or `vercel.json` for you. It stops before any actual deploy, remote service change, or push — those need a separate yes from you.

---

## Step 10 — Iterate

Once a spec is marked `Complete` and committed, return to Step 4 and repeat the cycle for the next feature — feature spec first, then component specs, then build and review.

Run `/status` at any point — after a break, or after clearing your agent's context — to see the spec queue, what's in progress, and the exact next action. Everything it reports comes from files on disk, so a fresh session knows exactly as much as the last one did.

Found a bug that has no spec? `/fix "<description>"` writes a short fix spec and runs it through the same build loop, logged separately under `context/history/fixes/`.

Built something you now want gone? `/rollback "<feature>"` finds the commit that introduced it, checks what has been built on top of it since, and writes a reversal plan for you to review before any code changes. The original spec goes back to `Ready` rather than disappearing — the contract stands, only the implementation is withdrawn.

As the project grows, update `docs/project-brief.md` with any new conventions or constraints the agent needs to know about.

---

## The spec is the source of truth

The spec files are the living documentation of your project. Keep them up to date and the agent stays useful. Let them drift and the agent becomes unpredictable.

If the output isn't right, the spec is always the first place to look. If the spec is clear and correct, but the output is wrong, re-prompt with the relevant section highlighted. If the spec is ambiguous, update it first to clarify, then re-run.
