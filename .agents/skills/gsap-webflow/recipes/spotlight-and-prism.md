---
type: recipe
title: Build a cursor spotlight and an SVG prism hover
summary: >
  The spotlight is darkness with a hole in it — an oversized radial-gradient overlay moved by a
  mouse-move interaction — and the prism is an SVG filter that splits the image into R, G and B
  channels with zero offsets at rest, so a small GSAP timeline animating those offsets pulls the
  colors apart on hover and focus.
level: advanced
tags: [gsap, svg-filter, spotlight, hover, feColorMatrix, accessibility, webflow]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: webflow-spotlight-prism-effects
    cloneable: https://webflow.com/made-in-webflow/website/spotlight-animation-svg-filter-gsap
assumes: [interactions-model, timeline-control]
related: [glassy-button, stacking-and-dom-order, verified-identifiers]
---

## Three layers, independently useful

| Layer | Needs code? |
|---|---|
| Cursor spotlight | **No** — a radial gradient plus a classic mouse-move interaction |
| Scale + rotate on hover | **No** — one GSAP timeline, `play` / `reverse` |
| Prism color split | **Yes** — an SVG filter plus a small GSAP script |

Build them in that order. Each one stands alone.

## Layer 1 — the spotlight

**It isn't light, it's darkness with a hole in it.** A full-bleed overlay over the image carries a
**radial gradient** that is transparent in the middle and black at the edges. Tighten the color stops —
a lazy fade reads as fog; pulling the black in close with a couple of semi-transparent stops gives a
crisp circle that reads as a real spotlight.

Put the overlay **between the image and the content** in the navigator, so it darkens the photo but
leaves your heading untouched.

**Move it with a classic "mouse move over element" interaction** on the section: X from `-50vw` to
`50vw`, Y from `-50vh` to `50vh`. Those are exactly the distances from viewport center to each edge, so
the spotlight lands precisely under the pointer. Smoothing ~95% makes it silky.

**The trap:** an absolutely positioned overlay only covers its parent, so the instant it moves it
exposes an edge. **Oversize it** — width and height `200%`, offset `-50%` on top and left to recentre —
so it blankets the viewport wherever the cursor pushes it. Section gets `overflow: hidden`.

**The stacking lesson baked in:** the absolutely positioned image naturally sits *above* a `static`
container, so reordering in the navigator does nothing until you give the main container
`position: relative`. Then both share `z-index: auto`, DOM order wins, and the later element renders on
top. See [[stacking-and-dom-order]].

## Layer 2 — hover scale and rotate

One timeline (scale `1.1`, rotate `3°`, ~`1.5s`, `power1.inOut`), two events: mouse-enter → **`play`**,
mouse-leave → **`reverse`**. Because both act from the playhead's current position, flicking the cursor
in and out feels continuous instead of jumpy. See [[timeline-control]].

## Layer 3 — the prism filter

```html
<!-- Embed placed BEFORE the section -->
<svg xmlns="http://www.w3.org/2000/svg" width="0" height="0" style="position:absolute">
  <defs>
    <!-- PRISM BASIC: splits R, G, B and shifts them slightly -->
    <filter id="prism-basic" color-interpolation-filters="sRGB">
      <feColorMatrix in="SourceGraphic" result="red" type="matrix"
        values="1 0 0 0 0
                0 0 0 0 0
                0 0 0 0 0
                0 0 0 1 0"/>
      <feOffset in="red" dx="0" dy="0" result="redShift"/>

      <feColorMatrix in="SourceGraphic" result="green" type="matrix"
        values="0 0 0 0 0
                0 1 0 0 0
                0 0 0 0 0
                0 0 0 1 0"/>
      <feOffset in="green" dx="0" dy="0" result="greenShift"/>

      <feColorMatrix in="SourceGraphic" result="blue" type="matrix"
        values="0 0 0 0 0
                0 0 0 0 0
                0 0 1 0 0
                0 0 0 1 0"/>
      <feOffset in="blue" dx="0" dy="0" result="blueShift"/>

      <feBlend in="redShift" in2="greenShift" mode="screen" result="rg"/>
      <feBlend in="rg" in2="blueShift" mode="screen" result="rgb"/>
    </filter>
  </defs>
</svg>
```

Apply it to the image with a custom property: `filter: url(#prism-basic)`.

**Why it does nothing at rest.** Each `feColorMatrix` is a channel mask — the red one keeps row 1 and
zeroes green and blue, and so on, with the last row preserving alpha. `screen` blending adds the three
masked copies back together, so with all offsets at `0` the channels recombine into the original image
exactly. The animation is simply moving them apart.

**`color-interpolation-filters="sRGB"`** keeps the blend in the color space you are actually looking at,
instead of the linear space filters default to. Drop it and the colors come out wrong.

**Two placement rules.** The filter embed must sit **before** the section in the DOM, so it is defined
before it is referenced. The script goes at the **end of the page**, so everything exists when it runs.

## The script, and its two limits

The button is found by the **`fc-prism-button`** attribute (present, no value), so your class names stay
yours. The timeline animates the three `feOffset` nodes' `dx`/`dy`:

```js
const tl = gsap.timeline({ paused: true, defaults: { duration: DURATION, ease: EASE }})
  .to(redOffset,   { attr: { dx: DX_RED,   dy: DY_RED   } }, 0)
  .to(greenOfset,  { attr: { dx: DX_GREEN, dy: DY_GREEN } }, 0)
  .to(blueOffset,  { attr: { dx: DX_BLUE,  dy: DY_BLUE  } }, 0)
```

Defaults pull red up-left by `6` and blue down-right by `6` while green stays put — that is what reads as
a prism split. Push them further for a louder aberration, or move one channel only for a subtle tint.
Tunable constants: `DX_RED`, `DY_RED`, `DX_GREEN`, `DY_GREEN`, `DX_BLUE`, `DY_BLUE`, `DURATION`, `EASE`.

**Limit 1 — one button only.** The script uses `querySelector`, singular. A second prism button on the
same page needs the selection turned into a loop.

**Limit 2 — the channel names are the contract.** It finds the nodes with `feOffset[in=red]`,
`feOffset[in=green]`, `feOffset[in=blue]`. **Rename `red` / `green` / `blue` in the filter and the
selectors silently stop matching.** See [[verified-identifiers]].

## Keyboard parity is built in — keep it

The handlers run on `pointerenter` / `pointerleave` **and** `focus` / `blur`. That is deliberate: a
keyboard user tabbing to the button gets the same effect. If you rewrite the script, keep both pairs —
binding only the pointer events is the most common way an otherwise good hover effect excludes people.

## Why `filter` works here but `backdrop-filter` wouldn't

This applies the SVG filter with the ordinary **`filter`** property, which is well supported everywhere.
That is *not* true of SVG filters referenced from `backdrop-filter` — see the support warning on
[[glassy-button]]. Same filter primitives, very different reach.

## Verify

Publish. Move the cursor across the hero and confirm the spotlight tracks it with no exposed edge at any
corner (an edge means the overlay isn't oversized). Then hover the button and confirm the color fringe
appears, **and Tab to it** to confirm the same thing happens on focus.
