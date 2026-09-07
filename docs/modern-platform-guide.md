# Modern Platform Guide

---

This file defines which web platform APIs and CSS features this project uses.
Agents must read it before writing any HTML, CSS, or JavaScript.

Where a native platform solution exists, use it. Do not reach for a JavaScript
workaround, a utility library, or a preprocessor feature unless this file
explicitly says otherwise.

**Cross-reference `docs/project-brief.md` → Browser support before using any
API not in your training data's confident support range. Flag gaps rather than
silently polyfilling or falling back.**

---

## HTML

| Task | Use | Do not use |
|------|-----|------------|
| Modal / dialog | `<dialog>` with `.showModal()` and `.close()` | `<div role="dialog">` with a custom focus trap and backdrop |
| Disclosure / accordion | `<details>` + `<summary>` | `<div>` with a click handler toggling `display` or `height` |
| Lazy-load images | `<img loading="lazy">` on **offscreen** images only, always with `width` and `height` | `loading="lazy"` on anything in the initial viewport; Intersection Observer scroll hacks; JS lazy-load libraries |
| Image alternative text | `alt` on every `<img>` — empty for decorative, descriptive otherwise, full stop at the end — see below | Omitting `alt`; `alt="image"` or the filename; describing an image the surrounding text already describes |
| Form validation | Constraint Validation API (`required`, `pattern`, `min`, `max`, `setCustomValidity`) | Manual JS validation re-implementing what the browser already provides |
| Popovers / tooltips | `popover` attribute + `popovertarget` — Baseline 2025, see below | Custom JS-positioned `<div>` overlays with manual show/hide logic |
| Inline Scalable Vector Graphics (SVG) icons | Inline `<svg>`, with `aria-hidden="true"` when decorative or `role="img"` plus a `<title>` when it carries meaning | `<img src="icon.svg">` or CSS `background-image` for icons; an unlabelled `<svg>` as the only content of a control |
| Progress indicators | `<progress value="" max="">` | `<div>` with a `width` style or animation |
| Meter / gauge values | `<meter value="" min="" max="">` | Custom `<div>` bars with inline width calculations |
| Native date / time input | `<input type="date">`, `<input type="time">` | Third-party date picker libraries for standard date entry |
| Colour input | `<input type="color">` | Custom colour picker libraries for basic colour selection |
| Toggle / switch | `<input type="checkbox">` styled with CSS | Custom `<div role="switch">` with manual Accessible Rich Internet Applications (ARIA) state management |
| Image with fallback | `<picture>` + `<source>` | JS-based format detection or `onerror` swapping |
| Responsive images | `srcset` and `sizes` attributes | JS that swaps `src` on resize |

**Alternative text.** Every `<img>` needs an `alt` attribute — the attribute itself,
present always, which is not the same as having a value. A missing `alt` and an empty
`alt=""` mean opposite things: `alt=""` tells a screen reader the image carries no
information and should be skipped, while a missing attribute leaves it guessing, and
most readers fall back to announcing the file name. So decorative images take `alt=""`,
and every other image takes a description of what it *conveys in this context* — not a
label for what it depicts. An image the adjacent prose already explains is decorative.

**End every non-empty `alt` with a full stop.** Screen readers derive prosody from
punctuation, and a sentence-final stop produces a pause before the next thing is
announced. Without it the alternative text runs into the following content in a single
breath, and the listener cannot tell where the image ended and the page resumed. This
is a house rule rather than a Web Content Accessibility Guidelines (WCAG) requirement,
and it is mildly contested: a trailing stop on a two-word fragment can read as an
over-long pause, and anyone running maximum punctuation verbosity will hear "period"
spoken aloud. The trade is deliberate — a slightly long pause is a smaller harm than
two sentences colliding.

**Lazy-loading.** Worth spelling out because the failure is a slower page, not a
broken one. The hero image is usually the Largest Contentful Paint (LCP) element, and
lazy-loading it defers the very thing that metric measures — the browser can no longer
start the fetch while parsing markup, and waits until layout has placed the image.
[web.dev is explicit](https://web.dev/articles/browser-level-image-lazy-loading):
"Don't lazy-load images that are likely to be in-viewport when the page loads,
especially LCP images." Leave above-the-fold images at the default eager loading,
and mark the LCP image `fetchpriority="high"` instead. `width` and `height` matter more on
lazy images than eager ones: an unloaded image is 0x0, so one that never intersects
the viewport never loads at all.

**Popover support, and what "modal" means.** `popover` is [Baseline 2025 - newly available](https://developer.mozilla.org/en-US/docs/Web/API/Popover_API), a lower support tier than everything else here, so check it against the project's browser targets rather than treating it as settled. Popovers are always non-modal; anything needing modal behaviour is `<dialog>`. And `<dialog>` only becomes modal - inert background, focus constrained, `aria-modal="true"` - when opened with `.showModal()`. A dialog opened with `.show()` constrains nothing.

---

## CSS

| Task | Use | Do not use |
|------|-----|------------|
| Two-dimensional layout | CSS Grid | Float layouts, `display: table` hacks, absolute positioning for layout |
| One-dimensional layout and spacing | Flexbox with `gap` | `margin` on child elements to simulate spacing, clearfix |
| Responsive fluid sizing | `clamp(min, preferred, max)` | JS `resize` listener computing sizes; separate breakpoint declarations for every step |
| Aspect ratio | `aspect-ratio` property | Padding-bottom percentage hack (`padding-top: 56.25%`) |
| Sticky positioning | `position: sticky` | JS `scroll` listener toggling a `.is-fixed` class |
| Theming and design tokens | CSS custom properties (`var(--token)`) | Sass variables for runtime theming, hardcoded values, JS class-swapping |
| CSS cascade management | `@layer` for ordering third-party vs project styles | Specificity hacks, `!important` to override cascade problems |
| Scroll snapping | `scroll-snap-type` / `scroll-snap-align` | JS scroll hijacking or animation loops |
| Subgrid alignment | CSS Subgrid (`grid-template-columns: subgrid`) | Nested grids with duplicated and manually synchronised track definitions |
| Component-level breakpoints | Container queries (`@container`) | Viewport `@media` queries used for component-level layout decisions |
| Parent state selectors | `:has()` | JS that walks the Document Object Model (DOM) to add a class to a parent element |
| CSS nesting | Native CSS nesting — see the callout below before migrating from Sass | Sass or PostCSS nesting plugins used solely to work around lack of native support |
| Logical / directional properties | `margin-inline`, `padding-block`, `inset-inline-start`, etc. | Physical properties (`margin-left`, `padding-top`) for layouts that need to support RTL |
| Smooth scrolling | `scroll-behavior: smooth`, guarded by `prefers-reduced-motion` — see below | Unguarded smooth scrolling; JS animation loop incrementing `scrollTop` |
| Text truncation (single line) | `text-overflow: ellipsis` with `overflow: hidden; white-space: nowrap` | JS that measures text and truncates the string |
| Custom focus ring | `outline` styled via CSS (never `outline: none` without a replacement) | Removing the outline and relying on a `:hover` state as a substitute |
| Colour functions | `oklch()` or `color-mix()` where targets support it | JS colour manipulation libraries for static colour derivations |

> [!IMPORTANT]
> **Native CSS nesting is not a drop-in replacement for Sass nesting, and both
> differences fail quietly.** A browser without
> [`CSSNestedDeclarations`](https://developer.mozilla.org/en-US/docs/Web/API/CSSNestedDeclarations)
> parses nested rules *in the wrong order* rather than failing to parse them, so
> the stylesheet loads and the cascade is silently wrong. And native nesting does
> no string concatenation: Sass's `&__child` Block-Element-Modifier (BEM) idiom
> does not produce `.block__child`, it is invalid. Convert BEM selectors by hand, and verify the
> computed cascade rather than assuming a visual match.

> [!IMPORTANT]
> **Nothing disables smooth scrolling for users who asked for less motion — you
> have to.** Neither
> [`scroll-behavior`](https://developer.mozilla.org/en-US/docs/Web/CSS/scroll-behavior)
> nor
> [`scrollIntoView()`](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView)
> consults `prefers-reduced-motion`; the animation simply plays, with nothing in the
> console and nothing visibly broken to anyone not affected by it. Declare the
> smoothness once in CSS and turn it off under the query:
>
> ```css
> html { scroll-behavior: smooth; }
> @media (prefers-reduced-motion: reduce) {
>   html { scroll-behavior: auto; }
> }
> ```
>
> Then call `scrollIntoView()` with no `behavior` argument. It defaults to `auto`,
> which defers to the computed CSS value — so the guard above covers the JavaScript
> path too. Passing `behavior: 'smooth'` explicitly opts back out of it.

---

## JavaScript

| Task | Use | Do not use |
|------|-----|------------|
| Intersection detection | `IntersectionObserver` | `scroll` event listener + `getBoundingClientRect()` on every frame |
| Element resize detection | `ResizeObserver` | `resize` window event listener querying element dimensions |
| DOM mutation detection | `MutationObserver` | `setInterval` polling the DOM for changes |
| Cancellable fetch | `AbortController` with a `signal` passed to `fetch` | Ignoring the resolved response after a race condition |
| Deep object clone | `structuredClone()` — throws `DataCloneError` on functions and DOM nodes, and clones class instances as plain objects | `JSON.parse(JSON.stringify(...))` — silently loses `undefined`, `Date`, `Map` and `Set` |
| Last array element | `array.at(-1)` | `array[array.length - 1]` |
| Safe own property check | `Object.hasOwn(obj, key)` | `obj.hasOwnProperty(key)` — fails on objects with no prototype |
| Optional chaining | `a?.b?.c` | `a && a.b && a.b.c` |
| Nullish fallback | `value ?? default` | `value \|\| default` — incorrectly treats `0` and `""` as falsy |
| Concurrent async (partial failure OK) | `Promise.allSettled()` | `Promise.all()` when partial failure should not abort everything |
| Concurrent async (all must succeed) | `Promise.all()` | Sequential `await` when operations are independent |
| URL construction | `URL` and `URLSearchParams` APIs | String concatenation or manual encoding for query parameters |
| Clipboard write | `navigator.clipboard.writeText()` — secure context only | `document.execCommand('copy')` — deprecated and unreliable |
| Smooth scroll to element | `element.scrollIntoView()` — leave `behavior` unset so it inherits the guarded CSS value | `scrollIntoView({ behavior: 'smooth' })`, which overrides the guard; JS animation loop incrementing `scrollTop` |
| Unique IDs | `crypto.randomUUID()` — secure context only | `Math.random()` string constructions for IDs that need to be unique |
| Type checking at runtime | `instanceof`, `typeof`, or `Array.isArray()` | String comparisons against `Object.prototype.toString` unless targeting older runtimes |
| Object immutability | `Object.freeze()` — shallow; nested objects stay mutable and need freezing themselves | Naming conventions or comments to signal "do not mutate" |
| Event delegation | Single listener on a parent with `event.target.closest()` | Attaching individual listeners to every child element in a list |
| Scheduling non-urgent work | `requestIdleCallback()` with a `setTimeout` fallback — see below | `setTimeout(fn, 0)` as the default path for deferring low-priority work |
| Animation frame sync | `requestAnimationFrame()` | `setInterval` or `setTimeout` for visual updates |

> [!IMPORTANT]
> **Three rows above fail silently rather than throwing where you would see it.**
>
> - [`requestIdleCallback()` is not Baseline](https://developer.mozilla.org/en-US/docs/Web/API/Window/requestIdleCallback).
>   Safari has it disabled by default (13.1 through 26.6; Technology Preview
>   only), so on Safari the callback simply never runs and the deferred work is
>   dropped with no error. Always pass the `timeout` option, and keep a
>   `setTimeout` fallback for work that must eventually happen.
> - [`navigator.clipboard.writeText()`](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard/writeText)
>   and [`crypto.randomUUID()`](https://developer.mozilla.org/en-US/docs/Web/API/Crypto/randomUUID)
>   are **secure-context only**. Over plain HTTP, `navigator.clipboard` and
>   `crypto.randomUUID` are `undefined`, so the call throws a `TypeError` at the
>   point of use rather than at load. `localhost` counts as secure; a LAN address
>   used for device testing does not.

---

## Patterns and conventions

### Prefer declarative over imperative

Reach for a CSS or HTML solution before a JavaScript one. JS is appropriate for
behaviour that cannot be expressed in markup or styles — not as a default.

**Example:** a disclosure widget should be `<details>` + `<summary>`, not a `<div>`
with a click handler and toggled class. The browser handles keyboard interaction
and ARIA state for free. Animation is *not* free - the Mozilla Developer Network
(MDN) is explicit that
[there is no built-in way to animate the open/closed transition](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/details);
it takes `::details-content`, `interpolate-size` and `transition-behavior`. That
is still less code than a custom disclosure, but budget for it rather than
expecting it to work by default.

### Avoid re-implementing what the browser provides

Check whether the platform already solves the problem before writing custom logic.
Common areas where agents drift toward unnecessary custom code:

- **Focus trapping** — a `<dialog>` opened with `.showModal()` makes the rest of the
  document inert and constrains focus to the dialog; do not write a focus trap for
  it. This does **not** apply to `.show()`, which opens a non-modal dialog and
  constrains nothing. Two things the browser still leaves to you: set initial
  focus with `autofocus`, and provide an explicit close control — Esc alone is not
  a sufficient close mechanism
- **Form validation** — the Constraint Validation API covers required fields, patterns,
  min/max ranges, and custom messages via `setCustomValidity`; do not re-validate
  fields the browser already validates
- **Smooth scroll** — `scroll-behavior: smooth` and `scrollIntoView` cover the
  standard cases; JS animation loops are not needed. Guard the CSS declaration with
  `prefers-reduced-motion` and leave `scrollIntoView()`'s `behavior` unset, so the
  preference is honoured in one place

### No layout with JavaScript

Do not use JavaScript to calculate or apply layout — widths, heights, positions,
or column counts. Use CSS Grid, Flexbox, container queries, and `clamp()`.
JavaScript layout code breaks on resize, causes layout thrash, and is harder to
maintain than a CSS equivalent.

### Minimal dependencies

Do not install a package to solve a problem the platform already solves. Before
adding a dependency, check whether a native API exists. If a library is genuinely
needed, stop and ask rather than installing it unilaterally — see the agent
behaviour rules in `docs/project-brief.md`.

---

## Notes for "Ai" agents

- **Check browser support first** — cross-reference `docs/project-brief.md` →
  Browser support before using any API. If support is insufficient for the project
  targets, flag it and propose an approach rather than silently falling back to a
  legacy workaround
- **The tables are not exhaustive** — if a native platform API exists for a task
  not listed here, use it and note it in the relevant component spec's Notes section
- **Specs take precedence on specifics** — if a component spec names a particular
  API or element to use, follow the spec. This file defines the default choice when
  the spec is silent
- **Native element, then ARIA** — always prefer a native semantic element over a
  generic element with ARIA roles. `<dialog>` is correct; `<div role="dialog">` is
  a fallback for environments where `<dialog>` cannot be used
- **If in doubt, ask** — if you are unsure whether a native solution covers a
  requirement fully, stop and ask rather than defaulting to a JS workaround
