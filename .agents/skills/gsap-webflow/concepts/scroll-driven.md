---
type: concept
title: Scroll-driven animation — scrub versus trigger actions
summary: >
  Every scroll animation is one of two kinds: a scrub, where the playhead is tied
  continuously to scroll position, or trigger actions, where crossing a threshold fires an
  animation that then plays on its own clock; choosing wrong is the most common cause of a
  scroll effect that feels off, and the scaffold for a scrub is always a tall section with
  a sticky full-viewport window inside it.
level: intermediate
tags: [gsap, scroll, scrolltrigger, scrub, sticky, thresholds, webflow]
status: verified
generated: 2026-07-27
verified_against: lesson-prose
sources:
  - lesson: sticky-scroll-video-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-set-action-pin-video-on-scroll
  - lesson: build-3d-cms-cubic-gallery-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-cubic-scrollable-image-gallery
  - lesson: apple-style-image-sequence-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-video-animation-on-scroll
  - lesson: master-webflow-staggered-scroll-animations
    cloneable: https://webflow.com/made-in-webflow/website/gsap-staggered-animation-update
assumes: [interactions-model, timeline-control]
related: [timeline-control, stagger, split-text, interactions-model]
---

## The rule

Two kinds, and you must choose deliberately:

| | **Scrub on scroll** | **Trigger actions** |
|---|---|---|
| Playhead | Tied continuously to scroll position | Fires once at a threshold, then plays on its own |
| Scrolling back | Runs the animation backward | Nothing, unless you wire an *enter back* event |
| Right for | Rotation, cross-fades, image sequences, anything the reader should *drive* | Reveals, state changes, pin-into-corner, class toggles |

**The test:** if the reader should be able to stop halfway and see a half-finished state,
it's a scrub. If it's a state change that happens *at* a point, it's trigger actions.

Trigger-action events map to threshold crossings — **`enter → play`**, **`leave → play`**,
**`enter back → reverse`** — each with its own threshold, expressed as which edge of the
element meets which edge of the viewport (`top/top`, and so on).

## How and why

**A scrub needs scroll distance, and distance is the speed control.** The scaffold is
always the same two elements:

1. A **deliberately tall section** — 300vh, 500vh — which is an arbitrary number that sets
   the overall pace. Taller is slower.
2. Inside it, a **full-viewport container** at `height: 100vh`, `position: sticky`,
   `top: 0`. It pins in place and acts as a fixed *window* while the rest of the section
   scrolls past behind it.

The scrub then maps how far you have scrolled through the section onto the animation's
progress. If a scroll effect feels too fast, change the section height before you touch
easings.

**Trigger actions for a discrete state change.** A video that sticks then shrinks into the
corner is not a scrub — its shape should not track scroll position, it should *change state
once*. So: trigger actions, `leave → play` when the wrapper starts leaving the viewport,
`enter back → reverse` to undo, end threshold `top/top`. Reaching for a scrub here produces
an element that rubber-bands with every scroll wheel twitch.

**Sticky is three properties, so make it a class.** `position: sticky` alone does nothing
useful — it needs a `top` offset and a high `z-index` so it stays above the content it
scrolls over. Because they must change together, bundle them into a combo class and flip it
with a `set` action. Keep a copy of an element carrying that class in your style guide, or a style
cleanup will delete it — see [[unused-class-cleanup]].

**A `translateY` alongside `position: sticky` throws the final layout off.** The compensation
is a bottom margin on the wrapper equal to its scaled height, cancelled by a negative top
margin on the element below. Margin percentages reference the parent's *width* and the margin
resolves *before* the scale, which is why these are written as pixel-based `calc()`
expressions rather than percentages.

**One variable can drive several transforms.** Animating three separate variables in step is
fragile. Instead create one **number** variable that goes `0 → 1`, write each transform as a
`calc()` multiplied by it, and express a shrink as `scale(calc(1 - 0.6 * factor))` so it eases
from `1` to `0.4`. A single *animate variable* action then drives all three. Note that the
style panel's transform controls cannot take `calc()` — these go in a custom `transform`
property.

## Custom-code ScrollTrigger

For effects the panel cannot express — an image sequence painted onto a `<canvas>`, for
instance — the same two ideas carry over to GSAP's own **ScrollTrigger**: a tall section, a
sticky container, and `start` / `end` values in ScrollTrigger's own syntax (`top top`,
`bottom bottom`). GSAP and every plugin including ScrollTrigger are **free and hosted by
Webflow** — enable them in site settings, never load them from an external CDN.

A `<canvas>` conveys nothing to a screen reader: mark it `aria-hidden="true"` and put the
message it carries in a visually-hidden paragraph beside it.

## Guard it

Scroll effects are the ones most worth disabling. Use **conditional playback** to switch a
heavy effect off from tablet down and for **`prefers-reduced-motion`** — scroll-linked
movement, parallax and scaling are exactly what motion-sensitive readers turn that setting on
to avoid. See [[timeline-control]].

## Verify

Scroll behavior depends on the real document height, so the Designer canvas is not a valid
test. Publish, then scroll the live link slowly *and* fast, and scroll back up — a scrub that
looks right going down and breaks going back up is the classic sign that a `set` action needs
an *enter back* counterpart.

## Related

Staggering cards as they enter the viewport is a scroll problem GSAP's own stagger cannot
solve — see [[stagger]] for the class-plus-CSS-delay pattern that replaces it.
