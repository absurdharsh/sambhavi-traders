---
type: recipe
title: Cut letters out of a solid block with a normalized clip-path
summary: >
  This is a build, not a snippet — the transferable technique is cutting a shape out of a solid field
  with a CSS clip-path built as the negative around the letters in Figma, then normalizing the exported
  path's absolute pixel coordinates into the 0–1 range that objectBoundingBox expects.
level: advanced
tags: [gsap, clip-path, svg, figma, typography, page-load, webflow]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: swiss-style-gsap-animation-webflow
    cloneable: https://webflow.com/made-in-webflow/website/the-jam-swiss-style
assumes: [interactions-model, stagger]
related: [cms-cube-gallery, text-reveal-on-load, subpixel-slivers]
---

## Scope this honestly before you start

**This is a poster build, not a component you drop in.** Half of it is layout craft specific to one
composition; presenting it as a step list would be dishonest.

What transfers is **one technique** — a normalized clip-path cut from a solid field — plus three
production details that apply to any similar set piece. That is what this page covers. The composition
itself belongs to the [cloneable](https://webflow.com/made-in-webflow/website/the-jam-swiss-style).

## The technique: cut the negative, not the letters

**A clip-path only shows what is *inside* its shape.** So to reveal letters cut out of a red block, the
shape you need is not the letters — it is the **negative around them**.

In Figma: type the letters, convert text to vector (`Cmd/Ctrl+E`), delete any anchor points you don't want
(the dot on a J, for instance), place a rectangle snugly behind them, select both, and run **Object →
Boolean → Subtract**. The letters are now carved out of the rectangle. Export as SVG.

That inversion is the whole idea, and it generalizes to any "shape knocked out of a solid" effect.

## Normalizing the path — the step that makes it reusable

**An exported SVG path speaks in absolute pixels. A CSS clip-path set to `objectBoundingBox` speaks in a
`0–1` range**, so it can scale with whatever element it clips. Bridging them is "normalizing": multiply
every coordinate by `1/width` and `1/height`.

The SVG Path Editor does it for you — paste the path data, note the SVG's width and height, and run a
scale with `1/width` and `1/height`. For a 15 × 11 export that is `1/15 ≈ 0.067` and `1/11 ≈ 0.091`.

```html
<!-- Embed at the very top of the page, position: absolute -->
<svg>
  <clipPath id="jm" clipPathUnits="objectBoundingBox">
    <path d="M.9996 1H0V0H.9996V1ZM.1385.1075V.715C…"/>
  </clipPath>
</svg>
```

**Every coordinate sits between 0 and 1 — that is what normalizing bought you.** Your `d` will differ; it
is specific to your letters and their original dimensions. The wrapper is exactly this.

Then apply it with a custom property on the element to be clipped: `clip-path: url(#jm)`.

**Placement rule:** the embed goes at the very **top of the page**, because a definition must come before
anything that references it. Same rule as the SVG filters in [[spotlight-and-prism]].

## Three production details that generalize

**Size type in `vh`, not `rem`.** Tying type to viewport height means the whole composition scales down
together on shorter screens, so it never overflows vertically. That is the instinct behind designing to a
fixed poster frame — and it is why this build stays intact where a `rem`-based one would break.

**Two `overflow: hidden` guards, each with a job.** One on the content wrapper, so an oversized letter
group can bleed off the edge; one on the inner letters wrapper, so a letter animating *inside* the group
stays hidden until it slides. Masking is almost always two nested clips, not one.

**A hairline appears where two aligned elements move together.** Sub-pixel anti-aliasing along the shared
edge — fixed with a `-1px` margin. See [[subpixel-slivers]].

## The flicker fix, done the manual way

This build hides the content wrapper with head CSS and restores it as the interaction's **first action**:

```html
<!-- Page Settings → Custom Code → Head -->
<style>
  .section_content-wrapper {
    opacity: 0
  }
</style>
```

Because it is plain CSS in the head it applies before first paint; the interaction's first action
(opacity to `100%`, duration `0`) hands control to GSAP the moment it is ready.

**Currency note (2026-07-27):** Webflow now prevents FOUC automatically for Interactions with GSAP, so a
new IX3 project does not need this. Understand it anyway — the *reason* it works explains a whole class of
animation bugs, and it is still required for custom-code GSAP. See [[page-load-flicker]].

Note also that this variant is **unscoped**, unlike the `.w-mod-js` version on that page: it hides the
wrapper whether or not JavaScript runs. Here the interaction always restores it, but if you copy this
pattern into a build where the reveal might not fire, scope it — otherwise the content is invisible
forever with JavaScript off.

## The animation is deliberately plain

Both interactions fire on **page load**. The text reveal targets four text blocks by a **custom
attribute** (`data-content-role="text"`, not a class) and animates opacity `0 → 100%` with X `-50% → 0`,
staggered `0.1s`. The letter sequence animates a **single property** — `move X` — on three targets with
different offsets and timings.

That is worth pointing out: the set piece looks elaborate and the motion is one property on a few
elements. Complexity lives in the layout and the clip-path, not the timeline. See [[stagger]].

## Verify

Publish. Resize to a short viewport and confirm the composition scales rather than overflowing — if text
overflows, something is sized in `rem`. Check the seam between the aligned letter elements at rest for a
hairline. Then hard-reload a few times to confirm no flash before the reveal.
