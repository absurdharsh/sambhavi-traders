---
type: reference
title: Recipes — task-shaped answers
summary: >
  Pages answering "how do I build X", each one task-scoped with the variants and a rule for
  choosing between them; still being filled in, so say so plainly when a task has no page yet.
level: beginner
tags: [meta, index, recipes]
status: verified
generated: 2026-07-27
verified_against: lesson-prose
sources: []
assumes: []
related: [accessible-dialog, scroll-lock]
---
| Page | What it answers |
|---|---|
| [[accessible-dialog]] | Building a modal or slide-in panel: one reversible timeline, the four required ARIA attributes, outside-click, and the CMS-per-item variant |
| [[focus-trap]] | Keeping keyboard focus inside an open panel — the script, the attribute contract it expects, and why the 50ms timeouts are load-bearing |
| [[scroll-lock]] | Locking page scroll while an overlay is open — two variants and when each is right |
| [[infinite-marquee]] | A seamless marquee in any direction, reusable across the site from one interaction |
| [[dark-mode-toggle]] | A light/dark theme switch: variable modes, one toggled class, and persistence with no flash of the wrong theme |
| [[text-reveal-on-load]] | A heading revealing word by word or letter by letter — the six settings, in dependency order |
| [[per-row-scroll-stagger]] | Staggering a grid row by row as it scrolls in, correct at every breakpoint |
| [[pin-element-on-scroll]] | Pinning an element and shrinking it into the corner, picture-in-picture style |
| [[cms-grid-scatter]] | CMS images growing from the center and scattering into their grid cells |
| [[hover-border-fill]] | A border drawing itself on hover, via `@property` and a conic gradient |
| [[state-machine-toggle]] | Switching between discrete states with `jump to` and `pause` |
| [[canvas-image-sequence]] | ~150 stills swapped on a canvas as you scroll, plus the asset pipeline and its two traps |
| [[disintegration-on-scroll]] | An image scattering into canvas fragments — one attribute, and a real performance budget |
| [[draggable-cards]] | A grabbable, throwable photo stack, static or CMS |
| [[spotlight-and-prism]] | A cursor-tracked spotlight and an SVG channel-split hover, in three independent layers |
| [[glassy-button]] | A button that refracts the page behind it — **read its support warning first** |
| [[attribute-driven-stagger]] | A staggered reveal on any list from one attribute, no panel work |
| [[hover-button-patterns]] | Five hover-button effects, and the rule for when CSS beats GSAP |
| [[cms-cube-gallery]] | A scroll-driven 3D cube built from CMS fields — a build, not a snippet |
| [[swiss-poster-clip-path]] | Cutting letters out of a solid block with a normalized clip-path |

## Two of these are builds, not snippets

[[cms-cube-gallery]] and [[swiss-poster-clip-path]] are 50-minute set pieces. Both pages give the
**mental model and the load-bearing decisions**, deliberately not a click path — presenting them as
ten-step recipes would be dishonest. If a reader asks for "the steps", say so and give them the model,
which is what lets them adapt the build instead of reproducing it.

## Building a dialog? Read three of these

[[accessible-dialog]] is the structure, [[scroll-lock]] stops the page moving behind it, and
[[focus-trap]] is what makes it a real modal rather than a panel that merely looks like one. A
dialog answer that omits the third is incomplete.

## Three recipes have browser-support limits — surface them unprompted

Checked 2026-07-27. None of these are footnotes; each changes whether the effect is worth building.

| Recipe | Limit | What the visitor gets instead |
|---|---|---|
| [[glassy-button]] | SVG filters in `backdrop-filter` are **Chromium-only** | **Safari (so all of iOS) and Firefox: no refraction at all** — just the frosted base |
| [[cms-grid-scatter]] | `sibling-index()` not yet Baseline | The scatter doesn't happen; images still grow in |
| [[per-row-scroll-stagger]] | `sibling-index()` not yet Baseline | A working reveal, minus the stagger — shippable today |

Note that the two `sibling-index()` recipes degrade *very* differently. **Tell a reader which one they
are choosing.**

## Performance budget

[[disintegration-on-scroll]] and [[canvas-image-sequence]] are both genuinely expensive — a pixel walk
and ~150 images respectively. They are hero effects. If someone wants either across a CMS grid, say
plainly that the cost is per-instance.

**If the reader's task has no page here, say so** and point at the nearest
[`concepts/`](../concepts/index.md) page for the underlying mechanism. Do not assemble a
confident step-by-step from a concept page — a fabricated Webflow workflow is harder to debug
than an admitted gap.
