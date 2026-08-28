# Build Plan

> Optional. A high-level roadmap of the features this project will have, and
> therefore which specs to write next. Write it directly, develop it through any
> AI conversation, or optionally run `/discovery`.

## This is a roadmap, not the work queue

**The work queue is the `**Status:**` line on each spec.** Every file in
`docs/features/` and `docs/specs/` carries `Draft`, `Ready`, or `Complete`.
`/feature` builds the next `Ready` spec; `/status` reports the queue by status;
`/complete` writes `Complete` back. That state machine is the project's real
progress tracker.

This file exists for the planning question the specs cannot answer: *what should
I write a spec for next?* It is a sketch of intent, ahead of any contract.

Where a checkbox here and a spec's `**Status:**` disagree, **the spec wins.**
`/complete` will tick a matching line here as a convenience, but nothing depends
on it, and a project can skip this file entirely and work straight from its specs.

The features that make up this project, high level and in rough build order, one
line each, no detail. Detail belongs in the spec, written from
`docs/specs/_component-template.spec.md`.

## Continuing after the initial build

This is a living roadmap, not a plan that freezes when the first release is
done. Keep completed items checked, then append new unchecked features as the
project grows. Optional milestone headings such as `## MVP` and `## Post-MVP`
keep a longer plan readable.

Do not renumber completed features; archived work refers back to those numbers.
Continue with the next unused number. If a new feature materially changes the
product direction, users, data, stack, monetization, UI/UX, or deployment, update
`project-plan.md` and `docs/project-brief.md` as needed.

**A line here is not buildable on its own.** Adding an item is a note to yourself
to write the spec. `/feature` will refuse a roadmap line with no `Ready` spec
behind it: it can offer to draft one as `Draft`, but only a human promotes a spec
to `Ready`. That gate is deliberate.

Scaffolding the app (create-next-app, etc.) and prototyping the look are
pre-build steps, not features (see the README), so don't list them here. Start
with your first real slice of functionality.

A common order that works well: build the core UI with placeholder data first,
then wire up data, auth, and integrations. Add deployment readiness only when
the app is worth shipping or a provider config change is part of the work. Adapt
it to your project.

## Format

Use checkboxes. Each item should be a feature-sized outcome, not a loose task or
a whole product area.

Good:

- [ ] 1. **Skill submission** - upload a skill package and save its metadata
- [ ] 2. **Validation result** - run checks and show pass/fail status for a skill
- [ ] 3. **Directory listing** - browse and filter published skills
- [ ] 4. **Deployment readiness** - configure Render or Vercel and verify the
  production build

Avoid:

- Upload stuff
- Database
- Make it look nice
- Auth, billing, dashboard, validation, and deploy

If your first pass is just rough bullets, that is okay. Nothing here blocks
building, because building runs off the specs.

Run `/overview` after filling both planning docs if you want a distilled product
context file. `docs/project-brief.md` remains the authority on stack, conventions,
browser targets, and accessibility.

- [ ] 1. **Feature one** - description
- [ ] 2. **Feature two** - description
