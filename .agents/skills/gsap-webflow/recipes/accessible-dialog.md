---
type: recipe
title: Build an accessible dialog or slide-in panel
summary: >
  A dialog is one GSAP timeline played forward to open and reversed to close, plus four
  non-negotiable ARIA attributes, real button elements, an outside-click overlay stacked behind
  the panel, and a scroll lock — and it is not finished until focus is managed.
level: intermediate
tags: [gsap, dialog, modal, popup, sidebar, accessibility, aria, webflow]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: accessible-gsap-popup-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-set-action-prevent-scrolling
  - lesson: master-accessible-cms-sidebars-webflow
    cloneable: https://webflow.com/made-in-webflow/website/cms-jquery-detailed-info-sidebar
assumes: [interactions-model, timeline-control, target-filters]
related: [focus-trap, scroll-lock, viewport-units-mobile]
---

## The rule

**One timeline. Open plays it, close reverses it.** Do not build a closing animation.

The panel wrapper is `position: fixed`, full-viewport, high `z-index`, `display: none`,
`height: 100dvh`. The open interaction is three or four actions:

| At | Action | Does |
|---|---|---|
| `0s` | **`set`** | wrapper `display` → `flex` |
| `0s` | **animate** | wrapper `opacity` — type `from`, `0%` |
| `0s` | **animate** | panel `move Y` (or `move X`) — type `from`, e.g. `2rem` / `100%` |
| `0s` | **`set`** | scroll lock — see [[scroll-lock]] |

Then on the close control: a click event with **control `reverse`**. That's the entire close.

**Four ARIA attributes, none optional:**

```
role="dialog"          → announces it as a dialog window, not a generic container
aria-modal="true"      → tells assistive tech the background is not interactive
aria-labelledby="…"    → points at the heading's id, so it announces its own title
tabindex="-1"          → focusable by script, but not in the tab order
```

**Controls must be real `button` elements** — a Webflow *custom element* with the tag `button`
and `type="button"`. Webflow's native button renders as an `<a>`, i.e. navigation. A real
button is focusable, responds to Enter and Space, and is announced as an action. Icon-only
controls need an `aria-label` (`close dialog`, `open join newsletter dialog`), and decorative
icons inside them need `aria-hidden="true"`.

## Outside-click, done right

Do **not** put the close trigger on the wrapper — it would fire on every click, including
inside the form.

Add an overlay div that fills the wrapper but sits **behind** the panel. Both have
`z-index: auto`, so stacking follows DOM order: drag the overlay *above* the panel in the
navigator to push it behind visually. Clicks inside the panel now never reach the overlay.

Then give the overlay **and** the close button the same attribute and target that, so one
trigger serves both:

- popup build: `dialog-animation="close"`
- CMS sidebar build: `data-sidebar-close`

## Variant A — one dialog on the page

Target by class or a custom selector. Close by `reverse`. This is the simple modal: newsletter
signup, confirmation, video lightbox.

## Variant B — one panel per CMS item

Put the panel **inside** the collection item, so each card renders its own, bound to that
item's fields. There is no shared panel and no id-matching script.

One interaction serves every card, using the target/filter/reference composition
([[target-filters]]):

| Element | Target | Filter | Reference |
|---|---|---|---|
| the panel, on open | `Attribute` `data-animate="sidebar"` | Previous sibling of | Trigger element |
| overlay / content wrapper | `Attribute` | Within | Parent of trigger |
| the panel, on close | `Attribute` | Contains | Trigger element |

`aria-labelledby` here points at the heading's `id` **set from the CMS slug**, so each item's
dialog announces its own title rather than all of them sharing one.

Close is *not* a reverse in this build, because the scroll lock must outlive the exit: fade the
overlay out, slide the content out, then at start **`0.8s`** set `display: none` and remove the
lock class.

## Choosing between them

| Situation | Variant |
|---|---|
| One dialog, fixed content | **A** |
| A panel per card in a collection | **B** |
| Close is a plain reverse | **A** |
| The lock must survive the exit animation | **B** |

## It is not done yet

A dialog that animates and locks scroll is still not accessible: a keyboard user can Tab
straight out of it into the page behind. **Add the focus trap** — see [[focus-trap]]. That is
what makes it behave like a real modal.

## Currency note — native `<dialog>` (checked 2026-07-27)

The browser now does much of this for free. A native `<dialog>` opened with `showModal()`
traps focus, returns focus to the opener on close, closes on Escape, and gives you a
`::backdrop`. The `closedby="any"` attribute adds click-outside dismissal (Chrome 134+, part of
Interop 2026).

**But it does not replace this recipe for an IX3-driven panel**, for one concrete reason:
`showModal()` is a JavaScript call, and a GSAP interaction cannot make it. The moment you drive
opening from the interactions panel, you own the semantics and the focus management yourself —
which is exactly what the ARIA set and [[focus-trap]] above provide.

Tell a reader building with custom code to prefer native `<dialog>`. Tell a reader building
visually in Webflow to use this recipe.

## Verify

Publish. The Designer canvas will not exercise scroll lock or focus. On the live link: open and
confirm the page behind does not scroll; click outside and confirm it closes; press Escape;
then Tab all the way round and confirm focus never leaves the panel and returns to the control
that opened it.
