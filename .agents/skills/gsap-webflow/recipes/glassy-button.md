---
type: recipe
title: Build a glass button that refracts the page behind it
summary: >
  A real SVG displacement-map filter wired to the button through backdrop-filter bends whatever scrolls
  underneath, and a tiny GSAP timeline hardens it from frosted glass to crisp ice on hover — but SVG
  filter references in backdrop-filter are Chromium-only, so Safari and Firefox visitors see the plain
  frosted base and none of the refraction.
level: advanced
tags: [gsap, svg-filter, backdrop-filter, glass, hover, browser-support, accessibility]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: webflow-glassy-button-svg-gsap
    cloneable: https://webflow.com/made-in-webflow/website/liquid-glass-crystal-button
assumes: [interactions-model, timeline-control]
related: [spotlight-and-prism, verified-identifiers]
---

## Read this before you build it (checked 2026-07-27)

**SVG filter references in `backdrop-filter` are Chromium-only.** `backdrop-filter` itself is Baseline,
but that covers the standard filter functions like `blur()` — **not** `url(#some-filter)`. Firefox does
not support it, and WebKit has an open bug for exactly this case (`feDisplacementMap` via
`backdrop-filter`).

| Browser | What the visitor gets |
|---|---|
| Chromium (Chrome, Edge, Brave, Opera) | The full effect |
| **Safari — including every browser on iOS** | The frosted base only. **No refraction** |
| **Firefox** | The frosted base only. **No refraction** |

**How it degrades.** The button keeps its own styling — transparent background with a white gradient,
pill radius, thin white border — so it still looks like a deliberate glass button. The GSAP hover still
nudges it up and scales it, because those animate the button itself. What is missing is the entire
refraction, which is the reason to build it.

**Say this to anyone who asks for this effect.** It is not a progressive-enhancement footnote: on iOS —
where every browser uses WebKit — the showpiece simply does not appear. That may be fine for a
Chromium-heavy audience or a portfolio piece, and wrong for a marketing hero. Let the person decide with
the facts.

## The rule

The glass look is an **SVG filter**, not a CSS blur. Paste the filter into an Embed, set it
`display: none`, and move it to the **very top of the `<body>`** — placement matters, because lower down
some browsers scope it locally or skip it, the same way a CSS variable belongs in `:root`.

```html
<!-- Embed at the very top of the body, display: none -->
<svg>
  <filter id="glass"
          x="-1" y="-1" width="3" height="3"
          filterUnits="objectBoundingBox"
          primitiveUnits="objectBoundingBox">
    <feImage x="-0.5" y="-0.5" width="2" height="2"
             preserveAspectRatio="none"
             result="map" />
    <feGaussianBlur in="SourceGraphic" stdDeviation="0.02" result="blur"/>
    <feDisplacementMap in="blur" in2="map"
                       scale="0.8" xChannelSelector="R" yChannelSelector="G"/>
  </filter>
</svg>
```

Then on the button, a custom property: **`backdrop-filter: url(#glass)`**.

## The three primitives

| Primitive | Job |
|---|---|
| `feImage` | loads the **displacement map** — a texture whose light and dark areas say how far to push each pixel |
| `feGaussianBlur` | softens the button first; `stdDeviation` is sharp-versus-cloudy |
| `feDisplacementMap` | takes the blurred button and the map together and shifts pixels; `scale` is the intensity dial |

The filter region is deliberately oversized (`x="-1" y="-1" width="3" height="3"`) so nothing clips at
the edges.

**`primitiveUnits="objectBoundingBox"` is the detail that makes the numbers make sense.** Every value
inside the filter is a **fraction of the button's own box**, not a pixel count. That is why
`stdDeviation="0.02"` and `scale="0.8"` look tiny and hit hard — `0.8` means the displacement can shove a
pixel by most of the button's width. It also means the effect scales automatically with the button
instead of needing different numbers per size.

**This is the one identifier-class error the code-gap pass caught inside the GSAP corpus**: plausible
"1–5" and "20+" pixel ranges were wrong by orders of magnitude. Push `scale` past ~`1.4` and the glass
reads as shattered rather than refracted.

## Why a script is unavoidable

**`feImage` has no `href` in the markup** — deliberately. The displacement map is an external image, so a
script fetches it and feeds it in. Without that, the filter knows it should distort but has no texture to
distort with.

The loader does a second, less obvious job: **it clones the filter once per button.** One filter animated
by two buttons would fight itself — hovering one would warp both — so each clone gets its own id
(`glass-1`, `glass-2`, …) and that button's `backdrop-filter` is repointed at its own copy. The
`url(#glass)` you set by hand in Webflow is really the Designer-time preview; on the live page the script
swaps it.

It keeps each clone's blur and displacement nodes in a `WeakMap` and fires a **`glass:filters-ready`**
event when done, which is how the animation script knows what to grab. Buttons are found by the
**`fc-glass-button`** attribute (present, no value), never by class.

**`MAP_URL` at the top of the loader points at an external server.** Swap it for your own hosted copy
rather than depending on someone else's — if that host goes away, every glass button on your site loses
its texture.

## The hover: glass → ice

A tiny timeline, and the direction of travel is the whole trick: **displacement goes up while blur goes
down.** More distortion, less haze — which is exactly what reads as glass hardening into ice.

| Constant | Default | Range |
|---|---|---|
| `HOVER_SCALE` | `1.40` | `1.05–1.25` classy ripple · `1.40+` jagged broken-crystal |
| `HOVER_TRANSF_SCALE` | `1.015` | keep modest — this is already noticeable |
| `HOVER_BLUR` | `0.00` | `0` crisp · `~0.03` softer landing |
| `DURATION` | `0.30` | `0.2` snappy · `0.4` elegant |
| `EASE` | `power1.out` | also `power2.out`, `expo.out` |

It also nudges the button `y: -1` and scales it slightly, for tactile feedback. The same animation runs on
**`focus`** and reverses on **`blur`**, so keyboard users get the identical experience — keep both event
pairs if you rewrite it.

## Give it something to refract

The effect only shows when there is content behind the button. In the demo the button is `fixed` and
centered while a full-width background image scrolls underneath, so the refraction changes as you scroll.
Any rich, scrollable background works; over a flat color there is nothing to bend and the whole thing is
invisible even in Chromium.

Style the base with a transparent background, a subtle white linear gradient (~`135deg`, white at ~20% →
~8% → ~3% opacity), a pill `border-radius`, and a 1px white border at ~30% opacity. **This base is what
non-Chromium visitors see**, so make it good on its own.

## Verify

**Test in Safari first, not last** — that is where you find out what most visitors get. Then in Chromium:
scroll the background behind the button and confirm the refraction changes; hover to confirm it hardens;
Tab to it to confirm focus does the same. With several buttons, hover one and confirm the **others don't
move** — if they do, the per-button filter cloning isn't running.
