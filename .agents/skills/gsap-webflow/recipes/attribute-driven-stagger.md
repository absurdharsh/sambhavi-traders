---
type: recipe
title: Add a staggered reveal to any list with one attribute
summary: >
  Drop fc-gsap-staggered="list" on the div wrapping your items — or straight onto a Collection List —
  and the included script builds a staggered slide-and-fade with sensible defaults; every other option
  is a bare attribute you add only when you want tighter control over timing, motion or order.
level: beginner
tags: [gsap, stagger, attributes, no-code, cms, reveal, webflow]
status: verified
generated: 2026-07-27
verified_against: [author, cloneable]
sources:
  - lesson: no-code-gsap-staggered-animations-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-staggered-animation
assumes: [stagger]
related: [stagger, per-row-scroll-stagger, verified-identifiers]
---

## The rule

**One attribute is the whole build:**

```
fc-gsap-staggered="list"
```

on the div block that wraps all your items, or directly on a Collection List. Publish, and you have a
working staggered slide-and-fade on defaults. Everything else is optional.

Works identically on static layouts, CMS collections and custom grids. **Multiple lists on one page
need no indexes** to tell them apart — drop the same attribute on each.

## The options

**Only the marker is prefixed. Every option is a bare attribute name** — this component does not follow
the `fc-<component>-<option>` convention the rest of the corpus uses, so do not "complete" them.

| Attribute | Controls | Value |
|---|---|---|
| `threshold` | visibility fraction at which it fires | `0.4` (40%) |
| `each` | fixed gap in seconds between items | `0.2` |
| `amount` | total time split across the row | `0.5` |
| `duration` | how long one item animates | `0.5` |
| `offset-y` | how far each item slides up from | `3rem` |
| `from` | where the wave begins | `start` |
| `ease` | how each item moves | `power3.out` |
| `staggered-ease` | how the delays are distributed | `power1.out` |

`from` accepts `start` · `end` · `center` · `edges` · `random` · or a **zero-based index** (`1` starts
from the second item).

## The three things worth knowing

**`each` and `amount` conflict by design.** `each` is a fixed gap between items — add items and the
sequence gets longer. `amount` is a total budget split across the row — add items and each gap shrinks.
**If `amount` is set, `each` is ignored, even when it holds a valid value.** This is the most common
misconfiguration; set one, not both.

**`ease` and `staggered-ease` do different jobs.** `ease` shapes how an individual item travels.
`staggered-ease` shapes the *spacing between* items — a curve like `power1.out` starts with bigger gaps
and clusters them tighter toward the end. Same distinction as the two easings in the panel, see
[[stagger]].

**`from` is the cheapest way to change the personality.** It only changes *order*, never spacing —
`edges` fires the outer items first and moves inward, `center` works outward from the middle. Try it
before touching anything else.

## Write `offset-y`, never `offsetY`

HTML lower-cases attribute names, so a camelCase spelling arrives as `offsety` and **silently never
matches** — you get the default `3rem` and no error.

The lesson prose has the camelCase form; it is wrong. See [[verified-identifiers]], where this is
recorded as one of the corpus's known identifier errors.

## When to use this instead of the panel

| Situation | Use |
|---|---|
| A list or grid, reveal on scroll, no strong art direction | **This** — one attribute |
| You need per-row stagger that resets on each row | [[per-row-scroll-stagger]] — this script staggers across the whole row set |
| The reveal is part of a larger choreographed timeline | The interactions panel, so it shares the timeline |
| Splitting text into words or letters | The panel — Split Text is a panel feature |

This is the right default for "make the cards appear nicely". Reach for the panel when the reveal has to
coordinate with something else.

## Verify

Publish — the script ships in the cloneable and runs on the live site. Scroll the list into view and
confirm the wave direction matches your `from` value. If timing changes do nothing, check whether you
have set **both** `each` and `amount`: `amount` wins silently.
