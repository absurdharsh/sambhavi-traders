---
type: pitfall
title: A thin line or sliver appears at the edge of a moving element
summary: >
  Fractional device pixels mean an element parked at exactly -100% can leave a hairline of itself
  visible, and two perfectly aligned elements moving together can show an anti-aliasing seam
  between them; overshoot to -101% in the first case and pull a -1px margin in the second.
level: intermediate
tags: [css, subpixel, anti-aliasing, transforms, hover, polish, webflow]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: webflow-gsap-hover-micro-interactions
    cloneable: https://webflow.com/made-in-webflow/website/gsap-microinteractions
  - lesson: swiss-style-gsap-animation-webflow
    cloneable: https://webflow.com/made-in-webflow/website/the-jam-swiss-style
assumes: []
related: [stacking-and-dom-order]
---

## The symptom

Two versions of the same class of bug, and both look like a rendering glitch rather than something
you did:

- A hover overlay that should be fully off-screen leaves a **1px stripe** of color along the
  button's edge at rest.
- Two elements that are flush against each other and animate together show a **thin outline**
  along the seam while they move.

## The cause

**Device pixels are not CSS pixels, and the ratio is rarely a whole number.** A position computed
as `-100%` can land on a fractional device pixel, and the browser resolves that by painting a
partial-coverage row — a hairline. Anti-aliasing along a shared edge does the same thing from the
other direction: two adjacent surfaces each get a half-covered pixel, and the blend reads as a
line.

Borders and non-integer element widths both make it more likely. It is also more visible on
non-retina displays and at certain zoom levels, which is why it can vanish on your machine and
appear in a client's screenshot.

## The two fixes

**Overshoot instead of landing exactly.** Park an off-screen overlay at **`-101%`**, not `-100%`.
The extra 1% guarantees it is fully outside the element before it slides in, and it is invisible
in the animation because the overlay travels that distance in the same tween.

```
translateX(-101%)  →  translateX(0)
```

Do the same for any element hidden by translating it out of an `overflow: hidden` wrapper — the
overshoot costs nothing.

**Pull the seam closed with a negative margin.** Where two aligned elements move together, a
**`-1px` margin** on one of them removes the shared edge, and with it the anti-aliasing artifact.
The Swiss-poster build applies it to the section letters for exactly this.

## The rule to carry

**Never rely on an exact boundary value to hide something.** `-100%`, `0`, and an exact edge match
are all values a sub-pixel rounding error can sit on. Overshoot by a percent, or overlap by a
pixel. Both are free, and neither is visible in motion.

## Don't confuse it with a stacking problem

A visible sliver of an element that should be *behind* another one is a different bug — that is
stacking order, not sub-pixel rounding. Tell them apart by whether the visible part is a
**hairline at an edge** (this page) or a **visible region of the element** ([[stacking-and-dom-order]]).

## Verify

Check at rest, not mid-animation, and on a non-retina display or at 90% / 110% browser zoom —
those are the conditions that expose it. A sliver that only appears at some zoom levels is
confirmation, not a red herring.
