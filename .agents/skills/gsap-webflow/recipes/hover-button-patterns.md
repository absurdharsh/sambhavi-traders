---
type: recipe
title: Build animated hover buttons — and know when not to use GSAP
summary: >
  Five button effects share three reusable moves: an overflow-hidden wrapper masking a moving child,
  animating width from 0 to auto, and pushing an incoming overlay to -101%; the real lesson is the tool
  boundary — CSS for the element's own properties, GSAP the moment you must reach into a child.
level: intermediate
tags: [gsap, hover, buttons, micro-interactions, css, performance, webflow]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: webflow-gsap-hover-micro-interactions
    cloneable: https://webflow.com/made-in-webflow/website/gsap-microinteractions
assumes: [interactions-model, timeline-control]
related: [button-vs-link, subpixel-slivers, timeline-control]
---

## The rule that decides everything

**Webflow's hover state styles only the element itself — never its children or siblings.**

| The effect changes… | Use |
|---|---|
| Only the hovered element's own properties — background, color, `box-shadow`, `transform` | **CSS hover state + transitions.** Ships no JavaScript |
| Anything *inside* it — an icon, an overlay, a text wrapper | **GSAP** |

That is the whole decision, and it is not free either way: **each interaction adds JavaScript Webflow
injects into the page.** Two or three hover interactions won't sink a site, but reaching for GSAP by
default will.

## The structure

Every button here is a **link block**, never Webflow's native Button element — a link block nests
children, and these effects animate those children. If the control performs an *action* rather than
navigating, use a custom element with the tag `button` instead. Both nest; the native Button does
neither correctly. See [[button-vs-link]].

Reusable pieces that show up again and again:

- an **`overflow: hidden` wrapper** to mask a moving element
- SVG icons with **`fill: currentColor`** so they inherit the button's color
- **`position: relative`** on the button so an absolute overlay can align to it

## The five patterns

**1. Bouncy expanding button.** Wrap the text in a div set to `overflow: hidden` and animate that
wrapper's **`width` from `0` to `auto`** — its natural size. The clip keeps the text hidden until the
button expands. Elastic-out easing for the bounce. Two hover events with control **`toggle
play/reverse`**.

**2. Fill from the left.** Button `position: relative`, `overflow: hidden`. A full-size overlay
(`position: absolute`, all offsets) parked at **`translateX(-101%)`**. Action 1: overlay X to `0%`.
Action 2: text color to an off-white for contrast. Same duration, back-out easing.

**3. Fill from the bottom.** Same idea with a circle: `aspect-ratio: 1`, `border-radius: 100%`,
`translateY(100%)`, centered by the button's own flex alignment. One action moving it to `translateY(0)`.

**4. Glow — no interaction at all.** CSS transitions on `background`, `color` and `box-shadow`, then set
the target values on the button's **hover state** in the style panel. This is the one to notice: it looks
as polished as the others and ships zero JavaScript.

**5. Light + shadow — the hybrid, and the tell-all.** The button's own scale and `box-shadow` go on the
CSS hover state. But the arrow *inside* it cannot, because the hover state can't reach a child — so
rotating that arrow (e.g. `-45°`) needs a GSAP interaction. **Match the easing and duration across both**
so the combined effect reads as one motion.

Pattern 5 is worth walking a reader through, because it is the boundary made concrete: one effect, split
across two tools, for a reason you can state.

## Why `-101%` and not `-100%`

At exactly `-100%` a border or sub-pixel rounding can leave a **hairline of the overlay** visible along
the button's edge at rest. The extra 1% pushes it fully out of sight, and it is invisible in the
animation because the overlay covers that distance in the same tween.

Never rely on an exact boundary value to hide something. See [[subpixel-slivers]].

## Why `toggle play/reverse` rather than two interactions

One timeline, two hover events: enter plays forward, leave plays backward **from the playhead's current
position**. So an interrupted hover reverses from that exact frame instead of snapping. Classic
Interactions needed two mirrored animations for this. See [[timeline-control]].

## Decompose before you build

A hover that looks sophisticated is always several single-purpose actions timed to converge — scale,
color, icon-out, icon-in. Do not try to express a multi-part effect as one clever action. If you cannot
name what each action does in three words, split it further.

## Verify

Hover in and **out again halfway** — the motion should reverse from where it got to, not jump. Check the
resting state at 90% and 110% browser zoom for an overlay hairline. Then Tab to the button: if the effect
only exists on hover, keyboard users get nothing — add `focus` handling or accept that the effect is
decorative.
