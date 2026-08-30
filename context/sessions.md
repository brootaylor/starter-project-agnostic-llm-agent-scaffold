# Session Notes

A running log of work done across sessions on this project.

---

## 2026-05-09 — Initial commit

Started the repository from scratch. Created the foundational structure: `README.md`, `WORKFLOW.md`, `AGENTS.md`, `docs/project-brief.md`, and the `.agents/` directory tree for Claude Code, Cursor, and GitHub Copilot configs.

Established the core premise: a tech-agnostic, spec-first scaffold that works equally for hand-crafted builds and AI-assisted ones. The `project-brief.md` was set up as the single source of truth — stack selector, conventions, agent rules.

---

## 2026-05-10 — Spec structure and workflow foundations

Heavy iteration day. Settled the shape of the spec system: status flags (`Draft` / `Ready` / `Complete`), where specs live (`docs/specs/`), and how they map to implementation. Built out the component spec template (`docs/specs/_component-template.spec.md`).

Wrote and refined the AI content rules, workflow instructions, and README content. Multiple formatting and text passes to get the tone and structure right.

---

## 2026-05-12 — Service worker spec + component template improvements

Added the first feature spec for the service worker (`docs/features/service-worker.md`), establishing the pattern for user-facing feature docs alongside technical specs. Improved the component spec template with richer coverage of states, accessibility, and test cases.

---

## 2026-05-13 — Config, docs, and spec refinements

Added extra config files (`.agents/` tooling). Cleaned up status flags for service worker and Storybook — removed premature statuses that were blocking agent reads. Tightened up docs and technical config. Multiple README passes.

---

## 2026-05-14 — Major expansion: project brief, security, Storybook, service worker, agent config

The largest single session. Rewrote `docs/project-brief.md` extensively — expanded the stack selector, framework guidance, file/folder conventions, and agent instruction clarity. Rewrote `WORKFLOW.md` to be sharper and easier to follow.

Added `docs/security.md` — HTTP security headers, CSP configuration, and secure coding guidelines with framework-specific notes. Integrated the security entry into the file/folder convention.

Substantially rewrote `docs/service-worker.md` (329+ line addition) with strategy selector, caching patterns, and framework-specific guidance. Updated `docs/storybook.md`. Revised design tokens structure. Updated agent instructions for compatibility across Claude Code, Cursor, and Copilot. Updated the component template.

---

## 2026-05-15 — Connective tissue between specs and docs

Focused on making the relationship between the `docs/specs/` technical specs and `docs/features/` feature docs clearer. Improved `docs/project-brief.md` to better explain how these two layers connect. Updated README.

---

## 2026-05-17 — Project brief update

Incremental refinement to `docs/project-brief.md` — likely clarifications or additions following a review of the expanded spec/doc system.

---

## 2026-05-20 — Modern platform guide

Added `docs/modern-platform-guide.md` — a reference for which web platform APIs and features to use, when a fallback is acceptable, and how to approach progressive enhancement. Wired it into `project-brief.md` and `README.md`. Updated active state handling.

---

## 2026-05-21 — Workflow, README, and agent compatibility pass

Rewrote `WORKFLOW.md` — condensed it significantly (167 lines removed, 95 added) while improving clarity. Enhanced `project-brief.md` to handle meta-framework implementation (Astro, Eleventy). Improved README structure and prose. Added clearer agent instructions with better compatibility notes across agents. Multiple formatting passes.

---

## 2026-05-22 — CSS, Storybook, service worker, README

Set baseline custom CSS property values in `docs/design-tokens.md` as a concrete starting point for design system tokens. Updated Storybook instructions. Enhanced service worker documentation (535 lines added — a major rewrite/expansion). Updated README intro.

---

## 2026-06-30 — Context sessions log created

Created this file (`context/sessions.md`) as a running log of session activity. No code changes this session.

---

## 2026-08-28 — AI Blueprint adopted as the build loop

Overlaid the AI Blueprint onto the scaffold and ran `/adopt` on a fresh branch
(`dev-ai-blueprint-adoption`). The adoption itself was straightforward; the
interesting work was everything that followed from two realisations.

**The scaffold is a template, not a project.** `/adopt` did what it is designed to
do and filled `project-plan.md`, `build-plan.md`, `coding-standards.md`, and the
`AGENTS.md` Commands section with accurate facts about this repository — "this
project has no commands", a seventeen-item roadmap about the scaffold itself. All
of it correct, and all of it wrong for anyone cloning the template. Reverted the
four files to worksheet form, matching how `docs/project-brief.md` ships. The
rule going forward: before writing to any tracked file here, ask who reads it
after a clone.

`coding-standards.md` was deliberately *not* restored to the shipped Blueprint
default, which assumes Next.js, Prisma, and Tailwind. That default is actively
misleading in a scaffold offering Eleventy, Astro, Svelte, and Vanilla. It is now
a tech-agnostic worksheet that defers to `docs/project-brief.md` and has empty
stack-specific headings to fill in.

**The Blueprint could not see the spec system.** Sixteen of its twenty-two skills
drove off `build-plan.md`, and not one referenced `docs/specs/`, `docs/features/`,
or `docs/project-brief.md`. Since the `Status:` state machine is the scaffold's
whole product, that meant two work queues that never spoke to each other — a
`Ready` spec would sit untouched forever.

Rewrote fifteen skills so the `**Status:**` line is the work queue. `/feature`
sequences a `Ready` spec into a disposable work order at
`blueprint/context/current-feature.md`, carrying a `Spec:` line that binds the
chain together; `/implement` builds against the spec as contract; `/check`,
`/audit`, and `/try` prove the work against the spec's own criteria rather than
the work order's paraphrase; `/complete` writes `Complete` back. `/continuous` and
`/autopilot` were selecting straight from the build plan, which would have let an
automated mode build a `Draft` spec — closed, with an explicit rule that no mode
may promote a spec to `Ready` to give itself more work. Running out of `Ready`
specs is a successful end to a run, not something to route around.

`build-plan.md` survives as an optional roadmap answering only "what should I
write a spec for next?" Where a checkbox and a spec status disagree, the spec
wins. The full authority order is now a table in `AGENTS.md`.

**Restored the agent wiring documentation.** The overlay had overwritten
`AGENTS.md`, taking with it the symlink walkthrough, the Windows caveats, and the
add/remove/troubleshoot sections — which `WORKFLOW.md` Step 1 still pointed at.
Folded that content back in under the new structure, updated for the shared
`.agents/skills/` tree and stating the gitignored-pointer rule as fact rather
than a suggestion. `.agents/claude/CLAUDE.md` had also drifted from the pattern
its Cursor and Copilot siblings follow; brought it back in line.

Added `CLAUDE.md`, `.cursor/`, and `.github/copilot-instructions.md` to
`.gitignore` alongside the existing `.claude/`, so no agent pointer is ever
committed. `.github/workflows/` stays committable for a future `/ci` run.

Still open: `/discovery` has not been taught the spec system and will still
present `build-plan.md` as the tracker. The Writing section of
`coding-standards.md` bans em dashes as an AI tell, which contradicts the voice of
every doc in this repo, including this log — unresolved.

---

## 2026-08-28 (later) — The Blueprint absorbed, tier by tier

Stepped back from the adoption and changed the approach. The previous session had
rewritten fifteen skills in place, which worked, but it left the scaffold carrying
a second system: `blueprint/` with its own context files, its own coding standards,
its own config, and an installer for something that was already installed. The
goal was never "my scaffold has the Blueprint bolted on" — it was "my scaffold has
a build loop." Those turn out to want opposite things.

**The Blueprint is four layers, not one thing.** Verbs (`.agents/skills/`),
context (`blueprint/context/`), planning (`project-plan.md`, `build-plan.md`), and
runtime state (`config.json`, `.state/`). Only the first was ever wanted. The
coupling looked total — nearly every skill referenced `blueprint/` — but counting
the references deflated it: `run.json` appeared once per skill as a dashboard
breadcrumb, `ai-interaction.md` once as a "write concise markdown" pointer. One
line each. The only genuinely load-bearing shared file was `current-feature.md`.

Sorted the twenty-two skills into three tiers by what they actually need, and took
them in that order.

**Tier A — six advisory skills, no state at all.** `brief`, `debug`, `tests`,
`ci`, `prototype`, `release`. Stripped the two boilerplate lines from each and
repointed every context read at `docs/project-brief.md` and `AGENTS.md`. Net
shrink of twenty-two lines. `debug` and `release` had each been reading
`current-feature.md`; pointed them at the spec system instead, which made them
better — `debug` now finds the spec governing the failing code by matching files
back to `docs/specs/`, and `release` reads `docs/security.md` for headers and CSP,
which the Blueprint version never consulted despite writing deployment config.

Tested both live. `/brief` picked Button (the only `Ready` spec) and found three
real blockers: the spec's props table and TC-11 presume a component abstraction
that Vanilla + plain CSS doesn't have, eleven test cases with no runner
configured, and a `spinner.svg` that doesn't exist. It also caught a genuine
cross-reference bug — `button.spec.md` claims it's used in `main-layout.spec.md`,
which lists only `ThemeToggle`. `/ci` correctly refused to do anything: no
typecheck (JavaScript), no tests, and no build, because Vite is marked `[active]`
but was never installed. Writing a workflow there would have produced a green
check that proves nothing.

Both refusals surfaced the same thing about this repo: the gap between "selected
in the brief" and "present in the repo" is *by design* for a template. Which means
these two skills can only ever be tested from a clone.

**Tier B — the build loop.** The realisation that shaped it: `WORKFLOW.md` Steps
4–10 already *are* a build loop, written for humans and executed by copy-pasted
prompts. So this was never about adding a loop; it was about automating the one
already documented. Every verb had to map onto a step already written, and
anything that didn't map was ceremony. Steps 4, 5 and 6 — feature specs, component
specs, design tokens — stay manual. That's the "human takes control" half of the
premise, and the loop doesn't touch it.

Two decisions came out of that mapping.

*No branching.* `/implement` created `feature/<name>` and `/complete`
squash-merged to main. `WORKFLOW.md` has never mentioned branches — it says
"commit your work" at Steps 3, 6, and 8. Imposing a branching model on a scaffold
whose prerequisites table explains what a terminal is would have been adding a
workflow nobody asked for. Both skills now commit to whatever branch is checked
out. In its place `/implement` records a `Base commit:` on the work order, which
is how `/audit` scopes "what changed" without a merge base.

*`/create-component` deleted.* It did the same job as `/implement` but was
Claude-only, so Step 7 had to apologise for it: "Other agents don't support it."
Its co-located `<Name>.spec.md` behaviour was ported into `/implement` along with
the spec→code directory table. Step 7 is now `/feature` then `/implement`,
identical across Claude Code, Cursor, and Copilot.

State moved to `context/`, beside this log: `current-feature.md`, `findings.md`,
and `history/{features,fixes,rollbacks}/`. The two stubs were extracted
programmatically from `/complete`'s own reset text so the shipped files and what
the skill writes back can't drift. `config.json` is gone — its values became
documented defaults, same behaviour, one less file.

Ran `/status` as a smoke test. It read the spec queue, found the new `context/`
files, skipped the template, and named `/feature button` as the next action. No
`blueprint/` involved.

**Still open.** Eight Tier C skills — `adopt`, `onboard`, `doctor`, `overview`,
`discovery`, `rollback`, `autopilot`, `continuous` — still point at `blueprint/`,
and are now its only consumers. The directory can't go until they're either
adopted or deleted. `adopt`, `onboard`, and `doctor` are the installer for
something already installed and should probably just go; the other five are real
features, `/rollback` especially, since `/implement` and `/complete` still handle
`Type: Rollback` work orders and those paths were kept intact and repointed at
`context/**`.

The em-dash ban noted last session is now moot for the loop itself —
`coding-standards.md` is only read by the unadopted Tier C skills. It becomes a
live question again only if any of them are kept.

Tier B is considered work, not demonstrated work. `/status` is the only piece that
could actually be exercised here, because `/feature` needs a buildable `Ready`
spec and Button isn't one. The loop wants shaking out on a real project before
it's trusted.

---

## 2026-08-28 (later still) — Tier C, and the end of `blueprint/`

Closed out the eight remaining skills. `blueprint/` is gone.

**Four were an installer for something already installed.** `adopt` overlays the
workflow onto a brownfield repo; `onboard` does the same for a greenfield one.
This scaffold is cloned, not overlaid, and `WORKFLOW.md` Steps 1–3 *are* the
onboarding. `doctor` health-checked adapters, ignore rules, and manifest drift —
nearly all of which had already stopped existing. `overview` generated an
AI-facing `project-overview.md` from the two planning files, which is what
`docs/project-brief.md` already is. All four deleted.

**`continuous` deleted too.** It built every `Ready` spec serially, unattended,
with a branch and a squash-merge per feature. Both of those contradict the
no-branching decision from earlier today, and the premise contradicts the point:
a spec-first loop where a human promotes `Draft` to `Ready` doesn't want a mode
whose whole value is not stopping. Five skills, 1,969 lines.

**Three were real features and were rewritten.**

`/rollback` had to stay — `/implement` and `/complete` still handle
`Type: Rollback` work orders, so without it that branch of the loop is
unreachable code. Repointed at `context/history/features/` and
`context/current-feature.md`, and its protected-path exclusions now match
`/implement`'s reverse-patch pathspec exactly, which they hadn't. Dropped the
"stop unless you're on the default branch" preflight, which no longer means
anything.

Its spec template turned out to be broken in a way nothing would have caught
until a rollback was actually attempted: `**Status:** not started` where
`/feature`'s template writes `**Work status:**`, and no `Spec:` or `Base commit:`
lines at all. Three fields that `/implement`, `/audit`, and `/complete` all read.
A rollback would have been the one work-order type the loop couldn't track.

`/discovery` was the interesting one. Stripped of its Blueprint output files it's
a guided planning interview, and deleting `adopt` and `onboard` had just left the
template with no on-ramp at all — clone it, open an empty brief, and start
typing. So it now drafts `docs/project-brief.md` and the first `docs/features/`
specs: the conversational form of Steps 2 and 4, for anyone who'd rather talk
through a product than write the brief cold. Hard-stopped at `Draft`. Promotion
stays human, which is the rule the whole loop rests on.

`/autopilot` kept, as the other end of the same spectrum — the agent carrying a
settled spec the whole way instead of stopping at each gate. Lost its branch
step, its `config.json` gate lookups, and its `run.json` publishing; gates are now
the same fixed policy `/complete` uses. Checkpoint commits land on the current
branch, and push, merge, and `/complete` stay hard stops. It sits above the loop
rather than in it, and `AGENTS.md` says so explicitly, because the review between
steps is the point of the default path.

**Two dangling references, found by grepping for the wrong thing.** Tier A had
been swept for `blueprint` but never for the *names* of skills that hadn't been
deleted yet. `/ci` pointed at `/doctor` for drift detection; `/prototype` had
`/overview` in its flow diagram. Fixing the second turned up a genuine
tech-agnosticism leak sitting in the scaffold since the adoption: `/prototype`
told agents to port the theme into `globals.css` `@theme`, which is Tailwind v4
syntax, in a scaffold whose whole claim is that it doesn't assume a stack. Now
points at `docs/design-tokens.md` and the project's stylesheet.

Seventeen skills left. Verified every `/skill` token referenced anywhere resolves
to one that exists, and every `SKILL.md` frontmatter `name` matches its own
directory. `/status` gives the same reading it gave before Tier C started.

**Still open.** The loop is still considered work rather than demonstrated work —
nothing here changed that. `/feature` needs a buildable `Ready` spec and Button
still isn't one, for the three reasons `/brief` found earlier today. The em-dash
ban is settled by deletion: `coding-standards.md` went with `blueprint/`, and
nothing reads it now.

`/discovery` and `/rollback` are the two most likely to bite on first real use.
Discovery writes into `project-brief.md`, a file with a lot of shipped reference
material below its checklist that it's told to leave alone; rollback has never
been run at all, and its git surgery is the least forgiving thing in the repo.
Both want exercising on a clone before they're trusted.

---

## 2026-08-30 — Branch renamed, and the documentation caught up with the code

**The branch is now `dev`.** `dev-ai-blueprint-adoption` renamed and pushed with
tracking set. Two branches that appeared to exist on the remote turned out to be
stale local tracking references: `git ls-remote` showed origin had only ever
held `dev` and `main`, so all of the Blueprint absorption work had never left
this machine until today's push. Pruned the stale references and deleted the old
`dev-ai-blueprint` branch.

**The documentation had not kept up with the absorption.** `AGENTS.md` and
`WORKFLOW.md` were rewritten during it; `README.md` received two lines, and
`docs/project-brief.md`, the Copilot instructions, and the Cursor rules were
untouched entirely — despite `AGENTS.md` naming the brief as the single source
of truth. A sweep of all 42 markdown files found twelve things to fix.

Two were genuinely broken pointers. The `.claude/commands` row in `AGENTS.md`
pointed at a directory deleted along with `/create-component`, so anyone
following the setup table would symlink to nothing. And the feature spec template
still cited `coding-standards.md` for its testing gate, a file that went with
`blueprint/`; it now points at the real gate, which is a `test` command in
`AGENTS.md`.

The rest was absence rather than error. `README.md` never mentioned the build
loop, the skills, or `context/` — the largest change on the branch was invisible
in the first document anyone reads. `project-brief.md` had no `context/` in its
folder tree, no work-order rows in "Where to look", and none of the three rules
the loop rests on in its agent behaviour rules. The Copilot and Cursor configs
never pointed at `AGENTS.md`, the skills, or `context/`, while `CLAUDE.md`
imports `AGENTS.md` directly — the agent-agnostic claim was thinnest exactly
where it is advertised. `WORKFLOW.md` named Netlify and Vercel for deployment
while `/release` actually covers Render and Vercel, and six of the seventeen
skills appeared nowhere in the human guide at all.

**One thing that looked like drift wasn't.** `docs/specs/hooks/` and
`docs/services.md` are both named in project-brief's "Other spec types" section,
which explicitly presents them as things a project adds as it grows. They were
removed from the folder tree and "Where to look" — which describe what ships —
and the forward-looking section was left alone. Worth remembering before
"fixing" them again.

**Diagrams, in ASCII, in two places.** A full setup-to-complete map at the top of
`WORKFLOW.md` and a compact loop-only version in README's build loop section.
Mermaid was considered and rejected: it renders as a real graphic on GitHub, but
it would have been the only Mermaid in the repository, every one of the
seventeen skills already draws its flow in ASCII, and a Mermaid block is
precisely the content that reads differently to a human on GitHub than to an
agent parsing the file — which cuts against the premise the scaffold sells.

The diagram took three passes to stop being misleading. The first used one
bracket notation for three different relationships, so `/discovery` read as
something you do *as well as* picking your stack, when it is an alternative route
that covers Steps 2 and 4 together. The second fixed that but labelled the block
"Instead of, or alongside", which names no object. Optional additions are now
marked `+`, alternative routes say which step they replace, and the fuller
explanation moved to prose below the diagram where it has room.

**A real bug, found while rewriting Step 1.** The documented skills symlink fails
on a fresh clone. `.claude/` is gitignored, so it does not exist, and `ln -s`
will not create a missing parent directory. Tested it: `ln: .claude/skills: No
such file or directory`. Anyone following `CLAUDE.md`'s setup note ends up with
no workflow skills and no obvious reason why. Both places now say
`mkdir -p .claude && ln -s ../.agents/skills .claude/skills`, and Step 1 gained
the explanation it never had: that agents hardwire a config filename, which is
why a link is needed at all.

**Still open.** The loop remains considered work rather than demonstrated work;
nothing today changed that, and Button is still not a buildable `Ready` spec. The
severity scale is used across eight files and the `P` in `P0` to `P3` is never
expanded anywhere — it is defined by meaning in `/audit` but never spelled out.
The `« review gate »` markers in the new diagram are the one piece of notation a
fresh reader cannot decode from the diagram alone. Further diagram refinement was
explicitly deferred.

---

## 2026-08-30 (later) — Features and components, and a lesson in scope

Started as a plain question — what is the difference between a feature and a
component here — and turned into finding that two documents answered it wrongly.

**The distinction was already load-bearing, just badly documented.** `/feature`
scans both `docs/features/` and `docs/specs/` for its work queue.
`dark-mode.md` already gates implementation behind a Components required table
and already tells component specs not to restate the values in its
Implementation notes. All of that machinery ran whether or not anyone could
explain it. The explanation itself was twelve lines of abstract prose buried at
line 468 of `project-brief.md`, with no example and no decision rule.

**Two documents were actively wrong.** `README.md` and `AGENTS.md` both said to
use `docs/specs/_component-template.spec.md` as the starting point for *any* new
spec. An agent following `AGENTS.md` literally would write a feature spec as
props tables, states, and test cases — the wrong shape entirely for something
that wants user stories. Both now route by kind.

**`docs/features/` had no template.** `docs/specs/` has shipped one since May;
the feature side has only ever had `dark-mode.md`, which `README.md` explicitly
invites consumers to replace. Delete the example and the format leaves the repo.
Added `docs/features/_feature-template.md`, mirroring the component template's
house style — status header, guidance blockquotes, `Draft → Ready` checklist —
and both templates now open with an "Is this the right template?" routing table
that tells you to delete it once you know. The test they both give: can you write
it as *"As a user, I want… so that…"*? If yes it is a feature; if nobody wants it
on its own, only the thing it enables, it is a component.

The `project-brief.md` section became a comparison table, that decision rule, the
one-way dependency, and the rule that any value more than one component must
agree on is fixed once in the feature spec and only referenced elsewhere — a
silent failure, since a copied key leaves both files reading as correct until
they drift. It also now names the three things called "feature" in this
scaffold: the spec in `docs/features/`, the generated work order at
`context/current-feature.md`, and the `/feature` skill that turns one into the
other. Only the first is written by hand.

**Two renames were made and then reverted.** `feature-spec-template.md` and
`rollback-spec-template.md` under `.agents/skills/` both describe work orders,
not specs — the names are inaccurate in exactly the way the session was about.
They were renamed to match, then backed out when the question came: is there a
clear benefit in what it is and how it's used? There wasn't. Neither changed any
behaviour, both files are read only by an agent mid-skill and never by a human,
and the second rename had been justified partly by an inconsistency the first one
created. `.agents/` ended the session byte-identical to where it started.

The scope menu offered at the outset is what let that happen: it bundled the two
genuine defect fixes and the cosmetic renames into a single recommended option,
so there was no way to approve one without the other. Worth splitting those next
time.

**The log's own warning worked.** A dead-link sweep over the docs flagged
`docs/services.md` as missing — precisely the thing the previous entry recorded
as looking like drift but not being it. It sits in "Other spec types", which
describes what a project adds as it grows, and was left alone for the second
time.

`UI` turned out to be the only unexpanded acronym in `project-brief.md`, in the
one sentence defining what a component is. Now spelled out.

**Still open.** `/fix` has no work-order template at all — it writes
`context/current-feature.md` freehand from prose in its own `SKILL.md`, while
`/feature` and `/rollback` both work from a file in `reference/`. The shape of a
fix work order is therefore pinned down nowhere. Noted and deliberately not
acted on, since closing it means authoring a template rather than tidying one.

Nothing here moved the loop from considered work to demonstrated work. This was
a documentation session; no skill was run and no spec was built. Button is still
not a buildable `Ready` spec.

**Then this log turned out to be invisible.** Asked to update session memory, I
wrote only to the `~/.claude` memory directory and reported the task done -
`context/sessions.md` was mentioned in exactly zero files in the repository.
`CLAUDE.md` imports `AGENTS.md`, `project-brief.md`, `current-feature.md`, and
`findings.md`, but not this one, and nothing instructed an agent to maintain it.
A file kept primarily so the agent knows where things stand, that no agent-facing
document named.

**It is now personal context rather than template content.** It had been tracked,
which meant anyone cloning the scaffold would inherit sixteen entries about
building the scaffold itself - the same error the 2026-08-28 entry records for
the planning files. It is now gitignored under a new "Personal context" section,
alongside the agent pointers. The convention travels; the entries do not. Note
`.gitignore` does not apply to an already-tracked file, so this needs
`git rm --cached context/sessions.md` once to take effect.

**Documented in the three places that enumerate `context/`.** Both folder trees,
project-brief's "Where to look", and a new "Keep the session log current" section
in `AGENTS.md` - deliberately there rather than in `CLAUDE.md`, so Cursor and
Copilot get it too. That section states the obligation the file actually has:
read the recent entries when starting cold, and update as work lands rather than
saving it for a wrap-up that a `/clear` may pre-empt.

**Two hooks, in personal settings only.** `.claude/settings.json` is gitignored,
so it is the right home for a Claude Code-specific reminder. `PreCompact` warns
when the log has no entry from today and stays silent when it does, so it is
signal rather than nagging; `SessionStart` injects a pointer to read the last
entries. Both were pipe-tested against the real payload and emit valid JSON.
Worth recording: `jq` is not installed on this machine, so neither command uses
it - the common formulation from the hook documentation would have failed
silently.

**A gap left open deliberately.** `.agents/claude/settings.json` is documented in
`CLAUDE.md` but never symlinked to `.claude/settings.json` by any setup step, so
it is currently dead - Claude Code never reads it. That makes it the wrong place
for a hook today. Wiring it would give consumers the same reminder, at the cost
of another setup step; not done without a decision.

**Still open, revised.** `/fix` still has no work-order template. The untracking
command above has not been run. And the reminder mechanism is Claude Code-only:
the `AGENTS.md` instruction covers every agent, but nothing enforces it for
Cursor or Copilot the way a hook does for Claude.
