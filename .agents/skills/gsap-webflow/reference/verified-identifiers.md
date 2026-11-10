---
type: reference
title: Verified identifiers
summary: >
  Every exact attribute name, class name, variable name, SVG id and selector used across the
  GSAP-in-Webflow corpus, each traced to the cloneable it was copied from; copy these
  verbatim and never reconstruct one from memory, because a wrong identifier fails silently.
level: beginner
tags: [reference, identifiers, attributes, classes, variables, selectors, anti-hallucination]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: apple-style-image-sequence-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-video-animation-on-scroll
  - lesson: gsap-disintegration-effect-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-disintegration-effect-on-scroll
  - lesson: build-gsap-draggable-cards-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-draggable-cards
  - lesson: webflow-spotlight-prism-effects
    cloneable: https://webflow.com/made-in-webflow/website/spotlight-animation-svg-filter-gsap
  - lesson: webflow-glassy-button-svg-gsap
    cloneable: https://webflow.com/made-in-webflow/website/liquid-glass-crystal-button
  - lesson: how-to-build-infinite-looping-marquees-webflow
    cloneable: https://webflow.com/made-in-webflow/website/060-gsap-infinite-marquee
  - lesson: master-accessible-cms-sidebars-webflow
    cloneable: https://webflow.com/made-in-webflow/website/cms-jquery-detailed-info-sidebar
  - lesson: how-to-build-cms-scatter-animations-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-grow-and-scatter
  - lesson: master-webflow-staggered-scroll-animations
    cloneable: https://webflow.com/made-in-webflow/website/gsap-staggered-animation-update
  - lesson: growing-border-animations-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-growing-border-animation
  - lesson: sticky-scroll-video-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-set-action-pin-video-on-scroll
  - lesson: accessible-gsap-popup-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-set-action-prevent-scrolling
  - lesson: webflow-dark-mode-gsap
    cloneable: https://webflow.com/made-in-webflow/website/set-actions-in-interactions-with-gsap
  - lesson: control-gsap-interactions-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-event-settings
  - lesson: master-webflow-gsap-state-machines
    cloneable: https://webflow.com/made-in-webflow/website/gsap-event-settings
  - lesson: build-3d-cms-cubic-gallery-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-cubic-scrollable-image-gallery
  - lesson: swiss-style-gsap-animation-webflow
    cloneable: https://webflow.com/made-in-webflow/website/the-jam-swiss-style
  - lesson: master-webflow-gsap-interactions
    cloneable: https://webflow.com/made-in-webflow/website/gsap-interactions-core-concepts
  - lesson: fix-webflow-gsap-flicker
    cloneable: https://webflow.com/made-in-webflow/website/gsap-remove-flickering
assumes: []
related: [interactions-model, target-filters]
---

## How to use this page

**Copy from here. Do not reconstruct from memory, and do not "correct" a name that looks
misspelled.** Several of these read like typos and are not: `fs-mirrorinput-element`,
`toc-subitems`, `aria-label="Breadcrumb"`. Attribute selectors are case-sensitive, and a
wrong identifier does not throw an error — the script simply never finds the element, so the
reader gets silence and no way to debug it.

If an identifier is not on this page, say so. Do not supply a plausible-looking substitute.

Names marked **presence only** take no value: the attribute's existence is the signal.

## Script-driven components (`fc-*`)

These are the highest-risk names on the page, because they are the contract between markup you
write and a script you don't read.

### `fc-image-scrubbing` — canvas image sequence on scroll

| Attribute | Values | Default |
|---|---|---|
| `fc-image-scrubbing` | `component` | — |
| `fc-image-scrubbing-urls` | comma-separated list of image URLs | — (required) |
| `fc-image-scrubbing-fit` | `contain` · `cover` | `contain` |
| `fc-image-scrubbing-fit-landscape` | `contain` · `cover` | `contain` |
| `fc-image-scrubbing-fit-portrait` | `contain` · `cover` | `contain` |
| `fc-image-scrubbing-fps` | number | `24` |
| `fc-image-scrubbing-start-point` | ScrollTrigger `start` syntax | `top top` |
| `fc-image-scrubbing-end-point` | ScrollTrigger `end` syntax | `bottom bottom` |

Provenance: `apple-style-image-sequence-webflow` ·
[cloneable](https://webflow.com/made-in-webflow/website/gsap-video-animation-on-scroll)

### `fc-disintegration` — scatter-into-fragments on scroll

| Attribute | Values | Default |
|---|---|---|
| `fc-disintegration` | `component` | — (required) |
| `fc-disintegration-active-on-mobile` | `true` · `false` | `true` |
| `fc-disintegration-rotation-angle` | degrees | `30` |
| `fc-disintegration-rotation-amplitude` | pixels | `40` |
| `fc-disintegration-cascade-offset` | number or percentage | `5` |
| `fc-disintegration-start-point` | ScrollTrigger `start` syntax | `top top` |
| `fc-disintegration-end-point` | ScrollTrigger `end` syntax | `bottom top` |

Note the end default differs from `fc-image-scrubbing`: `bottom top`, not `bottom bottom`.

Provenance: `gsap-disintegration-effect-webflow` ·
[cloneable](https://webflow.com/made-in-webflow/website/gsap-disintegration-effect-on-scroll)

### `fc-draggable-card` — throwable card stack

| Attribute | Values | Default |
|---|---|---|
| `fc-draggable-card` | `wrapper` · `component` · `reset` | — (required) |
| `fc-draggable-card-rotation` | degrees | `45` |
| `fc-draggable-card-reset-duration` | seconds | `0.2` |
| `fc-draggable-card-throw-duration` | seconds | `0.5` |
| `fc-draggable-card-throw-distance` | pixels | `1000` |
| `fc-draggable-card-throw-rotation` | degrees | `45` |
| `fc-draggable-card-threshold` | pixels | `100` |
| `fc-draggable-card-delay` | seconds | `0` |
| `fc-draggable-card-ease` | GSAP ease name | `power4.out` |

`wrapper` goes on the drag-zone div, `component` on each card, `reset` on the reset button.

Provenance: `build-gsap-draggable-cards-webflow` ·
[cloneable](https://webflow.com/made-in-webflow/website/gsap-draggable-cards)

### Presence-only markers

| Attribute | On | Provenance |
|---|---|---|
| `fc-prism-button` | the button whose SVG prism filter animates on hover/focus | `webflow-spotlight-prism-effects` |
| `fc-glass-button` | each glassy button; the loader clones a filter per match | `webflow-glassy-button-svg-gsap` |

Both are found with `document.querySelectorAll('[fc-…-button]')`, so the class name is
irrelevant — that is the point of them.

### `fc-gsap-staggered` — no-code staggered reveal

**The exception to the naming pattern: only the marker is prefixed. Every option is a bare attribute
name.** Do not "complete" them into `fc-gsap-staggered-each` and friends — that is wrong.

| Attribute | Values | Value in the reference |
|---|---|---|
| `fc-gsap-staggered` | `list` | — **required** |
| `threshold` | visibility fraction, 0–1 | `0.4` |
| `each` | seconds — fixed gap between items | `0.2` |
| `amount` | seconds — total time split across the row | `0.5` |
| `duration` | seconds — one item's animation | `0.5` |
| `offset-y` | length | `3rem` |
| `from` | `start` · `end` · `center` · `edges` · `random` · zero-based index | `start` |
| `ease` | GSAP ease name — each item's motion | `power3.out` |
| `staggered-ease` | GSAP ease name — distribution of the delays | `power1.out` |

`each` and `amount` are mutually exclusive: **if `amount` is set, `each` is ignored.**

**`offset-y`, not `offsetY`** — the lesson prose has the camelCase form, which is wrong. HTML
lower-cases attribute names, so `offsetY` arrives as `offsety` and never matches. This is a real
identifier error, caught 2026-07-27; the cloneable is authoritative.

Provenance: `no-code-gsap-staggered-animations-webflow` ·
[cloneable](https://webflow.com/made-in-webflow/website/gsap-staggered-animation) · names confirmed by
the corpus author, since the lesson carries no attribute table.

## Interaction-targeting attributes

Used with the target/filter/reference composition, never with a class. See [[target-filters]].

| Attribute | Values | Used for | Provenance |
|---|---|---|---|
| `data-animate` | `horizontal-marquee` · `reversed-horizontal-marquee` | marks each marquee wrapper; the action targets its direct children | `how-to-build-infinite-looping-marquees-webflow` |
| `data-animate` | `sidebar` (overlay and content wrapper get their own distinct values) | the CMS sidebar panel | `master-accessible-cms-sidebars-webflow` |
| `data-sidebar-open` | presence only | the card's open button | `master-accessible-cms-sidebars-webflow` |
| `data-sidebar-close` | presence only | **both** the close button and the overlay, so one interaction handles button-click and click-outside | `master-accessible-cms-sidebars-webflow` |
| `data-sidebar-close-button` | presence only | the control focus lands on when the panel opens | `master-accessible-cms-sidebars-webflow` |
| `data-animate-item` | presence only | each grid/collection item | `how-to-build-cms-scatter-animations-webflow`, `master-webflow-staggered-scroll-animations` |
| `data-animate-image` | presence only | the image inside a scatter item | `how-to-build-cms-scatter-animations-webflow` |
| `data-animate-link` | presence only | the hovered link | `growing-border-animations-webflow` |
| `data-animate-link-border` | presence only | the link's wrapper, targeted via *Direct parent of → Trigger element* | `growing-border-animations-webflow` |
| `data-content-role` | `text` | the four Swiss-poster text blocks in one staggered action | `swiss-style-gsap-animation-webflow` |
| `element-role` | `important-cta` · `open-popup` · `close-popup` | shared roles so one interaction serves several controls | `control-gsap-interactions-webflow` |
| `element-change-color-role` | one value per state | the state-machine color badges | `master-webflow-gsap-state-machines` |
| `dialog-animation` | `close` | on the close button **and** the outside-click overlay | `accessible-gsap-popup-webflow` |
| `remove-flicker` | presence only | every element the FOUC hide rule should cover | `fix-webflow-gsap-flicker` |

**`data-animate` carries different value vocabularies in different builds.** It is a
convention, not a global contract — check the lesson before assuming a value.

## Class names

| Class | Role | Provenance |
|---|---|---|
| `cc-animate-image` | end-state helper: `filter: blur(0px)` | `master-webflow-gsap-interactions` |
| `cc-animate-image-wrapper` | end-state helper: `border-radius: 0px` | `master-webflow-gsap-interactions` |
| `is-sticky` | combo class bundling `position: sticky` + `top: 0` + `z-index: 100` | `sticky-scroll-video-webflow` |
| `is-marquee` | combo on the main container: `overflow: hidden`, no horizontal padding | `how-to-build-infinite-looping-marquees-webflow` |
| `overflow-hidden` | scroll lock, applied to `body` | `master-accessible-cms-sidebars-webflow` |
| `on-hover` | toggled by a `set` action; the CSS transition does the motion | `growing-border-animations-webflow` |
| `in-view` | the scroll-reveal end state; carries the transitions | `master-webflow-staggered-scroll-animations` |
| `dark-mode` | carries the dark variable mode; toggled on `<html>` | `webflow-dark-mode-gsap` |
| `theme-transition` | added at `0s`, removed at `0.8s`, so color transitions exist only during the switch | `webflow-dark-mode-gsap` |
| `mode-toggle` | the theme toggle button; the persistence script queries `.mode-toggle` | `webflow-dark-mode-gsap` |
| `popup-closing-overlay` | outside-click catcher, stacked behind the popup | `accessible-gsap-popup-webflow` |
| `section_content-wrapper` | hidden by head CSS, restored as the interaction's first action | `swiss-style-gsap-animation-webflow` |
| `pictures_cube-item` | each cube face; per-face transform bound from CMS fields | `build-3d-cms-cubic-gallery-webflow` |

**Every class a `set` action adds or removes must stay applied to something in your style guide.**
Otherwise Webflow flags it as unused and a style cleanup deletes it — publishing keeps it, a tidy-up
does not. This applies to `overflow-hidden`, `in-view`, `dark-mode`, `theme-transition`, `is-sticky`
and `on-hover`. See [[unused-class-cleanup]].

## Variables

### Webflow variables (you create these; the name is yours to match)

| Variable | Type | Range / role | Provenance |
|---|---|---|---|
| `transform-factor` | number | `0 → 1`, multiplied into three `calc()` transforms | `sticky-scroll-video-webflow` |
| `move-factor` | number | `1 → 0` drives the scatter | `how-to-build-cms-scatter-animations-webflow` |
| `columns`, `rows` | number | the grid the centering maths reads | `how-to-build-cms-scatter-animations-webflow` |
| `number-of-columns` | number | one **variable mode per breakpoint** (e.g. 3 / 3 / 2 / 1) | `master-webflow-staggered-scroll-animations` |
| `image-left-margin`, `image-top-margin` | — | nudged between `-1%` and `1%` by a mouse-move interaction | `master-webflow-gsap-interactions` |

### Webflow-generated CSS variable names

**`--_cards---number-of-columns`** is Webflow's generated name for the `number-of-columns`
variable in the *cards* collection. Three dashes in the middle, two at the start. This form is
generated, not chosen — read it out of the Designer's *Copy CSS* rather than deriving it.

### Custom CSS variables in the embeds

Configuration knobs, meant to be tuned:

| Variable | Default | Role | Provenance |
|---|---|---|---|
| `--animation-mode` | `1` | `0` linear · `1` exponential · in-between blends | `master-webflow-staggered-scroll-animations` |
| `--step-or-max-delay` | `0.5s` | fixed step (linear) or delay ceiling (expo) | `master-webflow-staggered-scroll-animations` |
| `--expo-base` | `0.5` | deceleration curvature, useful range `0.3–0.6` | `master-webflow-staggered-scroll-animations` |

Internal, computed — do not hand-edit:

- `--index`, `--linear-part`, `--expo-part`, `--final-curve` — `master-webflow-staggered-scroll-animations`
- `--i`, `--x`, `--y`, `--shift-x`, `--shift-y` — `how-to-build-cms-scatter-animations-webflow`
- `--move-factor`, `--columns`, `--rows` — the CSS-side names, which must match the Webflow
  variable names above

### CMS field names — 3D cube gallery

`order` (integer, max 6) · `origin-x`, `origin-y` (labeled *Origin Left* / *Origin Top*) ·
`move-x`, `move-y`, `move-z` · `rotate-x`, `rotate-y`, `rotate-z`.

**Rotate fields are integers, move fields are decimals.** Positioning faces in 3D needs that
precision; small errors read as visibly misaligned faces. Provenance:
`build-3d-cms-cubic-gallery-webflow`.

## SVG ids and selectors

| Identifier | Referenced as | Provenance |
|---|---|---|
| `prism-basic` | `filter: url(#prism-basic)` | `webflow-spotlight-prism-effects` |
| `glass` | `backdrop-filter: url(#glass)` | `webflow-glassy-button-svg-gsap` |
| `glass-1`, `glass-2`, … | script-generated per-button clones; they replace `url(#glass)` at runtime | `webflow-glassy-button-svg-gsap` |
| `jm` | `clip-path: url(#jm)`, with `clipPathUnits="objectBoundingBox"` | `swiss-style-gsap-animation-webflow` |

The prism script selects its filter primitives by their `in` values —
`feOffset[in=red]`, `feOffset[in=green]`, `feOffset[in=blue]`. **Rename `red`/`green`/`blue`
in the filter and the selectors stop matching.**

Runtime handles from the glass build: `window.__glassNodes`, `window.__glassMapHref`, the
`glass:filters-ready` custom event, and `MAP_URL` at the top of the loader script.

## Webflow platform identifiers

| Identifier | Meaning |
|---|---|
| `w-mod-js` | Webflow adds this to `<html>` **only when JavaScript is running**. Scope FOUC hide rules to `.w-mod-js` or content stays hidden forever without JS |
| `Webflow.require("ix3")` | the interactions runtime; `.emit("<Custom event name>")` fires a custom-event interaction from your own code |

## ARIA and semantic attributes

| Attribute | Value | Where |
|---|---|---|
| `role` | `dialog` | the modal / sidebar panel |
| `aria-modal` | `true` | same element |
| `aria-labelledby` | the heading's `id` — `modal-title` in the popup build, the **CMS slug** in the sidebar build so each dialog announces its own title | same element |
| `tabindex` | `-1` | the panel: focusable by script, not in the tab order |
| `aria-label` | e.g. `close dialog`, `open join newsletter dialog` | icon-only controls |
| `aria-hidden` | `true` | decorative icons, and the `<canvas>` in the image sequence |
| `type` | `button` | close/open controls, built as **custom `button` elements** — Webflow's native button renders as an `<a>` |
| `role` | `listitem` | the collection item the focus-trap script walks up to |

Script event names used for keyboard parity in the prism and glass builds: `pointerenter`,
`pointerleave`, `focus`, `blur` — never `mouseenter` alone, or keyboard users lose the effect.

## Script constants

Tunable, and named exactly like this in the cloneables:

- Prism: `DX_RED`, `DY_RED`, `DX_GREEN`, `DY_GREEN`, `DX_BLUE`, `DY_BLUE`, `DURATION`, `EASE`
- Glass: `HOVER_SCALE`, `HOVER_TRANSF_SCALE`, `HOVER_BLUR`, `DURATION`, `EASE`, `MAP_URL`

## Why this page exists — the documented failure

Every identifier above was copied from a cloneable, because reconstructing them from video
transcripts **provably** produces wrong names. The Academy code-gap pass (D44) caught nine of
them across the wider corpus. Only one falls inside this KB's scope, but the failure *modes*
generalize, and they are what to guard against:

| Failure mode | Wrong | Right |
|---|---|---|
| Homophone heard by ASR | `fc-fluid-gradient-gooey` | `fc-fluid-gradient-gui` |
| Whole prefix invented | `FC-drop-*` | `fc-dropdown-*` |
| Prefix casing | `FC-ripples*`, `FC-marquee-*` | `fc-ripples*`, `fc-marquee-*` |
| Compound word split | `fs-mirror-input-element` | `fs-mirrorinput-element` |
| Suffix dropped | `var(--glow)` | `var(--glow-color)` |
| ARIA value casing | `aria-label="breadcrumb"` | `aria-label="Breadcrumb"` |
| Hyphen inserted | class `toc-sub-items` | class `toc-subitems` |
| Units misread | pixel tuning ranges | fractions — `primitiveUnits="objectBoundingBox"` |
| **camelCase in an HTML attribute** | `offsetY` | **`offset-y`** — the parser lower-cases it to `offsety` |
| **Prefix pattern over-applied** | `fc-gsap-staggered-each` | **`each`** — only the marker is prefixed |

*Rows one to seven come from Academy lessons outside this KB's GSAP scope and are listed as evidence of
the failure class. **The last three are in scope.** Row eight:
`webflow-glassy-button-svg-gsap`, where `primitiveUnits="objectBoundingBox"` makes every value a
fraction of the button's own box — which is why `stdDeviation: 0.02` and `scale: 0.8` are correct and
the plausible "1–5" and "20+" ranges were wrong. Rows nine and ten:
`no-code-gsap-staggered-animations-webflow`, both found on 2026-07-27 — the first is an error in the
lesson prose, the second is an error **this KB nearly made**, by reasoning from the naming convention
every other component follows. The convention is real; this component just doesn't follow it.*

Not one of these throws an error. Every one produces an element that is never found, a
variable that resolves to nothing, or a filter tuned by three orders of magnitude — and a
reader with no way to tell why.
