---
type: pitfall
title: It works in the Designer but not live — or the reverse
summary: >
  Custom code, non-standard CSS properties, first-paint flicker, scroll lock and keyboard focus
  all behave differently in the Designer and in plain preview than on the published site, and the
  Designer additionally shows you your animations' initial states, so half-hidden content while
  building is expected rather than broken.
level: beginner
tags: [webflow, designer, preview, publishing, testing, custom-code, debugging]
status: verified
generated: 2026-07-27
verified_against: lesson-prose
sources:
  - lesson: fix-webflow-gsap-flicker
    cloneable: https://webflow.com/made-in-webflow/website/gsap-remove-flickering
  - lesson: master-webflow-staggered-scroll-animations
    cloneable: https://webflow.com/made-in-webflow/website/gsap-staggered-animation-update
  - lesson: growing-border-animations-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-growing-border-animation
  - lesson: webflow-dark-mode-gsap
    cloneable: https://webflow.com/made-in-webflow/website/set-actions-in-interactions-with-gsap
  - lesson: accessible-gsap-popup-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-set-action-prevent-scrolling
assumes: []
related: [unused-class-cleanup, page-load-flicker]
---

## The symptom

Two directions, and they get confused for each other:

- **Works while building, dead on the live site.** Usually something that simply doesn't run before
  you publish — see the table below.
- **Looks broken while building, fine once published.** Almost always expected behavior, not a
  bug.

**If it worked live and then stopped**, this is the wrong page: go to [[unused-class-cleanup]].

## What does not run before you publish

| Thing | Where it works |
|---|---|
| Custom code in Site/Page head or *Before `</body>`* | **Published only** — does not reliably run in preview |
| Non-standard CSS custom properties | Published, or preview with custom code enabled — *not* plain preview |
| First-paint flicker (FOUC) | Published only — it is a first-paint artifact and will not reproduce in the Designer |
| Scroll lock | Published — it depends on the real document, which the canvas is not |
| Keyboard focus and focus traps | Published — needs the real DOM and real focus order |
| Anything reading `localStorage` or `prefers-color-scheme` | Published |

**The practical rule: if your feature involves an embed, the head, or the browser itself, the
Designer cannot tell you whether it works.** Publish to a staging page and test there. Every
lesson in the corpus that touches custom code ends with *"publish and test on the live link"* —
that is not filler.

For flicker specifically, publish and **hard-reload several times**: it is a timing artifact and
will not appear on every load.

## Why your content is hidden while you build

Any animation with an initial state that hides its target — `opacity: 0`, `translateY(20%)` —
hides it **in the Designer too**. The canvas shows the CSS you authored, and the CSS you authored
says invisible. GSAP is not running to reveal it.

That is expected. Do not "fix" it by removing the initial state.

**The fix is an override scoped to the Designer**, and it works because of one Webflow behavior:
Webflow adds **`w-mod-js`** to the `html` element only when its runtime JavaScript is running.
Write a rule that forces your animated items visible **when that class is absent** — a condition
true inside the Designer and false on the published site — and you get a normal editing
experience with no effect on the live animation.

Note this is the mirror image of the FOUC fix in [[page-load-flicker]], which scopes a *hiding*
rule to `.w-mod-js` so content stays visible when JavaScript is off. Same signal, opposite
direction. Both are correct; make sure you know which one you are writing.

## The diagnostic order

When something doesn't work, check in this sequence — it goes cheapest-first and catches the
common cases early:

1. **Are you testing on the published site?** If not, stop and publish.
2. **Is `runs on` set to the pages that need the interaction?** It defaults to the page you built it
   on, so a component copied into a navbar silently does nothing elsewhere.
3. **Does the target actually resolve?** A relationship filter that doesn't match the DOM finds
   nothing and reports nothing — see [[target-filters]].
4. **Does the class the `set` action toggles still exist?** Only worth checking if this used to
   work — see [[unused-class-cleanup]].
5. **Only then** look at the animation itself.

Steps 1–4 are all silent failures. The animation is rarely the problem.

## Verify

Keep a staging publish in the loop while you build motion, and check the live link after each
meaningful change rather than at the end. Debugging one new silent failure is easy; debugging
four at once is not.
