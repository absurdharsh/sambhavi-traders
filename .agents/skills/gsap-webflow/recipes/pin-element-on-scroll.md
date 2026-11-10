---
type: recipe
title: Pin an element and shrink it into the corner on scroll
summary: >
  A picture-in-picture effect is a discrete state change, not a scrub — trigger actions with
  leave → play and enter back → reverse, a combo class carrying all three sticky properties, and
  one number variable multiplied into three calc-based transforms so a single animate-variable
  action drives the move and the scale together.
level: advanced
tags: [gsap, scroll, sticky, variables, video, transforms, webflow]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: sticky-scroll-video-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-set-action-pin-video-on-scroll
assumes: [interactions-model, timeline-control, scroll-driven]
related: [sticky-translate-layout-shift, scroll-driven, unused-class-cleanup]
---

## The rule

**This is trigger actions, not a scrub.** The element should *change state once* when it crosses a
threshold, not have its shape tied continuously to scroll position. Reach for a scrub here and it
rubber-bands with every wheel twitch.

| Setting | Value |
|---|---|
| Interaction | Scroll, on the wrapper, **trigger actions** |
| Events | **`leave` → play** · **`enter back` → reverse** |
| End threshold | `top` / `top` |
| Action 1 | `set` → toggle the `is-sticky` class, at `0s` |
| Action 2 | **animate variable** `transform-factor` `0 → 1` (~`0.3s`, sine in-out) |

## Build it

**1. The wrapper.** Max width, `margin-inline: auto`, `aspect-ratio: 16/9`, and
**`transform-origin: top right`** so it shrinks *toward* the corner rather than toward its center.

**2. Sticky is three properties, so make it a class.** A combo class `is-sticky` carrying all
three together:

```
position: sticky;
top: 0;
z-index: 100;
```

`position: sticky` alone does nothing useful — it needs the offset to stick against and the
`z-index` to sit above the content it scrolls over. Because they must change together, they belong
in one class flipped by one `set` action.

**Keep an element wearing `is-sticky` in your style guide**, or a style cleanup deletes it — see
[[unused-class-cleanup]].

**3. One variable, three transforms.** Animating three separate variables in step is fragile.
Create a single **number** variable (`transform-factor`, default `0`) and multiply each transform by
it, in a **custom `transform` property** — the style panel's transform controls cannot take
`calc()`:

```css
transform:
  translateX((50vw - 50% - 2rem) * factor)
  translateY((100vh - 40% - 2rem) * factor)
  scale(calc(1 - 0.6 * factor));
```

Read the horizontal formula as a sentence: push to the center of the viewport (`50vw`), pull back
half the element's own width (`- 50%`), then account for the page padding (`- 2rem`). The scale
trick is the neat part — `1 - 0.6 * factor` eases from `1` at factor `0` to `0.4` at factor `1`,
so one `0 → 1` variable drives position *and* size.

**4. Compensate the layout.** A `translateY` alongside `position: sticky` throws the final layout
too low. Bottom margin on the wrapper equal to its scaled height, cancelled by a negative top
margin on the element below:

```css
/* on the wrapper */
margin-bottom: calc(100vh - 768px * 0.5625 - 2rem);
```

`768px` is the desktop wrapper width, `0.5625` the 16:9 ratio. **This must be pixel-based**, and the
reasons are subtle enough to get wrong — see [[sticky-translate-layout-shift]].

**5. Guard it.** In **conditional playback**: no animation from tablet down, and for
`prefers-reduced-motion`. A shrinking, traveling element is exactly the motion that setting exists
for, and the effect makes no sense on a phone anyway.

## Why a variable instead of three animate actions

You *could* animate `move X`, `move Y` and `scale` as three properties in one action. The variable
approach wins for two reasons:

- The positions are `calc()` expressions referencing viewport units and the element's own size.
  Interaction panels animate numbers, not formulas — the variable lets CSS keep the maths.
- One value means the three transforms cannot drift out of sync, at any duration or easing.

This is the general pattern whenever an effect needs several CSS properties to move as one: animate
a single unit-less driver and let CSS derive the rest. [[cms-grid-scatter]] uses the same idea in
the opposite direction (`1 → 0`).

## Verify

Published site. Scroll down past the threshold — the element should stick, then shrink into the
corner once. Scroll **back up** and it must return: if it doesn't, the `enter back → reverse` event
is missing. Then watch the element *below* it for a jump or a gap, which means the two margins
don't match.
