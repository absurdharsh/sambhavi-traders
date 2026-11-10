---
type: recipe
title: Trap keyboard focus inside an open panel
summary: >
  A GSAP interaction can animate a dialog but cannot manage focus, so a keyboard user Tabs
  straight out of it into the page behind; this script moves focus in on open, cycles Tab
  inside the panel, closes on Escape, and returns focus to the control that opened it.
level: advanced
tags: [gsap, accessibility, focus-trap, keyboard, dialog, sidebar, webflow]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: master-accessible-cms-sidebars-webflow
    cloneable: https://webflow.com/made-in-webflow/website/cms-jquery-detailed-info-sidebar
assumes: [interactions-model, target-filters]
related: [accessible-dialog, scroll-lock]
---

## The rule

**Interactions cannot manage focus.** `role="dialog"` and `aria-modal="true"` *describe* a
modal; they do not make one behave like one. Without a focus trap, Tab walks out of the open
panel and into the page behind it, where the user cannot see where they are.

Four behaviors make it a real dialog, and all four are in the script below:

1. On open, move focus **into** the panel (to the close button)
2. While open, **Tab cycles** within the panel — never out of it
3. **Escape** closes it
4. On close, **return focus** to the control that opened it

## The script

Goes in *Site Settings → Custom Code → Before `</body>` tag*. This is the version from the
cloneable:

```html
<!-- Site Settings → Custom Code → Before </body> tag -->
<script>
  document.addEventListener('DOMContentLoaded', () => {
    let lastTrigger = null;

    const focusableSelector = `
    a[href],
    button:not([disabled]),
    input:not([disabled]),
    textarea:not([disabled]),
    select:not([disabled]),
    [tabindex]:not([tabindex="-1"])
  `;

    document.querySelectorAll('[data-sidebar-open]').forEach((trigger) => {
      trigger.addEventListener('click', () => {
        lastTrigger = trigger;

        const item = trigger.closest("[role='listitem']");
        const sidebar = item?.querySelector('[data-animate="sidebar"]');
        if (!sidebar) return;

        setTimeout(() => {
          const closeButton = sidebar.querySelector('[data-sidebar-close-button]');
          if (closeButton) closeButton.focus({ preventScroll: true });
          else sidebar.focus({ preventScroll: true });
        }, 50);
      });
    });

    document.addEventListener('keydown', (event) => {
      const openSidebar = Array.from(document.querySelectorAll('[data-animate="sidebar"]')).find((sidebar) => getComputedStyle(sidebar).display !== 'none');

      if (!openSidebar) return;

      if (event.key === 'Escape') {
        const closeButton = openSidebar.querySelector('[data-sidebar-close-button]') || openSidebar.querySelector('[data-sidebar-close]');

        closeButton?.click();

        setTimeout(() => {
          lastTrigger?.focus({ preventScroll: true });
        }, 50);

        return;
      }

      if (event.key !== 'Tab') return;

      const focusable = Array.from(openSidebar.querySelectorAll(focusableSelector)).filter((el) => el.offsetParent !== null);

      if (!focusable.length) {
        event.preventDefault();
        openSidebar.focus({ preventScroll: true });
        return;
      }

      const first = focusable[0];
      const last = focusable[focusable.length - 1];

      if (event.shiftKey && document.activeElement === first) {
        event.preventDefault();
        last.focus({ preventScroll: true });
      }

      if (!event.shiftKey && document.activeElement === last) {
        event.preventDefault();
        first.focus({ preventScroll: true });
      }
    });

    document.querySelectorAll('[data-sidebar-close]').forEach((closeButton) => {
      closeButton.addEventListener('click', () => {
        setTimeout(() => {
          lastTrigger?.focus({ preventScroll: true });
        }, 50);
      });
    });
  });
</script>
```

## The contract it expects

The script will silently do nothing if these names don't match your build. Copy them exactly —
see [[verified-identifiers]].

| Identifier | On |
|---|---|
| `data-sidebar-open` | the card's open button |
| `data-animate="sidebar"` | the panel — **this exact value**, not just the attribute |
| `data-sidebar-close-button` | the control focus lands on when the panel opens |
| `data-sidebar-close` | the close button **and** the overlay |
| `role="listitem"` | the collection item the script walks up to via `closest()` |
| `tabindex="-1"` | the panel, so `.focus()` can reach it |

If your panel is not inside a Webflow Collection List there is no `[role='listitem']` ancestor,
and the `closest()` lookup returns null — change that selector to whatever wraps your card.

## Why the details are the way they are

**The `50ms` timeouts are not padding.** The panel is `display: none` until the GSAP `set`
action runs. You cannot focus a hidden element, so the script waits a beat for the interaction
to flip it. Remove the timeout and focus silently fails.

**`display !== 'none'` is how it finds the open panel.** With one panel per CMS item there are
a dozen candidates and only one is visible, so visibility *is* the open state. This is why the
`set` action must genuinely toggle `display` rather than only animating opacity — an
opacity-`0` panel is still "open" to this script, and still Tab-reachable.

**`offsetParent !== null` filters out hidden controls.** A collapsed accordion or an
`overflow`-clipped element inside the panel would otherwise become a Tab stop you can't see.

**`preventScroll: true`** stops the browser jumping the page to the focused element, which
would fight the panel's own animation.

**Escape clicks the close button rather than hiding the panel.** That routes through the same
GSAP interaction as a real click, so the exit animation and the scroll-lock release both happen.
Hiding the panel directly would leave `overflow-hidden` stuck on the body — the page would
never scroll again.

## Currency note — native `<dialog>` (checked 2026-07-27)

A native `<dialog>` opened with `showModal()` provides all four behaviors above with no script
at all: focus moves in, Tab is trapped, Escape closes, focus returns to the opener.

**Prefer it when you are writing your own JavaScript.** This script exists because
`showModal()` cannot be called from a GSAP interaction — if the panel is opened visually from
the interactions panel, focus management is yours. See the same note on [[accessible-dialog]].

## Verify

Keyboard only, on the published site. Tab to a card and press Enter. Focus must land inside the
panel. Tab forward past the last control — it must wrap to the first. Shift-Tab from the first
— it must wrap to the last. Press Escape — the panel must animate out, the page must scroll
again, and focus must be back on the card you started from.
