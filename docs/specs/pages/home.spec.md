# Page Spec: Home

**Status:** Draft
**Last updated:** <!-- e.g. 2025-01-15 -->

> See `docs/project-brief.md` → Spec conventions for the full status key and agent behaviour rules.

---

## Purpose

> A single paragraph describing what problem this page solves, who uses it,
> and when. Avoid describing how it looks — describe why it exists.

The default landing page for the project. The first page a visitor sees —
it should communicate the project's purpose clearly and direct users to the
most important content or actions.

---

## Route

| Route | Access |
|-------|--------|
| `/` | Public |

---

## Dependencies

Layouts, components, utilities, or design token categories this page relies on.
List anything an agent would need to locate or implement before building this page.

| Type | Name | Location |
|------|------|----------|
| Layout | `MainLayout` | `src/layouts/MainLayout/` — spec at `docs/specs/layouts/main-layout.spec.md` *(Draft)* |
| Component | `SiteNav` | `src/components/SiteNav/` — spec at `docs/specs/components/site-nav.spec.md` *(spec not yet written)* |
| Component | `Hero` | `src/components/Hero/` — spec at `docs/specs/components/hero.spec.md` *(spec not yet written)* |
| Component | `ComponentGrid` | `src/components/ComponentGrid/` — spec at `docs/specs/components/component-grid.spec.md` *(spec not yet written)* |
| Component | `SiteFooter` | `src/components/SiteFooter/` — spec at `docs/specs/components/site-footer.spec.md` *(spec not yet written)* |
| Tokens | Colour | `docs/design-tokens.md#colour` |
| Tokens | Spacing | `docs/design-tokens.md#spacing` |

> **Blocked** — all four component specs must be written and set to `Ready` before this page can be implemented.

---

## Behaviour

How the page behaves on first load and in response to user interaction.

### Default / initial state

Renders using `MainLayout` with `pageTitle="Home"`. Composes `SiteNav`, `Hero`,
`ComponentGrid`, and `SiteFooter` in order within the main content area. No
data fetching — all content is static.

### States

This is a static page with no loading or error states.

### Interaction rules

- All interaction is delegated to child components — this page has no interaction logic of its own

---

## Accessibility

The accessibility requirements this page must meet. The agent must not
consider the page complete until all of these are satisfied.

- Page title must be set to reflect the current page
- Landmark regions must be present (`<header>`, `<main>`, `<footer>`)
- Focus must move to the main content area on navigation

---

## Error handling

- This is a static page — no async operations and no error states
- If a required component is missing or throws during render, do not catch — let the error surface

---

## Styling notes

Token categories and CSS patterns specific to this page.
The agent must read `docs/design-tokens.md` before writing any styles.

- **Tokens used** — spacing (section gaps and padding), colour (page background)
- **CSS patterns** — page-level styles scoped to `.page-home`; component-level styles are owned by each component
- **Dark mode** — page background must reference CSS custom properties scoped to `[data-theme]`; no hardcoded values

---

## Test cases

The agent will generate one test function per entry. IDs must be unique within
this spec and must match the test file exactly.

### Render

- [ ] **TC-01** — Page renders within `MainLayout`
- [ ] **TC-02** — `SiteNav`, `Hero`, `ComponentGrid`, and `SiteFooter` are all present in the rendered output

### Accessibility

- [ ] **TC-03** — Document title is set to the correct page title
- [ ] **TC-04** — Landmark regions (`<header>`, `<main>`, `<footer>`) are present

---

## Out of scope

- Data fetching or server-side rendering
- Authentication or access control
- Page-level animation or transitions

---

## Example usage

How this page is implemented depends on the active framework selection in
`docs/project-brief.md`.

**Vanilla:**

```html
<!-- src/index.html -->
<!DOCTYPE html>
<html lang="en">
  <head><title>Home</title></head>
  <body>…</body>
</html>
```

**Astro:**

```astro
<!-- src/pages/index.astro -->
---
import MainLayout from '../layouts/MainLayout.astro';
---
<MainLayout pageTitle="Home">
  <!-- page content -->
</MainLayout>
```

**React:**

```jsx
// src/pages/Home.jsx
export function Home() {
  return (
    <MainLayout pageTitle="Home">
      {/* page content */}
    </MainLayout>
  );
}
```

**Svelte:**

```svelte
<!-- src/pages/Home.svelte -->
<MainLayout pageTitle="Home">
  <!-- page content -->
</MainLayout>
```

---

## Notes for "Ai" implementation

Additional context, constraints, and implementation guidance that the agent
should be aware of before writing any code.

- **Public page** — no authentication required
- **Static content only** — no data fetching; all content is hardcoded or passed as props to child components
- **ComponentGrid** — should render links to individual component documentation pages; the destination pages are out of scope for this spec and must be defined separately before `ComponentGrid` can be fully implemented
- **Related specs** — `docs/specs/layouts/main-layout.spec.md`; component specs listed in Dependencies above
- **Gotchas** — do not begin implementation until all component specs in Dependencies are `Ready`

---

## Draft → Ready checklist

Complete every item before changing the status to `Ready`.
**Agents must not begin implementation until the status is `Ready`.**

- [x] Purpose describes the *why*, not the *how*
- [ ] All props are named, typed, and have a default where applicable
- [ ] All events include a payload description
- [x] Every meaningful state is listed in the States table
- [x] Interaction rules cover all non-obvious behaviours
- [x] Accessibility requirements are specific, not generic
- [x] At least one test case exists per behaviour and state
- [x] Out of scope section is filled in
- [x] Example usage matches the active framework
- [ ] Notes for "Ai" implementation are complete — blocked until component specs are written
