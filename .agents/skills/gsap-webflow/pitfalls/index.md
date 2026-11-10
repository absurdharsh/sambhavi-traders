---
type: reference
title: Pitfalls — why it's broken
summary: >
  Pages answering "why does this flash, jump, or silently fail", each opening with the symptom
  rather than the fix; most of these fail silently, which is why the diagnostic order matters more
  than any individual page.
level: beginner
tags: [meta, index, pitfalls]
status: verified
generated: 2026-07-27
verified_against: lesson-prose
sources: []
assumes: []
related: [designer-vs-published, unused-class-cleanup]
---
## Start here if something silently doesn't work

Most failures in this corpus produce **no error at all**. First split on one question, because it
sends you to different places:

**Did it ever work?**

- **Never worked** → [[designer-vs-published]] first, then in order: `runs on` (does the interaction
  even load on this page — see [[timeline-control]]), then [[target-filters]] (does the target
  resolve against this DOM).
- **Worked, then stopped** → [[unused-class-cleanup]]. A deleted animation class is the classic
  cause of a regression nobody changed anything to cause.

The animation itself is rarely the problem. Check the silent failures first.

## By symptom

| Page | The symptom |
|---|---|
| [[unused-class-cleanup]] | An animation that used to work stops, and nothing about it changed |
| [[designer-vs-published]] | Works while building and dead live — or looks broken while building and is fine |
| [[page-load-flicker]] | Elements flash in their final state for one frame before a page-load animation starts |
| [[stacking-and-dom-order]] | Reordering in the navigator doesn't change what's on top |
| [[viewport-units-mobile]] | A full-height overlay is cut off or jumps on mobile |
| [[overflow-hidden-on-body]] | The page stops scrolling — or a wide element still spills sideways |
| [[button-vs-link]] | Nowhere to nest an icon, or a control announced as a link instead of a button |
| [[sticky-translate-layout-shift]] | A sticky element settles too low, with a gap where it used to be |
| [[subpixel-slivers]] | A hairline or thin stripe at the edge of a moving element |

## Check `status:` before answering from here

Pitfall pages are the ones most likely to go stale, because the usual reason a pitfall disappears
is that Webflow automated the fix. Several open with a *"does this still apply to you?"* table —
honor it. `superseded` means the technique still works but a better path now exists: say both.

## Two pages that resolve apparent contradictions

The corpus disagrees with itself in two places, and both are merged here with the rule for
choosing rather than left as two authoritative-looking answers:

- [[overflow-hidden-on-body]] — the marquee lesson calls it "a hard no"; the dialog lessons do it
  deliberately. The property does one thing, disable page scroll: in one case that's an unwanted
  side effect of trying to crop, in the other it's the entire goal.
- [[button-vs-link]] — one lesson says use a link block, another says use a real `button`. Both
  reject Webflow's native Button, for different reasons.
