---
type: pitfall
title: A style cleanup deletes the classes your interactions toggle
summary: >
  A class that exists only to be added by a GSAP set action is applied to no element, so Webflow
  flags it as unused and any style cleanup deletes it — publishing itself does not strip it, which
  means this breaks work that was already correct, later, when someone tidies the project; keep an
  element wearing the class so it is genuinely used and never appears in a cleanup list.
level: beginner
tags: [gsap, set-action, classes, style-guide, silent-failure, maintenance, webflow]
status: verified
generated: 2026-07-27
verified_against: [author, lesson-prose]
sources:
  - lesson: sticky-scroll-video-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-set-action-pin-video-on-scroll
  - lesson: master-accessible-cms-sidebars-webflow
    cloneable: https://webflow.com/made-in-webflow/website/cms-jquery-detailed-info-sidebar
  - lesson: master-webflow-staggered-scroll-animations
    cloneable: https://webflow.com/made-in-webflow/website/gsap-staggered-animation-update
  - lesson: webflow-dark-mode-gsap
    cloneable: https://webflow.com/made-in-webflow/website/set-actions-in-interactions-with-gsap
  - lesson: growing-border-animations-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-growing-border-animation
assumes: [interactions-model]
related: [designer-vs-published, dark-mode-toggle, verified-identifiers]
---

## The symptom

**An animation that used to work stops working, and nothing about it changed.** The interaction
still fires, the class is still toggled, no console error — the page just looks identical to
before the animation ran.

The tell is the timeline: **it worked, then it didn't.** If the animation never worked at all,
this is probably not your bug — go to [[designer-vs-published]] instead.

## The cause

A class that exists purely as an animation state — `in-view`, `is-sticky`, `overflow-hidden`,
`dark-mode` — is **applied to no element**. You create it, style it, and hand it to a `set`
action. From Webflow's point of view it is an orphan: it has styles, but nothing wears it.

Webflow surfaces exactly that. In the Style selectors panel an unapplied class shows no "list"
icon and gains a **trash icon**, and the Style Manager's **Clean Up Styles** removes unused styles
in bulk.

So the class survives happily until someone tidies the project — and then it is gone, along with
the animation that depended on it. The person running the cleanup gets no signal that the class
was load-bearing: it looks like exactly the dead weight they are trying to remove.

## What publishing does *not* do — tested

**Publishing does not strip the class.** Verified empirically (2026-07-27, by the corpus author): a
class that was styled and then removed from the only element using it is **still present in the
published CSS**.

That reframes the risk:

| | |
|---|---|
| **Not** the hazard | Every publish silently degrading your project |
| **The** hazard | A maintenance action — *Clean Up Styles*, or a click on a trash icon — deleting the class from the project, after which the next publish has no styles for it |

Arguably the worse of the two, because it is **delayed and human-triggered**: it breaks work that
was already correct and shipping, it survives review (nothing changed in the interaction), and the
change that caused it looks like good housekeeping.

## The fix

**Keep one element wearing the class, somewhere that never ships visibly** — a style-guide page, or
a hidden `div`.

Understand what that actually buys you, because it is not a formality: the class becomes
**genuinely used**, so it loses the trash icon and never appears in a cleanup list at all. You are
not asking people to be careful — you are removing the class from the set of things they could
plausibly delete.

Every lesson in the corpus that uses a `set` action repeats this:

| Class | Lesson |
|---|---|
| `is-sticky` | `sticky-scroll-video-webflow` — keep a copy of the wrapper with the class |
| `overflow-hidden` | `master-accessible-cms-sidebars-webflow` |
| `in-view` | `master-webflow-staggered-scroll-animations` — "register it in your style guide" |
| `dark-mode`, `theme-transition` | `webflow-dark-mode-gsap` |
| `on-hover` | `growing-border-animations-webflow` — a hidden `div` with the class |
| `cc-animate-image`, `cc-animate-image-wrapper` | `master-webflow-gsap-interactions` — a dummy div in the style guide |

**Do it at creation time, not at debug time.** The moment you create a class for a `set` action, put
it on a style-guide element in the same breath. Diagnosing it later is miserable, because nothing
about the symptom points at the cause and the change that broke it may have been made weeks ago by
someone else.

## The rule to carry

**Any class whose only job is to be toggled by an interaction needs a home.** If you are writing a
`set` → class → add / remove / toggle action, ask where that class lives when the animation isn't
running. If the answer is "nowhere", it is one cleanup away from being deleted.

## The other side: before running Clean Up Styles

Worth saying to anyone maintaining a project that has interactions: **an unused class is not
necessarily a dead one.** Before a bulk cleanup, check whether any flagged class is named in a
`set` action. Preview a class's styles in the Style selectors panel rather than trusting the
"unused" label.

## Verify

If a toggled class does nothing live, open the Style selectors panel and search for it. Missing
entirely → it was deleted; recreate it, style it, and this time put it on a style-guide element.
Present but showing a trash icon → you have found the bug before it happened.
