---
type: recipe
title: Build a stack of draggable, throwable cards
summary: >
  GSAP's Draggable plugin powers a photo-stack you can grab, tilt and toss off-screen, driven entirely
  from Webflow settings by fc-draggable-card attributes; the two structural details that make it feel
  right are overflow hidden on the section and a pointer-events pairing that lets clicks reach the
  reset button once the deck is empty.
level: intermediate
tags: [gsap, draggable, cards, attributes, cms, pointer-events, webflow]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: build-gsap-draggable-cards-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-draggable-cards
assumes: [interactions-model]
related: [verified-identifiers, button-vs-link]
---

## The rule

Three roles, one attribute:

| Value | Goes on |
|---|---|
| `fc-draggable-card="wrapper"` | the drag-zone div holding the stack and the reset button |
| `fc-draggable-card="component"` | each draggable card |
| `fc-draggable-card="reset"` | the reset button |

On load — and again on reset — the script finds every wrapper, looks inside for cards, takes the top
one, adds a hover hint and initializes its drag behavior. You never open the code.

**Enable GSAP and the Draggable plugin** in site settings. Everything is free and hosted by Webflow.

## The structure

```
section                    ← overflow: hidden
  wrapper                  ← position: relative, height (e.g. 80vh), ="wrapper"
    list wrapper           ← position: absolute, all offsets 0, pointer-events: none
      card                 ← position: absolute, pointer-events: auto, ="component"
      card
      …
    button                 ← ="reset"
```

## The three structural decisions that make it work

**`overflow: hidden` on the section.** A thrown card technically leaves the section's bounds. Without
clipping you get a horizontal scrollbar or cards poking out of the layout. This is the legitimate use
of container-level clipping — never put it on the body, see [[overflow-hidden-on-body]].

**`position: absolute` on the card container needs `position: relative` on the wrapper.** Absolute
positioning resolves against the nearest non-static ancestor. Leave the wrapper static and the stack
anchors to something further up. See [[stacking-and-dom-order]].

**The `pointer-events` pairing is the clever bit.** The list wrapper is `pointer-events: none` and each
card is `pointer-events: auto`. So while cards exist they are grabbable — and once every card has been
tossed away, clicks **pass through** the empty list wrapper to the reset button sitting beneath the
stack. Without the pairing, an invisible full-size wrapper would swallow every click and the reset
button would be unreachable.

That is worth understanding rather than copying: it is the general trick for "an overlay that stops
being an overlay once it's empty".

## The tilt

Combo classes: odd cards lean one way, even the other, top card dead center.

**In a CMS Collection List you cannot add manual combo classes**, so use Webflow's **structure
selectors** instead — style *last item* with no tilt (that is your top card), *even items* at one angle,
*odd items* at the other. Everything else is identical: same attributes on the wrapper, the reset
button, and each Collection Item. Two independent stacks on one page work and reset independently.

## Tuning (all optional, all on the card)

| Attribute | Values | Default |
|---|---|---|
| `fc-draggable-card-rotation` | degrees | `45` |
| `fc-draggable-card-reset-duration` | seconds | `0.2` |
| `fc-draggable-card-throw-duration` | seconds | `0.5` |
| `fc-draggable-card-throw-distance` | pixels | `1000` |
| `fc-draggable-card-throw-rotation` | degrees | `45` |
| `fc-draggable-card-threshold` | pixels | `100` |
| `fc-draggable-card-delay` | seconds | `0` |
| `fc-draggable-card-ease` | GSAP ease name | `power4.out` |

`threshold` is the one that changes the feel most: it is how far you must drag before release counts as
a *throw* rather than a snap-back. Lower it and the deck feels light and flicky; raise it and cards
resist being thrown by accident.

Every attribute has a default, so publish with none of them and tune afterwards.

## Make the reset a real button

Change the element's tag to `button` in the settings panel rather than using Webflow's native Button
element, which renders as an `<a>`. This is an action, not navigation — see [[button-vs-link]] — and it
needs to be keyboard-activatable, since dragging is inherently mouse- and touch-only.

## Accessibility note the recipe does not solve

Dragging has no keyboard equivalent here. If the cards carry content a visitor needs, **the content must
also be reachable some other way** — a list below, a link on each card, anything. Treat the stack as a
delightful alternative path to content, not the only path to it. Say this to a reader rather than
shipping an interaction only pointer users can complete.

## Verify

Publish. Drag a card a little and release — it should snap back. Drag past the threshold and release —
it should fly off and spin. Throw the whole deck, then **click the reset button**: if nothing happens,
the `pointer-events` pairing is wrong and the empty list wrapper is eating the click. Then Tab to the
reset button and press Space to confirm it is a real button.
