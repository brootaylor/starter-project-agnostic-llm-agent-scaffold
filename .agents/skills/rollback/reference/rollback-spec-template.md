# Rollback: <feature name>

**Type:** Rollback
**Spec:** `docs/features/<name>.md`
**Base commit:** <filled in by /implement before the first step>
**Work status:** not started
**Target archive:** `context/history/features/NN-name.md`
**Target commit:** `<full 40-character commit SHA>`
**Target parent:** `<full 40-character parent SHA>`
**Reason:** Why this completed feature must be removed

> `Spec:` is the source spec being withdrawn. `/complete` resets its
> `**Status:**` line from `Complete` back to `Ready`: the contract still stands,
> only the implementation is going away.
>
> `Target commit` and `Target parent` must both be full 40-character SHAs.
> `/implement` refuses abbreviated or malformed values.
>
> `Work status:` tracks this work order only. It is not the spec's `**Status:**`
> line and must never be confused with it. `AGENTS.md` lists its five values
> under "The work order's own status"; nothing else is a valid value.

## Goal

Restore the product behaviour that existed before the target feature while
preserving the project's history and compatible work added afterward.

## Scope

### Reverse

- Product paths and behaviour introduced or changed by the target commit

### Preserve

- The original completed feature archive
- Every spec in `docs/features/` and `docs/specs/`
- Context files, skills, this rollback work order, and prototypes
- Later product behaviour confirmed compatible in the risk review

### Out of scope

- Cascading rollbacks of later features
- Destructive data migration
- Unrelated cleanup or refactoring

## Product paths

- `path/from/target-commit`

## Later-change risk

**Classification:** No overlap | Overlap, likely compatible | Dependency risk

| Later commit | Shared path or contract | Required handling |
| ------------ | ----------------------- | ----------------- |
| `<sha> subject` | `path` or contract | Preserve, adapt, or block |

## Build steps

- [ ] Apply the target commit's product diff in reverse with the Type: Rollback
  guard in `/implement`.
  - Done when: the reverse patch applies only to product paths, protected
    workflow paths are unchanged, and the staged diff matches the approved
    rollback scope.
- [ ] Make only the compatibility edits approved by the risk review.
  - Done when: later features named above still build and retain their stated
    behaviour. Remove this step when no compatibility work is required.
- [ ] Run the project checks and the observable removal path below.
  - Done when: every declared build, test, and acceptance command passes, the
    removed behaviour is no longer reachable, and unaffected core behaviour still
    works.

## Verification

- Build: `<command from AGENTS.md>`
- Tests: `<declared test command, or not configured>`
- Removed behaviour: `<route, UI action, CLI output, API, or public call that must no longer exist>`
- Regression path: `<small unaffected flow that must still work>`

## Notes for the "Ai"

- Stop on patch conflicts or evidence of an unplanned dependency.
- Do not delete the original feature archive.
- Do not broaden this rollback beyond the product paths and compatibility work
  listed above.
