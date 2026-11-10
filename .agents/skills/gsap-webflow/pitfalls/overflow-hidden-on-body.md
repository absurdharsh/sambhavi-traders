---
type: pitfall
title: overflow hidden on the body — right for scroll lock, wrong for clipping
summary: >
  Overflow hidden on the body does exactly one thing — it disables scrolling for the whole page —
  so using it to crop something that spills sideways breaks the site to fix one section, while
  using it as an overlay's scroll lock is correct because there disabling scroll is the goal; clip
  at the container instead, and make sure every close path releases the lock.
level: intermediate
tags: [css, overflow, scroll-lock, marquee, clipping, webflow]
status: verified
generated: 2026-07-27
verified_against: [author, lesson-prose]
sources:
  - lesson: how-to-build-infinite-looping-marquees-webflow
    cloneable: https://webflow.com/made-in-webflow/website/060-gsap-infinite-marquee
  - lesson: accessible-gsap-popup-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-set-action-prevent-scrolling
  - lesson: master-accessible-cms-sidebars-webflow
    cloneable: https://webflow.com/made-in-webflow/website/cms-jquery-detailed-info-sidebar
assumes: []
related: [scroll-lock, infinite-marquee, focus-trap]
---

## Start here: the property does exactly one thing

**`overflow: hidden` on the body disables scrolling for the whole page.** That is the entire
behavior. Every question about it reduces to one thing:

> **Is killing page scroll what you wanted?**

- Trying to **crop** something that spills sideways? Then no — you have just broken scrolling for
  the whole site to solve a problem in one section.
- Building a **scroll lock** for an open overlay? Then yes — that is precisely the goal.

This is why the corpus looks like it contradicts itself. The marquee lesson calls body overflow
**"a hard no"**; the dialog lessons set it **deliberately**. Same declaration, opposite intent:
in one case disabling scroll is an unwanted side effect, in the other it is the whole feature.

## Wrong: as a clipping fix

**What you were trying to solve.** Something wide — a marquee strip, an oversized decorative
element — spills sideways and produces a horizontal scrollbar.

**Why body overflow is the wrong tool.** Because of the one thing it does: **the page stops
scrolling.** You wanted to hide overflow in one section and you have disabled scrolling
everywhere, permanently. That alone settles it.

Secondarily, it doesn't even crop where you want: clipping at the body means the cut happens at
the *viewport* edge, so the strip runs edge to edge instead of stopping at your content width.

**The fix.** Clip at the element that owns the problem. Put `overflow: hidden` on the **main
container**, via a **combo class** (`is-marquee`) rather than the base class, so you don't clip
every container on the site. The visible marquee stays bounded to your content max-width, which
usually looks better anyway. See [[infinite-marquee]].

The same reasoning covers the other clipping cases in the corpus: an `overflow: hidden` wrapper to
mask a sliding element, `overflow: hidden` on a section so a spotlight overlay can't spill. Always
the nearest sensible ancestor, never the body.

## Right: as a temporary scroll lock

**Locking page scroll while an overlay is open** is exactly the case where the body is the correct
target — it *is* a document-level state, and disabling scroll is the intended effect, not a
side effect.

Because it is temporary, the thing that keeps it correct is that **something takes it off again**:

| Mechanism | Release |
|---|---|
| A `set` action on `body` via a custom selector | Automatic — GSAP restores the original value on timeline **reverse** |
| An `overflow-hidden` **class** toggled by `set` | Explicit — a matching action removes it at the *end* of the close (`start: 0.8s`) |

See [[scroll-lock]] for choosing between the two.

## The failure mode: a lock that gets stuck

This is the one to actually worry about, because the symptom is severe and the cause is remote
from it: **the page never scrolls again**, and there is no error.

It happens when the dialog is dismissed by a path that bypasses the release. The concrete case,
from [[focus-trap]]: an Escape handler that hides the panel directly instead of clicking the close
control. The panel goes away, the closing interaction never runs, and `overflow-hidden` stays on
the body forever.

**The rule:** every path that closes an overlay must route through the same interaction that
releases the lock. Escape, click-outside, the close button — one exit, not three.

## Provenance note

The marquee lesson states the "hard no" without giving the reason. **The reason above — that it
disables page scrolling — was confirmed directly by the corpus author (2026-07-27)**, which is why
this page is `verified_against: [author, lesson-prose]`. It is authoritative rather than inferred,
even though it appears in no lesson.

## Verify

Open the overlay and confirm the page behind does not scroll. Close it **by every available
route** — button, outside click, Escape — and after each one confirm scrolling has returned. Then
check the body's computed `overflow` in devtools; it must be back to its original value, not
`hidden`.
