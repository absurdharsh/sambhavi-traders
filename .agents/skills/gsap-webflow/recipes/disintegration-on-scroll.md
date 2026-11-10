---
type: recipe
title: Disintegrate an image into fragments on scroll
summary: >
  One attribute turns an image into a cloud of canvas fragments that scatter and fade as you scroll —
  the script snapshots the image with html2canvas, rebuilds it as fragments and animates them with
  ScrollTrigger; it needs a non-static position on the component and overflow hidden around it, and it
  is expensive enough that it belongs on a hero, not a grid.
level: intermediate
tags: [gsap, scrolltrigger, canvas, disintegration, attributes, performance, accessibility]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: gsap-disintegration-effect-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-disintegration-effect-on-scroll
assumes: [scroll-driven]
related: [canvas-image-sequence, scroll-driven, designer-vs-published]
---

## The rule

Mark the element with **`fc-disintegration="component"`**, put your image inside it at `width: 100%`,
and enable the **ScrollTrigger** plugin in site settings. That is the minimum.

Then two structural requirements that are not optional:

| Requirement | Why |
|---|---|
| The component needs a **non-static position** (`relative` works) | The generated canvases are absolutely positioned, and absolute positioning resolves against the nearest non-static ancestor. Leave it static and the fragments anchor to the body — the effect falls apart visually |
| A surrounding container with **`overflow: hidden`** | The fragments are real DOM elements that occupy space; without clipping they spill past the layout and trigger a stray scrollbar |

Both are the same class of mistake as [[stacking-and-dom-order]] and
[[overflow-hidden-on-body]] — worth recognizing, because they recur across every canvas-based effect.

## Tuning (all optional)

| Attribute | Values | Default |
|---|---|---|
| `fc-disintegration` | `component` | — **required** |
| `fc-disintegration-active-on-mobile` | `true` · `false` | `true` |
| `fc-disintegration-rotation-angle` | degrees | `30` |
| `fc-disintegration-rotation-amplitude` | pixels | `40` |
| `fc-disintegration-cascade-offset` | number or percentage | `5` |
| `fc-disintegration-start-point` | ScrollTrigger `start` syntax | `top top` |
| `fc-disintegration-end-point` | ScrollTrigger `end` syntax | `bottom top` |

- `rotation-angle` is a **range**: each fragment gets a random value between minus half and plus half
  the value.
- `rotation-amplitude` is how far fragments travel from their original position — raise it and the
  pieces fly further.
- `cascade-offset` is the stagger between fragments. `0` for all at once, or a value like `10` (or a
  percentage like `2%`) to make each fragment follow the last.
- Note the end default is **`bottom top`**, not `bottom bottom` — the sibling effect
  [[canvas-image-sequence]] uses the latter, so don't carry assumptions between them.

## Set `active-on-mobile` deliberately

This is the attribute to think about rather than leave alone. The effect creates a stack of canvases,
walks every pixel, duplicates and scatters them, then animates the lot on scroll — and the extra
canvases are most costly exactly where devices are weakest.

`fc-disintegration-active-on-mobile="false"` turns it off from tablet down. On a showpiece hero that is
usually the right call.

## Budget it like a showpiece

**This is genuinely heavy under the hood.** One instance is a snapshot, a full pixel walk, a fragment
build, and a scroll-driven animation over all of them.

So: **one hero image, or a few key visuals — never a CMS grid.** If someone asks to put it on every card
in a collection, say plainly that the cost is per-instance and multiplies. It pairs well with Webflow's
own while-scrolling interactions on the same wrapper — animate a heading alongside it — which gets you a
richer set piece without a second disintegration.

## Accessibility is handled, and it's worth knowing how

To work around cross-origin restrictions the script temporarily removes the original image and reloads
it as a base64 blob. **Before it does, it captures the original `alt` text and reassigns it to the image
it generates** — so screen readers get the same experience as with the untouched image.

The practical consequence for you: **write a real `alt` on the source image.** The script preserves what
you gave it; it cannot invent one.

## Verify

**Publish and test on the live link.** The Designer's *preview custom code* does not render this
reliably, so a blank or broken result in preview proves nothing — see [[designer-vs-published]].

Then scroll through and check three things: fragments stay inside the layout (no stray horizontal
scrollbar), they scatter from the image's own position rather than from the top of the page (which means
the component is still `static`), and the effect is off on mobile if you set it to be. It adapts to
responsive layouts automatically, because each canvas is generated from the layout as it currently
appears — so re-check after a breakpoint change.
