---
type: recipe
title: Reveal a heading word by word or letter by letter on page load
summary: >
  Enable Split Text on the action so one heading becomes many targets, set the type to from with
  opacity 0 and move Y 100%, turn the mask on to match the split unit so each piece is clipped, and
  choose offset time for a fixed rhythm or total time to fit a slot on the timeline.
level: intermediate
tags: [gsap, split-text, stagger, text, reveal, page-load, webflow]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: master-webflow-gsap-staggered-animations
    cloneable: https://webflow.com/made-in-webflow/website/mastering-gsap-split-text-animations
  - lesson: master-webflow-gsap-interactions
    cloneable: https://webflow.com/made-in-webflow/website/gsap-interactions-core-concepts
assumes: [interactions-model, stagger, split-text]
related: [split-text, stagger, page-load-flicker]
---

## The rule

Six settings, and the order matters because each one depends on the last:

| # | Setting | Value |
|---|---|---|
| 1 | Trigger | **Page load** |
| 2 | Target | **Custom selector** `h1` (headings usually carry no class) |
| 3 | Properties | **opacity** and **move Y** only |
| 4 | Type | **`from`** — `opacity: 0%`, `Y: 100%` |
| 5 | Split Text | on, split by **word** or **letter**, **mask** matching the split unit |
| 6 | Stagger | **offset time** for a fixed rhythm, **total time** to fit a slot |

Ease `expo out` reads nicely. Uncheck the `To` box when you set type to `from`.

## Why each of those

**Type `from`, not `to`.** You declare where the pieces *start* — invisible, pushed down — and they
land on their real designed state. With `to` you would have to author the final state twice.

**Split Text is what makes stagger work at all.** A lone `h1` is one target, and stagger across one
target does nothing. Splitting turns it into many. See [[split-text]].

**The mask is what separates "animated text" from a designed reveal.** It wraps each piece in its own
clipping container, so a piece stays hidden until it moves into view — paired with the upward `Y:
100%`, each word appears to slide out from behind a hidden edge. A bare fade looks accidental by
comparison.

**Target `h1` with a custom selector.** With correct SEO there is exactly one per page, so "all
matching elements" is safe. If a page has several headings of the same level, tag the one you're
animating with an attribute and target that — see [[target-filters]].

## The two-row composition

The workhorse hero pattern, and worth knowing because it covers most cases:

| Row | Type | Value | Reads as |
|---|---|---|---|
| One | `from` | `Y: 100%` | words rise into view |
| Two | `to` | `Y: -100%` | words slide out of sight |

Both split, both with an offset-time stagger around `0.1s`. Two actions, opposite directions, one
timeline.

## Choosing the split unit and the timing

| | Split by |
|---|---|
| Short display heading, mechanical texture | **letter** |
| Anything long, or must stay legible while animating | **word** |
| Multi-line where each line is a phrase | **line** (behaves like *word* when each word is on its own line) |

Letters multiply the target count, so **the same total time produces much tighter gaps**. With 13
letters and a total time of `0.5s`, each gap is `0.5 ÷ 12 ≈ 0.04s`. That is the relationship to hold:
intervals are always targets − 1.

Then shape the pacing with the **stagger's own ease** — a curve ending in `in` packs the gaps then
spreads them (feels like slowing down), `out` does the reverse. This is a *different* setting from the
action's ease, which controls how each individual piece travels. See [[stagger]].

## Two things that will bite you

**The `from` origin is free personality.** `start` is the classic left-to-right; `center` animates the
middle piece first and works outward; `edges` fires the outer pieces and moves in. It changes order
only, never spacing. Try it before adding complexity elsewhere.

**A page-load reveal is exactly when FOUC happens.** Your heading's designed state is visible for one
frame before GSAP applies `opacity: 0`. If you are using Interactions with GSAP, Webflow now handles
this automatically — if you are writing custom-code GSAP, you need the manual fix. See
[[page-load-flicker]].

## Tuning tip

Exaggerate the total time briefly while you choose a stagger ease, so you can actually see what the
curve does, then dial it back. Curves are almost indistinguishable at production speed and obvious at
3×.

## Verify

Publish and hard-reload a few times. Check that the ordering is what you intended — at production
speed a wrong `from` origin often just reads as "fast", so slow it down once to confirm. And confirm
each piece is genuinely clipped rather than fading: if you can see a word before it starts moving, the
mask is off or set to a different unit than the split.
