---
type: pitfall
title: Reordering in the navigator doesn't change what's on top
summary: >
  An absolutely positioned element paints above a static sibling no matter where it sits in the
  DOM, so reordering does nothing; give the shared parent position: relative so both children
  keep z-index: auto, and then DOM order decides — the element that comes later renders on top.
level: intermediate
tags: [css, stacking, z-index, position, dom-order, overlay, webflow]
status: verified
generated: 2026-07-27
verified_against: lesson-prose
sources:
  - lesson: accessible-gsap-popup-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-set-action-prevent-scrolling
  - lesson: webflow-spotlight-prism-effects
    cloneable: https://webflow.com/made-in-webflow/website/spotlight-animation-svg-filter-gsap
  - lesson: swiss-style-gsap-animation-webflow
    cloneable: https://webflow.com/made-in-webflow/website/the-jam-swiss-style
assumes: []
related: [accessible-dialog, subpixel-slivers]
---

## The symptom

You drag an element in the navigator to put it behind another one. Nothing changes. You start
adding `z-index` values, they behave unpredictably, and you end up at `z-index: 9999`.

## The cause

**A positioned element paints above a `static` one regardless of DOM order.** If your image is
`position: absolute` and the container holding your content is `static`, the image wins — and no
amount of reordering will change that, because you are not comparing like with like.

`z-index` only orders elements that are *in the same stacking context and comparably positioned*.
Reaching for a big number papers over the real problem and creates a new one the next time
something needs to sit above it.

## The fix

**Make the elements comparable, then let DOM order decide.**

Give the shared parent `position: relative`. Now both children sit in the same stacking context
with the default `z-index: auto` — and at that point **the element that comes later in the DOM
renders on top**. Reordering in the navigator starts working, and you need no `z-index` at all.

Three builds in the corpus rest on this:

- **Spotlight hero** — the absolutely positioned image covered the heading. Setting the main
  container to `position: relative` puts the content back on top with no `z-index`.
- **Swiss poster** — `section letters` is set to `position: relative` so it stacks above the
  absolutely positioned letter-A wrapper beneath it.
- **Dialog overlay** — see below, because this one is counterintuitive.

## The counterintuitive one: an overlay that must sit behind

For click-outside-to-close you need an overlay that fills the dialog wrapper but sits **behind**
the panel, so clicks inside the panel never reach it.

Since both have `z-index: auto` and stacking follows DOM order, **you drag the overlay *above* the
panel in the navigator** to push it *behind* visually. Earlier in the DOM means painted lower.

That reads backwards the first time. State it explicitly when explaining it to someone, because
"move it up to send it back" is exactly the kind of instruction people assume they misread.

## The rule to carry

**Before touching `z-index`, check whether the two elements are even comparable.** Nine times out
of ten the fix is `position: relative` on the parent plus a drag in the navigator — and that fix
keeps working as the page grows, which a `z-index` arms race does not.

Reserve explicit `z-index` for genuine layers: a sticky element that must ride above the content
it scrolls over (`z-index: 100` on the sticky video's combo class), a fixed dialog wrapper above
the whole page (~`3000`).

## Verify

Remove the `z-index` you added and confirm the layering still works. If it does, it was never the
mechanism — and leaving it in will mislead whoever debugs this next.
