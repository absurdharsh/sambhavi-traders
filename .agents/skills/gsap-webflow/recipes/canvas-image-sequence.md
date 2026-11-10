---
type: recipe
title: Play an image sequence on a canvas as the page scrolls
summary: >
  The Apple-style scroll animation is ~150 stills swapped on a canvas — no video, no WebGL, no
  Lottie; the build splits into an asset pipeline (extract, compress, upload, then fetch the CDN URLs
  via the Webflow Data API because Webflow renames every file) and a runtime of two attributes on a
  tall section with a sticky container.
level: advanced
tags: [gsap, scrolltrigger, canvas, image-sequence, webflow-api, assets, accessibility]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: apple-style-image-sequence-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-video-animation-on-scroll
assumes: [scroll-driven]
related: [scroll-driven, disintegration-on-scroll, verified-identifiers]
---

## The rule

**There is no video.** A `<canvas>` is a blank programmable surface; the script draws one frame onto
it and swaps that frame as scroll progress changes. ~150 images at 24fps read as fluid motion.

A ~6-second source clip is the sweet spot: `6s × 24fps ≈ 144` frames — enough progression to look
smooth, few enough to stay light. 1080p is plenty.

## The layout

```
section  300vh            ← fc-image-scrubbing="component" + fc-image-scrubbing-urls
  container  100vh        ← position: sticky, top: 0, padding 0
    canvas  100% × 100%   ← custom element, tag "canvas", aria-hidden="true"
  p                       ← visually hidden, describes the animation
```

Same scaffold as any scrub: tall section for scroll distance, sticky full-viewport container as the
pinned window. Taller section = slower playback. See [[scroll-driven]].

## The attributes

| Attribute | Values | Default |
|---|---|---|
| `fc-image-scrubbing` | `component` | — |
| `fc-image-scrubbing-urls` | comma-separated list of image URLs | — **required** |
| `fc-image-scrubbing-fit` | `contain` · `cover` | `contain` |
| `fc-image-scrubbing-fit-landscape` | `contain` · `cover` | `contain` |
| `fc-image-scrubbing-fit-portrait` | `contain` · `cover` | `contain` |
| `fc-image-scrubbing-fps` | number | `24` |
| `fc-image-scrubbing-start-point` | ScrollTrigger `start` syntax | `top top` |
| `fc-image-scrubbing-end-point` | ScrollTrigger `end` syntax | `bottom bottom` |

`fps` does **not** change render speed — scroll drives that. It sets how finely the frames are spaced
across the scroll range.

The orientation overrides are the useful pair: `cover` on landscape for an immersive edge-to-edge
feel, `contain` on portrait so the frame doesn't become oversized on a tall narrow screen.

## The asset pipeline — and its two traps

**1. Extract.** In a frame extractor, set the frame count and the distance between frames in
milliseconds: total duration ÷ frame count, so ~40ms for a 6s / 144-frame clip. Avoid alpha unless you
truly need it — it makes frames heavy.

**2. Compress — and this is not only about weight.** **Webflow silently discards images it considers
visually identical**, even under different names. That happens constantly at the still start or end of
an animation, and it will quietly punch holes in your sequence. Compressing nudges each frame's bytes
just enough that the duplicates survive.

**3. Upload**, then select all and use *Compress selected assets → AVIF*. Move every frame into a
folder named exactly **`frames`** — the URL generator relies on that name.

**4. Fetch the URLs via the Data API, because you cannot guess them.** **Webflow renames every asset
on upload** — `00001.png` becomes something unpredictable, and only the part after the underscore is
yours. So:

- Call *list asset folders* to find the `frames` folder (the response includes the IDs of the images
  inside it).
- Call *list assets* to get the full CDN URLs.
- Run both responses through the generator tool, which returns a clean ordered array.

**The API returns only 100 assets per request.** With ~150 frames you need a second call with
`offset=100`; the tool merges the batches. Forget this and your animation stops two thirds of the way
through.

Authenticate with a read-only API token as a Bearer token; the Site ID is in *Site settings →
Overview*.

## Accessibility

**A canvas conveys nothing to a screen reader** — it is pixels. Mark it `aria-hidden="true"` and put
the message it carries in a visually-hidden paragraph beside it. If the sequence is purely decorative,
`aria-hidden` alone is enough; if it communicates something (an animated logo, a product transforming),
the text is not optional.

## Currency note (2026-07-27)

Two dependencies to sanity-check before recommending this, because neither is under your control:

- **Third-party tools** — the frame extractor, the compressor and the URL generator are all external
  web tools. If one is gone, the *pipeline* changes; the runtime and attributes don't.
- **The Webflow Data API** — endpoint shapes and pagination are versioned. The 100-per-request limit
  and the `offset` parameter were current as of the lesson; check the reference rather than trusting
  the number.

The effect itself is plain `<canvas>` plus ScrollTrigger, which is stable and widely supported. GSAP
and ScrollTrigger are free and hosted by Webflow — enable them in site settings, never load them from
an external CDN.

## Verify

Publish and scroll the whole section slowly, then fast, then back up. Watch specifically for a **jump
near the end** (the missing second API batch) and for a **frozen stretch** in the middle of otherwise
smooth motion (frames dropped as duplicates on upload). Then check portrait and landscape to confirm
the `fit` overrides do what you want.
