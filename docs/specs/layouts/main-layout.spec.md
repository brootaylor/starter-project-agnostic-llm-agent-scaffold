# Layout Spec: MainLayout

**Status:** Draft
**Last updated:** <!-- e.g. 2025-01-15 -->

> See `docs/project-brief.md` → Spec conventions for the full status key and agent behaviour rules.

---

## Purpose

> A single paragraph describing what problem this layout solves, who uses it,
> and when. Avoid describing how it looks — describe why it exists.

The primary layout wrapping all pages. Provides a consistent structure with
a header, main content area, and footer so individual pages only need to
concern themselves with their own content.

---

## Dependencies

Components, utilities, assets, or design token categories this layout relies on.
List anything an agent would need to locate or implement before building this layout.

| Type | Name | Location | Status |
|------|------|----------|--------|
| Component | `ThemeToggle` | `src/components/ThemeToggle/` — spec at `docs/specs/components/theme-toggle.spec.md` | Draft — must be `Ready` first |
| Tokens | Colour | `docs/design-tokens.md#colour` | n/a |
| Tokens | Spacing | `docs/design-tokens.md#spacing` | n/a |
| Tokens | Typography | `docs/design-tokens.md#typography` | n/a |

---

## Interface

The props and attributes that define how this layout is used.
The agent will derive the implementation interface directly from this section.

### Props / Attributes

| Name | Type | Required | Default | Description |
|------|------|:--------:|---------|-------------|
| `children` | element / slot | ✓ | — | The page content to render in the main content area |
| `pageTitle` | string | ✓ | — | Sets the page `<title>` and main heading |

### Events / Callbacks

None — this layout emits no events. Interactivity is handled by child components.

---

## Structure

A description of the regions this layout renders and their purpose.

| Region | Element | Purpose |
|--------|---------|---------|
| Header | `<header>` | Site navigation and global controls (e.g. theme toggle) |
| Main | `<main>` | Page content passed via children / slot |
| Footer | `<footer>` | Secondary navigation and legal links |

---

## Behaviour

How the layout behaves on first render and in response to user interaction.

### Default / initial state

Renders the header, main content area, and footer. The `pageTitle` is applied
to the document `<title>` and rendered as the visible `<h1>` inside `<main>`.
The `ThemeToggle` is rendered in the header. The skip navigation link is present
but visually hidden until focused.

### States

| State | Trigger | Visual / functional result |
|-------|---------|---------------------------|
| default | Page loads | Full layout rendered with header, main, and footer |
| skip-link focused | Keyboard user tabs to the skip link | Skip link becomes visible; activating it moves focus to `#main-content` |

### Interaction rules

- When the skip navigation link is activated → focus moves to `#main-content`
- The layout itself has no other interactive behaviour — interactivity is handled by child components

---

## Accessibility

The accessibility requirements this layout must meet. The agent must not
consider the layout complete until all of these are satisfied.

- Must use semantic HTML landmark elements (`<header>`, `<main>`, `<footer>`)
- `pageTitle` must be reflected in both the document `<title>` and a visible `<h1>`
- A skip navigation link must be present to allow keyboard users to bypass the header

---

## Error handling

- If `pageTitle` is an empty string, render the `<h1>` as empty and log a `console.warn` in development — do not throw
- If `children` / slot content is absent, render the layout shell with an empty `<main>` — do not throw

---

## Styling notes

Token categories and CSS patterns specific to this layout.
The agent must read `docs/design-tokens.md` before writing any styles.

- **Tokens used** — colour (header/footer backgrounds), spacing (padding, gaps), typography (base font settings)
- **CSS patterns** — Block-Element-Modifier (BEM): `.layout` (block), `.layout__header`, `.layout__main`, `.layout__footer`, `.layout__skip-link` (elements)
- **Dark mode** — background and text colours for header and footer must reference CSS custom properties scoped to `[data-theme]`; no hardcoded values

---

## Test cases

The agent will generate one test function per entry. IDs must be unique within
this spec and must match the test file exactly.

### Render

- [ ] **TC-01** — Renders `<header>`, `<main>`, and `<footer>` landmark elements
- [ ] **TC-02** — `pageTitle` sets the document `<title>`
- [ ] **TC-03** — `pageTitle` is rendered as a visible `<h1>` inside `<main>`
- [ ] **TC-04** — `ThemeToggle` is rendered inside `<header>`
- [ ] **TC-05** — Children / slot content is rendered inside `<main>`

### Interaction

- [ ] **TC-06** — Skip navigation link is present and points to `#main-content`
- [ ] **TC-07** — Skip navigation link receives focus before other header content in tab order

### Accessibility

- [ ] **TC-08** — Skip link is visually hidden by default and visible when focused

### Edge cases

- [ ] **TC-09** — Renders without throwing when `pageTitle` is an empty string

---

## Out of scope

- Page-specific header content beyond the `ThemeToggle`
- Multiple layout variants (e.g. sidebar, full-width)
- Animated page transitions

---

## Example usage

How this layout is implemented depends on the active framework selection in
`docs/project-brief.md`. This is one of the scaffold's shipped examples, so every
framework is shown — the stack has not been chosen yet, and the `[active]` marks
in the brief are a placeholder default, not a recommendation. **In your own specs,
keep only the example matching your active selection and delete the rest.** That
is what the Example usage row in the checklist below is asking for.

**Vanilla:**

```html
<div class="layout">
  <header>…</header>
  <main id="main-content">…</main>
  <footer>…</footer>
</div>
```

**Astro:**

```astro
<MainLayout pageTitle="Home">
  <p>Page content goes here</p>
</MainLayout>
```

**React:**

```jsx
<MainLayout pageTitle="Home">
  <p>Page content goes here</p>
</MainLayout>
```

**Svelte:**

```svelte
<MainLayout pageTitle="Home">
  <p>Page content goes here</p>
</MainLayout>
```

---

## Notes for "Ai" implementation

Additional context, constraints, and implementation guidance that the agent
should be aware of before writing any code.

- **Skip link** — must point to `#main-content` and be visually hidden until focused; use a CSS class (e.g. `.skip-link`) with a focus style override, not `display: none` (which removes it from tab order)
- **CSS** — import from `./MainLayout.css` or `./MainLayout.scss` depending on active styles selection in `docs/project-brief.md`
- **Related specs** — `ThemeToggle` component (`docs/specs/components/theme-toggle.spec.md`); used by `docs/specs/pages/home.spec.md`
- **Gotchas** — `ThemeToggle` spec is currently `Draft`; do not implement this layout until `ThemeToggle` is `Ready` and implemented

---

## Draft → Ready checklist

Complete every item before changing the status to `Ready`.
**Agents must not begin implementation until the status is `Ready`.**

- [x] Purpose describes the *why*, not the *how*
- [x] All props are named, typed, and have a default where applicable
- [x] All events include a payload description
- [x] Every meaningful state is listed in the States table
- [x] Interaction rules cover all non-obvious behaviours
- [x] Accessibility requirements are specific, not generic
- [x] At least one test case exists per behaviour and state
- [x] Out of scope section is filled in
- [ ] Every dependency that has a spec of its own has reached `Ready` — `ThemeToggle` is still `Draft`
- [x] Example usage matches the active framework
- [x] Notes for "Ai" implementation are complete
