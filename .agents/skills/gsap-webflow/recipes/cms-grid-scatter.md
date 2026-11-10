---
type: recipe
title: Grow CMS images from the center and scatter them into a grid
summary: >
  An embed inside the collection item lets CSS read each item's own CMS order, compute how far it
  sits from the grid center, and multiply that shift by a move-factor variable — so animating one
  variable from 1 to 0 releases every card from the center into its real cell, with the scatter's
  start time derived arithmetically from the grow stagger.
level: advanced
tags: [gsap, cms, animate-variable, css, grid, stagger, variable-modes, webflow]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: how-to-build-cms-scatter-animations-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-grow-and-scatter
assumes: [interactions-model, stagger, target-filters]
related: [pin-element-on-scroll, per-row-scroll-stagger, stagger]
---

## The rule

**CSS can read the CMS** — put an embed *inside* the collection item, so Webflow renders one copy of
the rule per card and the `{{wf:Order …}}` binding stamps each item's own position into it.

Each card then computes its own distance from the grid center and multiplies it by a shared
**`move-factor`** variable:

| `move-factor` | Result |
|---|---|
| `1` | every shift is at full strength — all cards pulled to the center |
| `0` | every shift is `0` — cards rest in their real grid cells |

So the entire scatter is one **animate variable** action taking `move-factor` from `1` to `0`.

## The CSS

```html
<!-- Embed inside the collection item -->
<style>
  [data-animate-item]:nth-child({{wf:Order|Dynamo|DynamoGateway::dynamoNumberToText(-1)(Dynamo.order)}}) {
    --i: sibling-index();
    --x: mod(var(--i) - 1, var(--columns));
    --y: round(up, var(--i) / var(--columns), 1);

    --shift-x: calc(var(--move-factor) * (var(--columns) / 2 - (var(--x) + 0.5)) * 100%);
    --shift-y: calc(var(--move-factor) * (var(--rows) / 2 - var(--y) + 0.5) * 100%);

    transform: translate(var(--shift-x), var(--shift-y));
  }
</style>
```

Read it as: work out my column and row from my index, work out how far that is from the center of the
grid, scale that by `move-factor`, and translate by it.

## Build it

**1. CMS + grid.** A collection with an `image` field and an integer `order` field; sort the
collection list by it. Build the grid with your column count and set row heights to **100% ÷ rows** in
grid edit mode. Tag items `data-animate-item` and images `data-animate-image`.

**2. Three variables.** Number variables `move-factor` (default `1`), `columns`, and `rows`, matching
your grid. The CSS-side names must match: `--move-factor`, `--columns`, `--rows`.

**3. The embed.** Inside the collection item, with the CSS above.

**4. The interaction.** Page load, two actions:

| # | Action | Settings |
|---|---|---|
| 1 | **animate** `scale` **from `0`** on `data-animate-image` | `0.8s`, power2-out, **stagger** offset-time `0.2s` |
| 2 | **animate variable** `move-factor` **to `0`** | `0.8s`, power2-out, start = see below |

**5. The start time is arithmetic, not taste.** The scatter must begin exactly when the last image
finishes scaling:

```
start = (itemCount − 1) × stagger + duration
```

With 12 images: `11 × 0.2 + 0.8 = 3s`. Change the item count or the stagger and you must recompute it
— this is the number people forget when they add a card.

## Why this works, and where the idea generalizes

**The grid maths is written in terms of `columns` and `rows`**, never in absolute positions, which is
what makes it grid-agnostic: change the grid and you update two variables.

That also makes it **responsive for free**. Add **variable modes** for tablet / landscape / portrait
with different values (e.g. tablet 4×3, portrait 3×4), update the grid and row heights to match, and
the centering maths re-reads them per breakpoint. No per-breakpoint rebuild.

**One variable driving many computed properties** is the same pattern as
[[pin-element-on-scroll]], run in the opposite direction (`1 → 0` rather than `0 → 1`). Worth
recognizing as a pattern: when an effect needs several derived CSS values to move as one, animate a
single unit-less driver and let CSS do the derivation. The interactions panel animates numbers; CSS
does maths.

## Browser support — check this before shipping (2026-07-27)

This recipe depends on **`sibling-index()`**, plus `mod()` and `round()`. `sibling-index()` is **not
Baseline**: Chromium has stable support, Safari's is recent and partial, Firefox was still in
development, with Baseline expected mid-to-late 2026.

**How it degrades — and this one is not graceful.** If `sibling-index()` doesn't resolve, `--shift-x`
and `--shift-y` are invalid, the `translate` does nothing, and **the scatter simply doesn't happen**.
The images still grow in (that action is ordinary GSAP), so you get a plain staggered scale-in rather
than a broken page — but the effect that justified the build is absent.

Compare [[per-row-scroll-stagger]], which depends on the same function but degrades to a working
reveal without the stagger. Same dependency, very different fallback — worth telling a reader which
one they are choosing.

The lesson predates the support question and does not raise it.

## Verify

Publish. On load, the images should grow from nothing **at the center**, then release outward into
their cells with no pause and no jump — a visible gap between the grow and the scatter means the start
time is wrong for your item count. Then resize across breakpoints and confirm the cards still converge
on the center rather than an off-center point, which means a variable mode doesn't match its grid.
Finally check a non-Chromium browser to see whether the scatter runs at all.
