---
type: pitfall
title: Elements flash before a page-load animation starts
summary: >
  The flash is a one-frame timing gap where the browser paints elements in their
  final CSS state before GSAP applies the animation's initial values; fix it by
  hiding the wrapper from the page head, scoped to .w-mod-js so content stays
  visible when JavaScript is off. Webflow now prevents this automatically for
  Interactions with GSAP, so the manual fix is only needed for custom-code GSAP.
level: intermediate
tags: [gsap, fouc, flicker, page-load, webflow, ix3]
status: verified
generated: 2026-07-26
verified_against: cloneable
sources:
  - lesson: fix-webflow-gsap-flicker
    cloneable: https://webflow.com/made-in-webflow/website/gsap-remove-flickering
assumes: [interactions-model]
related: [timeline-control]
---

## Does this still apply to you?

**Read this first.** Webflow now **prevents FOUC automatically** when you use
Interactions with GSAP (IX3) — no action needed on your part.

| Your setup | Do you need the manual fix? |
|---|---|
| Webflow Interactions with GSAP (IX3) | **No** — handled automatically |
| GSAP written by hand in custom code | **Yes** |
| A project built before IX3 and not migrated | **Yes** |

The technique below remains correct and is worth understanding, because the *reason*
the flicker happens explains a whole class of animation bugs. But do not add it to a
new IX3 project as a reflex.

## Why the flash happens

It is a **timing gap, not a bug in your animation**. For one frame the browser
paints your elements in their final CSS state — the state you designed — before GSAP
runs and applies the animation's initial values. The eye catches that jump.

So a heading that should fade in from `opacity: 0` appears fully visible for a
frame, then snaps to invisible, then fades in.

## The fix

**Hide the wrapper, not each element.** If you hide individual elements you have to
enumerate every one of them, and anything you miss still flashes. Hiding the
container means nothing inside can flash, and GSAP switches it back on at the exact
moment it takes control.

**Put the CSS in the page's `<head>`.** Not in the Designer — that would hide your
work while you build. And not via GSAP itself, which just moves the flicker onto the
wrapper.

**Target a custom attribute, not a class list.** A single rule matching
`remove-flicker` catches every element carrying it, combo classes and all, instead of
a growing list of class selectors.

**Scope the rule to `.w-mod-js`.** This is the part people miss, and it is the
difference between a fix and a bug. Webflow adds `w-mod-js` to the `html` element
only when JavaScript is running. If JavaScript is off, GSAP never executes — so
without the scope your content stays hidden **forever**. With it, the hiding rule
simply never applies and the content shows normally.

## The failure mode to remember

An unscoped hide rule is worse than the flicker it fixes: a cosmetic one-frame
blemish becomes permanently invisible content for anyone without JavaScript. Always
scope to `.w-mod-js`.

## Verify

Publish, then hard-reload the live page several times — the flicker is a first-paint
artifact and will not reproduce reliably in the Designer. Then disable JavaScript in
the browser and reload: **the content must still be visible.**

## Source

Lesson: `fix-webflow-gsap-flicker` ·
[cloneable](https://webflow.com/made-in-webflow/website/gsap-remove-flickering) ·
[Webflow: automatic handling of FOUC](https://webflow.com/updates/automatic-handling-of-fouc)
