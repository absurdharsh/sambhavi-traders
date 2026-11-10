---
type: concept
title: Stagger — spreading one animation across many targets
summary: >
  Stagger spreads a single animation across multiple targets, so it does nothing at all
  with one target; offset time sets a fixed gap between each target's start while total
  time is one budget divided across the intervals, and the stagger's own ease shapes the
  distribution of delays — a different job from the action's ease.
level: intermediate
tags: [gsap, stagger, timing, easing, split-text, webflow]
status: verified
generated: 2026-07-27
# The fc-gsap-staggered attribute names come from the author (the lesson carries
# no attribute table); everything else is derived from verified lesson prose.
verified_against: [author, lesson-prose]
sources:
  - lesson: master-webflow-gsap-staggered-animations
    cloneable: https://webflow.com/made-in-webflow/website/mastering-gsap-split-text-animations
  - lesson: no-code-gsap-staggered-animations-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-staggered-animation
  - lesson: master-webflow-staggered-scroll-animations
    cloneable: https://webflow.com/made-in-webflow/website/gsap-staggered-animation-update
  - lesson: master-webflow-interaction-settings
    cloneable: https://webflow.com/made-in-webflow/website/gsap-interaction-settings
assumes: [interactions-model]
related: [split-text, scroll-driven, interactions-model]
---

## The rule

**Stagger needs several targets or it does nothing.** With a single element there is
nothing to spread, so turning it on looks identical to leaving it off. If a lone `h1`
isn't staggering, the fix is [[split-text]] — break it into words or letters so each piece
becomes a target.

Two ways to time it, and they answer opposite questions:

| | Meaning | As the count grows |
|---|---|---|
| **Offset time** | A fixed delay between each target's start | The sequence gets **longer** |
| **Total time** | One budget for the whole stagger, divided across the intervals | Each gap gets **smaller** |

The number of intervals is always **targets − 1**. So 13 letters with a total time of
`0.5s` gives gaps of `0.5 ÷ 12 ≈ 0.04s` — tight and smooth.

**Use total time** when the reveal must fit a known slot on the timeline. **Use offset
time** when the rhythm between pieces matters more than the overall length.

## How and why

**The `from` origin changes order only.** `start`, `center`, `end`, `edges`, `random`,
`element` decide *where the sequence begins* — `center` animates the middle piece first
then works outward, `edges` fires the outer pieces first and moves inward. The spacing
maths between pieces is identical in every case. It costs nothing and completely changes
the personality of the animation, so it is the first thing to try when a reveal feels
generic.

**There are two easings and they are not the same thing.** This trips up almost everyone,
because they share a name and sit two panels apart.

| Ease | Shapes |
|---|---|
| The **action's** ease | How each individual piece travels from its start value to its end value |
| The **stagger's** ease | The *distribution of the delays* between pieces |

Leave the stagger ease at none and the gaps are even. A curve ending in **`in`** packs the
gaps tight then spreads them — the reveal feels like it is slowing down. One ending in
**`out`** spreads then tightens — it feels like it is speeding up. Exotic curves (back,
bounce, elastic) produce deliberately chaotic spacing: fun, rarely right for a heading.

Tuning tip: exaggerate the total time while you choose a stagger ease so you can actually
see what the curve does, then dial it back.

## When GSAP's stagger is the wrong tool

**Per-row stagger on scroll cannot be done with it**, and this is worth knowing before you
waste an hour. Neither target works:

- Target the **trigger element** → each card animates alone; a single element has no
  siblings to stagger against.
- Target the shared **attribute** → the first time it fires, GSAP staggers the *entire*
  collection at once, including cards far below the fold.

The pattern that does work inverts the responsibility: GSAP does the minimum — a `set`
action adding an `in-view` class to each card as it enters the viewport — and the animation
lives in **CSS transitions** between the initial state and the `in-view` state. The stagger
is then a per-card **`transition-delay`**, computed from the card's position within its row
using the `sibling-index()` CSS function and a Webflow variable for the column count. Give
that variable a **mode per breakpoint** (e.g. 3 / 3 / 2 / 1) and the delays re-derive
correctly whenever the grid reflows — responsive with no rebuild.

Two consequences worth carrying: the initial state hides the cards **in the Designer too**,
so add an override that forces them visible when Webflow's JS class is absent on `html`
(true only inside the Designer); and this approach works identically for static elements,
not just CMS lists.

## The attribute-driven script variant

A no-code cloneable exposes stagger through custom attributes instead of the panel, and its
vocabulary is GSAP's own rather than Webflow's. **Only the marker is prefixed; every option is a
bare attribute name.**

| Attribute | Panel equivalent | Value |
|---|---|---|
| **`fc-gsap-staggered`** | — | **`list`** — the one required attribute |
| `threshold` | — visibility fraction at which it fires | `0.4` |
| `each` | offset time — fixed gap in seconds | `0.2` |
| `amount` | total time — split across the row | `0.5` |
| `duration` | how long one item's animation lasts | `0.5` |
| `offset-y` | initial vertical offset each item slides up from | `3rem` |
| `from` | the stagger's from origin (also accepts a **zero-based index**) | `start` |
| `ease` | the **action's** ease | `power3.out` |
| `staggered-ease` | the **stagger's** ease — distributes the delays | `power1.out` |

**`each` and `amount` conflict by design: if `amount` is set, `each` is ignored** even when it holds
a valid value. This is the single most common misconfiguration, and it means at most one of those two
rows applies to any given build.

`fc-gsap-staggered="list"` goes on the div wrapping your items, or straight onto a Collection List;
that alone gives the default animation. Multiple lists on one page need no indexes to tell them
apart, and the same attribute works on static layouts, CMS collections and custom grids alike.

**Two names to get right**, because both fail silently:

- **`offset-y`, not `offsetY`.** HTML attribute names are lower-cased by the parser, so a camelCase
  spelling becomes `offsety` and never matches. The lesson prose has this wrong; the cloneable is
  authoritative.
- **`staggered-ease`** is a separate attribute from `ease`. They are the two easings from the section
  above — one shapes each item's motion, one shapes the distribution of delays.

## Interaction repeat, not stagger, loops a sequence

A staggered reveal that should cycle forever loops at the **interaction** level so all its
actions stay in sync. Staggering and looping are independent settings — see
[[timeline-control]].
