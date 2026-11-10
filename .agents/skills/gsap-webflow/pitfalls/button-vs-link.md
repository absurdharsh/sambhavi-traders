---
type: pitfall
title: Webflow's native Button is wrong for animated controls
summary: >
  Webflow's native Button element renders as an anchor and cannot nest children, which makes it
  wrong twice over — semantically wrong for anything that performs an action rather than
  navigating, and structurally useless for the layered hover effects that need an icon or overlay
  inside.
level: intermediate
tags: [webflow, semantics, accessibility, buttons, links, hover, aria]
status: verified
generated: 2026-07-27
verified_against: lesson-prose
sources:
  - lesson: webflow-gsap-hover-micro-interactions
    cloneable: https://webflow.com/made-in-webflow/website/gsap-microinteractions
  - lesson: accessible-gsap-popup-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-set-action-prevent-scrolling
  - lesson: master-accessible-cms-sidebars-webflow
    cloneable: https://webflow.com/made-in-webflow/website/cms-jquery-detailed-info-sidebar
assumes: []
related: [accessible-dialog, focus-trap]
---

## The symptom

Two different-looking problems with one root:

- You want to animate an icon *inside* a button and there is nowhere to put it — Webflow's Button
  element won't accept children.
- A screen reader announces your "Open dialog" control as a **link**, and keyboard users find
  that Space doesn't activate it.

## The cause

**Webflow's native Button element renders as an `<a>`.** It is a styled anchor — navigation,
semantically — and it accepts no child elements.

So it is wrong along two independent axes, which is why the corpus rejects it from two different
directions and arrives at two *different* replacements. That looks like a contradiction until you
see the question underneath: **is this control navigating, or performing an action?**

## The rule

| The control… | Use | Why |
|---|---|---|
| Goes somewhere — another page, an anchor, an external URL | **Link block** | It genuinely is a link, and a link block nests children freely |
| Does something — opens a dialog, toggles a theme, resets a stack | **Custom element, tag `button`, `type="button"`** | Focusable, activates on Enter **and** Space, announced as an action |
| Webflow's native Button | **neither** | An anchor that can't nest — wrong semantics *and* no structure |

**Both replacements nest children.** That is the part people miss: choosing correct semantics
does not cost you the layered structure the animation needs.

## For animated controls specifically

Layered hover effects need an inner structure — an icon, an `overflow: hidden` text wrapper, an
absolutely-positioned overlay div. All of the micro-interaction patterns in the corpus animate
those *children*:

- a text wrapper's `width` from `0` to `auto` inside an `overflow: hidden` parent
- an overlay sliding from `translateX(-101%)` to `0`
- an arrow icon rotating inside the button

None of that is possible in Webflow's Button element. And it connects to a second boundary worth
holding onto: **Webflow's hover state styles only the element itself, never its children or
siblings** — so the moment your effect reaches into a child, you need both a nestable element
*and* a GSAP interaction. See [[interactions-model]].

## Don't forget the labels

An icon-only control has no accessible name. Whichever element you chose:

- `aria-label` on the control — `close dialog`, `open join newsletter dialog`
- `aria-hidden="true"` on the decorative icon inside it

And on a custom `button` element, set **`type="button"`** explicitly. Without it, a button inside
a form defaults to submit, and clicking your "open panel" control submits the form instead.

## Verify

Tab to the control and press **Space**. A real button activates; a link does not. Then check what
a screen reader announces — "button" versus "link" is the whole difference, and it tells the user
whether to expect the page to change.
