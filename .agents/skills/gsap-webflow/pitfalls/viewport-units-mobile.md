---
type: pitfall
title: A full-height overlay is cut off or jumps on mobile
summary: >
  vh is measured against the initial viewport and does not track the mobile browser's UI
  expanding and collapsing, so a 100vh fixed overlay is taller than the visible area and its
  bottom is unreachable; use 100dvh, which always matches the real visible height.
level: beginner
tags: [css, viewport, dvh, vh, mobile, overlay, dialog, webflow]
status: verified
generated: 2026-07-27
verified_against: lesson-prose
sources:
  - lesson: accessible-gsap-popup-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-set-action-prevent-scrolling
  - lesson: master-webflow-gsap-interactions
    cloneable: https://webflow.com/made-in-webflow/website/gsap-interactions-core-concepts
assumes: []
related: [accessible-dialog, scroll-lock]
---

## The symptom

On a phone: a full-screen dialog's close button sits below the fold and cannot be reached. Or the
overlay shifts by 60-odd pixels as you scroll, because the browser's address bar collapsed.

It looks perfect on desktop and in the Designer.

## The cause

**`vh` is based on the *initial* viewport.** It is measured once, against the viewport as it was
when the page loaded, with the browser UI expanded. It does not update when the address bar
collapses or reappears.

So `100vh` on a mobile browser is *taller than the visible area* for most of the session. On a
`position: fixed` overlay, that difference is not scrollable away — the bottom of your dialog is
simply off-screen.

**Scroll lock makes it worse.** Changing the body's `overflow` can shift viewport calculations,
which is exactly when a `fixed` wrapper sized in `vh` breaks. That is why this pitfall shows up
in dialog builds specifically, and why [[scroll-lock]] carries a pointer here.

## The fix

**`height: 100dvh`** — dynamic viewport height. It tracks the real visible area continuously, so
it always matches what the user can see.

```css
/* On the fixed overlay wrapper */
height: 100dvh;
```

The horizontal equivalent is **`dvw`**, and it is the right choice for a full-bleed element whose
width must match the real viewport — the hero image in `master-webflow-gsap-interactions` uses
`100dvw` for exactly this reason, with `max-width: none` so Webflow doesn't override it.

## When `vh` is still right

Do not blanket-replace it. `vh` is correct — and often preferable — when you *want* a stable
measurement that doesn't move:

| Use | Unit |
|---|---|
| A fixed overlay, dialog, or anything with a control near the bottom edge | **`dvh`** |
| Scroll-scaffold section heights (`300vh`, `500vh`) | **`vh`** — a value that jitters with the browser UI would make a scrub animation shudder |
| Typography sized to a poster-like composition | **`vh`** — stability is the point |

The distinction is whether a change mid-scroll would be *corrective* or *disruptive*. For a
fixed overlay it is corrective. For a scroll timeline it is disruptive.

## Verify

Test on a real phone, not a resized desktop window — desktop browsers do not have collapsing
UI, so they cannot reproduce this. Open the dialog, confirm the close control is reachable, then
scroll the page behind it (if it isn't locked) and confirm nothing shifts.
