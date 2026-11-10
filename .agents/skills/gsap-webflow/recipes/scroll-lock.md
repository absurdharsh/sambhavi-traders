---
type: recipe
title: Lock page scroll while an overlay is open
summary: >
  Scroll locking in Webflow is a state change, not an animation, so it belongs in a
  GSAP set action at the start of the open timeline — either flipping body overflow
  to hidden via a custom selector, or toggling an overflow-hidden class when the
  close animation needs to finish first.
level: intermediate
tags: [gsap, scroll-lock, overlay, dialog, accessibility, webflow]
status: verified
generated: 2026-07-26
verified_against: cloneable
sources:
  - lesson: accessible-gsap-popup-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-set-action-prevent-scrolling
  - lesson: master-accessible-cms-sidebars-webflow
    cloneable: https://webflow.com/made-in-webflow/website/cms-jquery-detailed-info-sidebar
assumes: [interactions-model, timeline-control]
related: [accessible-dialog, focus-trap, viewport-units-mobile]
---

## The rule

Locking scroll means setting the page's `overflow` to `hidden`, and releasing it
means putting it back. That is **instantaneous**, so it is a GSAP **`set`** action —
never an `animate` action. Place it at `0s` of the open timeline.

## Variant A — set `body` overflow directly

Add a `set` action at `0s` targeting `body` through a **custom selector**, setting
`overflow` to `hidden`.

Release is free: if the overlay closes by **reversing** the same timeline, GSAP
restores the original value automatically. You write nothing for the unlock.

Use this when the overlay closes via timeline reverse — the common case for a modal.

## Variant B — toggle an `overflow-hidden` class

Add a `set` action at the start of the open animation that applies an
`overflow-hidden` class, and a matching action that removes it at the **end** of the
close animation (`start: 0.8s`, matching the close duration).

Use this when the close is its own animation rather than a reverse, and the page
must stay locked until the panel has finished sliding out. Unlocking at `0s` of the
close would let the page jump behind a still-visible panel.

## Choosing between them

| Situation | Variant |
|---|---|
| Overlay closes by reversing the open timeline | **A** — reverse restores it for free |
| Close is a separate animation | **B** — so the lock outlives the exit |
| Lock must survive until an exit finishes | **B** |
| Simplest possible modal | **A** |

## The mobile gotcha

Changing the body's `overflow` can shift viewport calculations and break a
`position: fixed` wrapper. Give the wrapper **`height: 100dvh`**, not `100vh`.

`vh` is based on the initial viewport and does not track the mobile browser UI
expanding and collapsing; `dvh` — dynamic viewport height — always matches the real
visible area. See [[viewport-units-mobile]].

## Test it on the published site

Scroll lock depends on the real document, so the Designer canvas is not a valid
test. Publish, open on the live link, confirm the page does not scroll behind the
overlay, then close and confirm scrolling returns **after** the exit completes.

## Related

Scroll lock alone does not make an overlay accessible. A dialog also needs
`role="dialog"`, `aria-modal="true"`, `aria-labelledby`, real `button` elements, and
a focus trap — see [[accessible-dialog]] and [[focus-trap]].
