---
type: recipe
title: Stagger a grid row by row as it scrolls into view
summary: >
  GSAP's own stagger cannot do this, so invert the responsibility — a set action adds an in-view
  class to each card as it enters the viewport, CSS transitions do the animation, and a per-card
  transition-delay computed from sibling-index() and a column-count variable produces a stagger
  that resets on every row and re-derives itself at every breakpoint.
level: advanced
tags: [gsap, stagger, scroll, css, responsive, variable-modes, cms, webflow]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: master-webflow-staggered-scroll-animations
    cloneable: https://webflow.com/made-in-webflow/website/gsap-staggered-animation-update
assumes: [interactions-model, stagger, scroll-driven]
related: [stagger, cms-grid-scatter, unused-class-cleanup, designer-vs-published]
---

## Why GSAP's stagger can't do it

Try it first and you will waste an hour, because **neither target works**:

- Target the **trigger element** → each card animates alone. One element has no siblings to stagger
  against.
- Target the shared **attribute** → the first time it fires, GSAP staggers the *entire* collection at
  once, including cards far below the fold.

Neither gives a stagger tied to what is actually entering the viewport. See [[stagger]].

## The rule

**Invert the responsibility.** GSAP does the minimum — it marks cards as they arrive. CSS does the
animation and the timing.

| Layer | Job |
|---|---|
| GSAP | one `set` action adding an `in-view` class to the trigger element |
| CSS | the transition between initial state and `in-view` state |
| CSS | a per-card `transition-delay`, computed from its position in its row |

The whole effect is one `set` action, one utility class, a bit of CSS, and one variable.

## Build it

**1. Initial state.** On the card, `opacity: 0` and `translateY(20%)`. Tag every card
`data-animate-item`.

**2. The scroll interaction.** Target `data-animate-item`, **trigger actions**, `enter → play`,
start threshold ~90% of viewport height. One `set` action adding `in-view` to the **trigger
element**.

**3. The `in-view` class.** `opacity: 1`, all move transforms `0`, plus transitions on `transform`
and `opacity` (800ms, ease-out expo). **Register it in your style guide** or a cleanup will delete it
— see [[unused-class-cleanup]].

**4. The column-count variable.** A number variable `number-of-columns`, with a **variable mode per
breakpoint** — e.g. 3 desktop / 3 tablet / 2 landscape / 1 portrait.

**5. The stagger engine.** An embed on the page (or in the site head). Only the config block at the
top is meant to be edited:

```html
<!-- Embed on the page (or in Site head) -->
<style>
  [data-animate-item].in-view {
    /* ===== CONFIG — tune these three ===== */

    /* 0 = pure linear (equal gaps) · 1 = pure exponential (gaps shrink) · 0.5 = hybrid */
    --animation-mode: 1;

    /* Linear: the fixed step between cards. Expo: the maximum delay ceiling. */
    --step-or-max-delay: 0.5s;

    /* Deceleration curvature, only active when --animation-mode > 0. Useful range 0.3–0.6. */
    --expo-base: 0.5;

    /* ===== MATHS — leave alone ===== */

    /* 0-based column index within the row */
    --index: mod(sibling-index() - 1, var(--_cards---number-of-columns));

    --linear-part: var(--index);
    --expo-part: calc(1 - pow(var(--expo-base), var(--index)));

    /* linear interpolation between the two pacings */
    --final-curve: calc((var(--linear-part) * (1 - var(--animation-mode))) + (var(--expo-part) * var(--animation-mode)));

    transition-delay: calc(var(--final-curve) * var(--step-or-max-delay));
  }
</style>
```

**`--_cards---number-of-columns` is Webflow's generated name** for the `number-of-columns` variable
in the *cards* collection. Three dashes in the middle, two at the start — read it out of the
Designer's *Copy CSS*, never derive it. See [[verified-identifiers]].

**6. Stay able to edit in the Designer.** The initial state hides the cards while you build. Add an
override forcing them visible when Webflow's `w-mod-js` class is **absent** from `html` — a condition
true only inside the Designer. See [[designer-vs-published]].

## Why this is responsive for free

`mod(sibling-index() - 1, columns)` gives each card its index *within its row*, so the delay resets
at every row start. Because the formula reads the variable rather than a hard-coded number, changing
breakpoint reflows the grid **and** re-derives every delay. No per-breakpoint rebuild.

That is the reason to store the column count as a variable with modes instead of writing `3` into the
CSS.

## Browser support — check this before shipping (2026-07-27)

This recipe depends on **`sibling-index()`**, which is **not Baseline**. Chromium has stable support;
Safari is recent and partial; Firefox was still in development, with Baseline expected mid-to-late
2026. `mod()` and `pow()` are better supported but belong to the same modern-CSS-maths family.

**How it degrades:** if `sibling-index()` doesn't resolve, the `transition-delay` calculation fails
and falls back to no delay — so **every card in view animates at once**. You still get a working
scroll reveal, just without the stagger. That is a genuinely acceptable fallback, which is what makes
this technique shippable today.

Say this to a reader rather than letting them discover it in Safari. The lesson predates the support
question and does not raise it.

## Works for static content too

Nothing here needs the CMS. `sibling-index()` counts DOM siblings, so the same attribute, class and
CSS work on a hand-built grid.

## Verify

Publish, then scroll slowly and watch **one row at a time**: each row should stagger and the next
should start over, not continue the count. Then resize across breakpoints and confirm the reset still
lands on the row boundary — if it doesn't, the variable mode for that breakpoint is wrong. Finally
check in a non-Chromium browser to see which behavior your visitors actually get.
