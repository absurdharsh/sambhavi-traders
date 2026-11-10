---
type: pitfall
title: A sticky element ends up in the wrong place after moving or scaling it
summary: >
  A translateY on an element that is also position sticky throws the final layout too low,
  because the transform moves the painted element without changing the space it reserves;
  compensate with a bottom margin equal to the scaled height and cancel the gap with a negative
  top margin on the element below.
level: advanced
tags: [css, sticky, transform, layout, scroll, margins, webflow]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: sticky-scroll-video-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-set-action-pin-video-on-scroll
assumes: [scroll-driven]
related: [scroll-driven, stacking-and-dom-order]
---

## The symptom

Your pin-into-corner effect animates correctly, but the layout around it is wrong: the element
settles too low, and there is a large empty gap where it used to be.

## The cause

**A transform moves what is painted; it does not change the space the element reserves.** Combine
a `translateY` with `position: sticky` and the two disagree — sticky is resolving against the
element's original box while the transform has visually moved it somewhere else.

The result is a final layout offset by roughly the distance you translated.

## The fix

Two margins that cancel each other:

1. A **bottom margin on the wrapper** equal to its *scaled* height:
   `calc(100vh - 768px * 0.5625 - 2rem)` — where `768px` is the desktop wrapper width and
   `0.5625` is the 16:9 height ratio.
2. A **negative top margin on the element below**, to close the gap that opens.

### Why those are pixel-based `calc()` and not percentages

Two reasons, both easy to get wrong:

- **Margin percentages reference the parent's *width*, not its height.** A vertical margin written
  as a percentage resolves against the wrong axis.
- **The margin resolves *before* the scale.** So you must express the height the element will
  occupy *after* scaling, which is why the ratio (`0.5625`) is written into the formula rather
  than relying on the rendered box.

Get either wrong and the compensation is close enough to look almost right, which is worse than
obviously broken.

## Before reaching for this

**Check that a scrub is not what you actually wanted.** This whole compensation exists because the
effect is a *discrete state change* driven by trigger actions — the element pins, then changes
shape once. If you wanted the shape tied continuously to scroll position, you want a scrub and a
tall-section-plus-sticky-window scaffold instead, which has no such layout problem. See
[[scroll-driven]].

## Related properties that must travel with it

`position: sticky` alone does nothing useful — it needs a `top` offset and a `z-index` high enough
to sit above the content it scrolls over. Bundle all three into a combo class and toggle that
class with a `set` action, rather than setting them individually.

And because the class exists only to be toggled, keep an element wearing it in your style guide —
see [[unused-class-cleanup]].

## Guard it

This effect makes no sense on a small screen and is exactly the kind of motion that causes
discomfort. In **conditional playback**, disable it from tablet down and for
`prefers-reduced-motion`.

## Verify

Published site, scrolling **down and back up**. Watch the element *below* the sticky one: if it
jumps or a gap appears as the effect completes, the two margins don't match. Then check it at a
narrower desktop width — the pixel formula is tied to the wrapper width, so a different breakpoint
needs its own value.
