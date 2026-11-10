---
type: recipe
title: Draw a border on hover with a conic gradient
summary: >
  There is no border — a slightly larger wrapper behind the element leaves a visible ring, filled by
  a conic gradient whose fill percentage is registered with @property so it can actually transition;
  GSAP's only job is toggling one class on the hovered instance's wrapper.
level: advanced
tags: [gsap, hover, css, at-property, conic-gradient, set-action, attributes, webflow]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: growing-border-animations-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-growing-border-animation
assumes: [interactions-model, target-filters]
related: [target-filters, unused-class-cleanup, designer-vs-published]
---

## The rule

**There is no border property anywhere in this.** Wrap the element in a slightly larger circle; the
inner element covers most of it, leaving a thin ring visible. Give the ring a **conic gradient** —
conic because color spreads radially around a circle — and animate how far it fills.

Three parts, and GSAP is the smallest of them:

| Part | Job |
|---|---|
| `@property --fill` | makes the fill percentage a *typed* value, so it can transition |
| The CSS transition | does the actual animation |
| GSAP | a `set` action toggling one class on the right wrapper |

## The CSS

```html
<!-- Embed on the body, display: none, dragged above the section so it loads first -->
<style>
  @property --fill {
    syntax: '<percentage>';
    inherits: true;
    initial-value: 0%;
  }

  [data-animate-link-border] {
    transition: --fill 0.8s cubic-bezier(.19, 1, .22, 1);
    background: conic-gradient(greenyellow var(--fill), transparent var(--fill));

    &.on-hover {
      --fill: 100%;
    }
  }
</style>
```

**`@property` is the load-bearing line.** You cannot smoothly transition an ordinary CSS custom
property — the browser treats it as an untyped string and swaps it instantly. Registering it with
`syntax: '<percentage>'` tells the browser it is a real percentage, and *only then* does the
`transition` on `--fill` do anything. Remove the `@property` block and the border snaps from 0 to 100
with no animation, which is the most likely way this build fails.

## Build it

**1. The element.** A link block: flex-centered, square, `border-radius: 100%`. Inside, an embed with
your SVG (`fill: currentColor`). Put the color on the **link**, not the embed, add a color
transition, and set the hover-state color there.

**2. The fake-border wrapper.** Wrap it in a slightly larger square, flex-centered, `border-radius:
100%`. Add the attribute **`data-animate-link-border`**.

**3. The CSS.** Embed on the body, `display: none`, dragged **above** the section — the `@property`
registration must be parsed before anything references `--fill`.

**4. Register `on-hover`.** A hidden `div` carrying the class, ideally in a style guide, or a cleanup
deletes it — see [[unused-class-cleanup]].

**5. The interaction.** Add `data-animate-link` to the link. Hover interaction, mouse-enter +
mouse-leave, one `set` action that **toggles** the `on-hover` class:

| Control | Value |
|---|---|
| Target | `Attribute` `data-animate-link-border` |
| Filter | **Direct parent of** |
| Reference | `Trigger element` |

## Why the filter is not optional

Without it, the action targets *every* element carrying `data-animate-link-border` and **every link's
border fills at once**. The filter resolves the target relative to the trigger, so only the hovered
link's own wrapper is affected.

This is what makes the build reusable: duplicate the link and it animates independently, with no
interaction edits and regardless of class names. See [[target-filters]].

## Why GSAP only toggles a class

The alternative — animating the gradient with an animate action — is not available: the interactions
panel does not animate custom properties of this kind. The class-toggle plus CSS transition pattern is
the standard bridge whenever the property you want is outside what the panel can tween, and it recurs
across the corpus. See [[interactions-model]].

It also means the motion is owned by one line of CSS you can retune without opening the interaction.

## Verify

**Publish, or enable custom code in preview** — `@property` and the gradient will not run in plain
preview, so a "broken" build in the Designer proves nothing. See [[designer-vs-published]].

Then hover **two different links in turn**. If both rings fill, the filter is missing or set to the
wrong reference. Hover and leave quickly: the fill should reverse from wherever it got to, because the
CSS transition — not a timeline — is driving it.
