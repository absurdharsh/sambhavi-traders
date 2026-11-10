---
type: recipe
title: Build a scroll-driven 3D cube gallery from CMS data
summary: >
  This is a build, not a snippet — the value is four ideas rather than a click path: scroll distance
  becomes animation time via a tall section and a sticky window, each cube face's transform lives in the
  CMS as numeric fields, the interaction is scrubbed rather than triggered, and real 3D needs perspective
  on the parent plus preserve-3d on the cube.
level: advanced
tags: [gsap, cms, 3d, scroll, scrub, transforms, webflow]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: build-3d-cms-cubic-gallery-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-cubic-scrollable-image-gallery
assumes: [interactions-model, scroll-driven, split-text]
related: [scroll-driven, swiss-poster-clip-path, cms-grid-scatter]
---

## Scope this honestly before you start

**This is a 50-minute build, not a recipe you can follow in ten steps.** Do not present it as one.

What this page gives a reader is the **mental model** — the four ideas that make it work, and the two
CSS properties people always miss. The click-by-click belongs to the
[cloneable](https://webflow.com/made-in-webflow/website/gsap-cubic-scrollable-image-gallery) and the
[Codrops write-up](https://tympanus.net/codrops/2026/05/26/building-a-scroll-driven-3d-cube-gallery-in-webflow-with-gsap/),
which walks the 3D maths.

If someone asks for "the steps", say that plainly and give them the model — it is what lets them adapt
the build rather than reproduce it.

## Idea 1 — scroll distance becomes animation time

```
section  500vh              ← arbitrary; sets the overall speed. Taller = slower
  main-container  100vh     ← position: sticky, top: 0, max-width: none
    content wrapper  100%×100%
```

The sticky container pins as a fixed *window* while the rest of the section scrolls past behind it.
Standard scrub scaffold — see [[scroll-driven]]. If the animation feels too fast, change the section
height before touching easings.

## Idea 2 — the cube's geometry is data

**Each face's position lives in the CMS.** The `pictures` collection carries the obvious fields (image, a
short `text` for the split-text label, an integer `order` capped at 6 — a cube has six faces) plus eight
numeric ones that map directly onto CSS:

| Fields | Type | Maps to |
|---|---|---|
| `origin-x`, `origin-y` (labeled *Origin Left* / *Origin Top*) | decimal | `transform-origin` |
| `move-x`, `move-y`, `move-z` | **decimal** | `translateX/Y/Z` |
| `rotate-x`, `rotate-y`, `rotate-z` | **integer** | `rotateX/Y/Z` |

**Rotate fields are integers and move fields decimals for a reason:** positioning faces in 3D needs that
precision, and small errors read as visibly misaligned faces. It is not an arbitrary schema choice.

An embed **inside** the collection item binds each item's fields to its own transform, so Webflow renders
one rule per face:

```html
<!-- Embed inside the cube collection item -->
<style>
  .pictures_cube-item:nth-child({{wf:Order|Dynamo|DynamoGateway::dynamoNumberToText(-1)(Dynamo.order)}}) {
    transform-origin: calc({{wf:Origin Left|…}} * 100%) calc({{wf:Origin Top|…}} * 100%);
    transform: translateX(calc({{wf:Move X|…}} * 100%))
               translateY(calc({{wf:Move Y|…}} * 100%))
               translateZ(calc({{wf:Move Z|…}} * 100cqw))
               rotateX(calc({{wf:Rotate X|…}} * 90deg))
               rotateY(calc({{wf:Rotate Y|…}} * 90deg))
               rotateZ(calc({{wf:Rotate Z|…}} * 90deg));
  }
</style>
```

Note the multipliers: rotations are expressed as multiples of `90deg` and Z-depth in `100cqw`, so the CMS
values stay small readable numbers (`1`, `-1`, `0.5`) instead of raw degrees and pixels. Copy the exact
block from the cloneable — the bindings are long and easy to mistype.

**This is the same family as [[cms-grid-scatter]]:** CSS reads the CMS through an embed inside the item.
Recognize the pattern and the whole technique transfers.

## Idea 3 — this one is a scrub, not trigger actions

The cube should rotate and the images cross-fade **as** you scroll, forward *and* backward. That is
scrub-on-scroll. Trigger actions would fire once and play on their own clock, which is wrong here.

Two collection lists, two jobs — worth copying as an approach:

- one renders the background images stacked, sorted by `order` **descending** so item 1 sits on top at
  the start, for the cross-fade plus a split-text label ([[split-text]])
- a second uses the same data to build the cube faces

Splitting the work keeps each animation focused instead of one timeline doing everything.

## Idea 4 — real 3D needs two properties, and they are always the ones missing

A CSS "cube" looks **flat** unless:

| Property | On | Does |
|---|---|---|
| `perspective` | the cube's **parent** | gives the scene depth — how strong the foreshortening is |
| `transform-style: preserve-3d` | the **cube** | keeps children in their own 3D positions instead of flattening them onto the parent's plane |

If a reader says "my cube looks flat", this is the answer nine times out of ten. Check it before anything
else.

## Verify

Publish, then scroll through the whole section slowly in both directions. Faces should stay joined at the
edges — a visible seam or overlap means a `move-*` value is off by a fraction, which is exactly why those
fields are decimal. Then confirm the cross-fade order: item 1 on top at the start means the background
list is sorted **descending**.
