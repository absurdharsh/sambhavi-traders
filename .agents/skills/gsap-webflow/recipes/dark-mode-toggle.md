---
type: recipe
title: Build a light/dark theme toggle
summary: >
  Put every themed color in a variable collection with a second manual variable mode, then the
  whole theme switch is a GSAP set action toggling one class on the html element; a
  theme-transition class added for 0.8s smooths the change without colliding with your other
  animations, and two small scripts make the choice survive a reload.
level: intermediate
tags: [gsap, dark-mode, variables, variable-modes, set-action, localstorage, webflow]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: webflow-dark-mode-gsap
    cloneable: https://webflow.com/made-in-webflow/website/set-actions-in-interactions-with-gsap
assumes: [interactions-model, timeline-control]
related: [interactions-model, page-load-flicker]
---

## The rule

**Four layers, in this order. Skip the first and nothing else works.**

1. **Variables + a second variable mode.** Put every color the theme touches into a variable
   collection. The default *base* mode is light; add a **manual** mode named *dark mode* and
   override the values there.
2. **Wire the variables up.** Assign them at the *Body (All Pages)* tag first — most elements
   inherit — then specific classes and states.
3. **A `set` action toggles one class.** `set` → **class** → **toggle** on `dark-mode`,
   targeting **`html`**. That is the entire theme switch.
4. **Two scripts for persistence**, below.

Once step 1 and 2 are done, the whole theme change is one class. If you find yourself writing
per-element color animations, go back to step 1.

## The interactions

Two, both with control **`toggle play/reverse`** so each click reverses the last:

| Interaction | Does |
|---|---|
| Knob | animates the switch knob across its track (`move X`) |
| Theme | `set` → class → **toggle** `dark-mode` on `html`, plus the transition class below |

Set **`runs on: all pages`** on the theme interaction. It defaults to the page you built it on,
so a toggle copied into the navbar silently does nothing everywhere else.

**Target `html`, not `body`.** This matters for the persistence script — see below.

## Smoothing the change without breaking your other animations

The naive fix is a CSS transition on every colored element. Don't: those transitions would
collide with any GSAP scroll or hover animation touching the same properties, and you would be
debugging that for a week.

Instead define one class in head CSS that transitions colors on **everything, but only while it
is present**:

```html
<!-- Site Settings → Custom Code → Head -->
<style>
  .theme-transition,
  .theme-transition *,
  .theme-transition *::before,
  .theme-transition *::after {
    transition:
      color 0.8s cubic-bezier(0.16, 1, 0.3, 1),
      background-color 0.8s cubic-bezier(0.16, 1, 0.3, 1),
      border-color 0.8s cubic-bezier(0.16, 1, 0.3, 1),
      outline-color 0.8s cubic-bezier(0.16, 1, 0.3, 1);
  }
</style>
```

In the theme interaction: **`set` `theme-transition` on at `0s`, and off at `0.8s`.** Colors
now ease over 800ms during the switch, and for the rest of the page's life the transition does
not exist, so it can never interfere.

## Persistence — and why the class goes on `html`

Two scripts. The **head** one runs before first paint:

```html
<!-- Site Settings → Custom Code → Head -->
<script>
/**
 * THEME CHECKER
 * Executed before the body renders to prevent the "white flash" (flicker).
 */
(function () {
  const savedTheme = localStorage.getItem('theme');
  const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
  // Dark if the user chose it before, or has no choice but their system prefers dark
  if (savedTheme === 'dark' || (!savedTheme && prefersDark)) {
    document.documentElement.classList.add('dark-mode');
  }
})();
</script>
```

And the **footer** one saves each choice:

```html
<!-- Site Settings → Custom Code → Before </body> tag -->
<script>
/**
 * THEME TOGGLE MANAGER
 * Handles the click event and persistence.
 */
document.addEventListener("DOMContentLoaded", () => {
  const themeToggle = document.querySelector('.mode-toggle');
  const htmlElement = document.documentElement;

  if (themeToggle) {
    themeToggle.addEventListener('click', () => {
      // Read the class on documentElement to stay consistent with the head script
      const isGoingToDark = !htmlElement.classList.contains('dark-mode');
      if (isGoingToDark) {
        localStorage.setItem('theme', 'dark');
      } else {
        localStorage.setItem('theme', 'light');
      }
    });
  }
});
</script>
```

Swap `.mode-toggle` for your toggle's class, and keep the `dark-mode` name in sync with the class
the `set` action toggles.

**`html`, not `body`, is the whole trick.** The `html` element exists before the body renders, so
the head script can apply `dark-mode` *before first paint* — no flash of the wrong theme. Put the
class on `body` and the user sees a white flash on every load. This is the same reasoning as
[[page-load-flicker]]: anything that must be true before the first frame belongs in the head and
on `html`.

Note the fallback order: an explicit saved choice wins; with no saved choice, the OS
`prefers-color-scheme` decides. A user who set their system to dark gets dark on first visit, and
a user who later overrides it keeps their override.

## Give both classes a home in the style guide

`dark-mode` and `theme-transition` are only ever applied by a `set` action, so they are attached to
no element and Webflow flags them as **unused** — trash icon and all. Publishing keeps them, but any
style cleanup deletes them, and the toggle then switches a class with no styles.

Keep an element carrying each one somewhere in your style guide. See [[unused-class-cleanup]] — a
theme toggle that worked for a month and then quietly stopped is almost always this.

## Verify

Publish — custom code does not reliably run in preview. Then: toggle and confirm colors ease
rather than snap; reload and confirm the choice stuck; open in a fresh private window with the OS
set to dark and confirm it opens dark with **no white flash**; and navigate to a second page to
confirm `runs on: all pages` is set.
