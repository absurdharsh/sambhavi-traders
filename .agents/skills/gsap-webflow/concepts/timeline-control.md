---
type: concept
title: Timeline control — control, speed, jump to, delay, repeat
summary: >
  One GSAP timeline can be driven by many events, and the event settings decide what each
  one does to it: control moves or holds the playhead, speed changes the rate without
  moving it, jump to repositions it, and repeat loops either the whole interaction or a
  single action — which are not the same thing.
level: intermediate
tags: [gsap, interactions, timeline, control, repeat, events, webflow]
status: verified
generated: 2026-07-27
# The custom-event snippet (Webflow.require("ix3")) is copied from the cloneable;
# the event settings themselves are derived from verified lesson prose.
verified_against: [cloneable, lesson-prose]
sources:
  - lesson: control-gsap-interactions-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-event-settings
  - lesson: master-webflow-gsap-state-machines
    cloneable: https://webflow.com/made-in-webflow/website/gsap-event-settings
  - lesson: master-webflow-interaction-settings
    cloneable: https://webflow.com/made-in-webflow/website/gsap-interaction-settings
assumes: [interactions-model]
related: [interactions-model, target-filters, scroll-driven]
---

## The rule

An interaction is **one timeline with many events**. Each event carries its own settings
and they answer different questions:

| Setting | What it does |
|---|---|
| **Control** | What this event does to the playhead |
| **Speed** | How fast the timeline runs — without repositioning the playhead |
| **Jump to** | Move the playhead to a specific time |
| **Delay** | Wait before the event takes effect |
| **Repeat** | Loop — see *interaction repeat vs action repeat* below |

Control values, and the distinctions that actually bite:

| Control | Effect |
|---|---|
| `play` | Continue **forward from the current position** |
| `reverse` | Run **backward from the current position** |
| `pause` / `resume` | Hold the playhead where it is / continue from there |
| `stop` | Halt **and reset to the start** |
| `toggle play/reverse` | Alternate forward and backward on each firing |
| `no action` | Deliberately do nothing to the playhead |

**`pause` is not `stop`.** Pause keeps the position; stop resets it to zero.

## How and why

**Hover-in and hover-out are one animation.** Build the effect once, then `play` on
mouse-enter and `reverse` on mouse-leave. Because both act from the playhead's *current*
position, an interrupted hover reverses from that exact frame instead of snapping — and
hovering back in continues forward. Classic Interactions needed two animations for this;
the doubled surface for bugs is gone.

**`toggle play/reverse` collapses an open/close pair.** Give both the open and the close
control a click event on the *same* interaction with `toggle play/reverse`: first click
plays forward, next plays backward. One animation instead of two mirrored ones.

**Reach for `no action` + `speed` when the stop must be permanent.** This is the subtle
one. Suppose an animation must stop for good once a button is clicked. If hover-in and
hover-out use `pause`/`resume`, the hover-out *after* the click restarts it. Fix: set
both hover events to `no action` and give them `speed: 0` (freeze) and `speed: 1`
(resume), then a click event with `stop`. Events that never touch the playhead have no
power to restart what the click already stopped.

**`jump to` + `pause` makes a state machine.** Place a **zero-duration** action for each
state at a fixed time (0s, 1s, 2s), give each trigger `jump to` its state's time with
control **`play`** — not the default play-from-beginning, which would restart the
timeline and break the logic — then add one catch-all event over all the triggers with
control `pause` so the playhead freezes on whatever was selected. The smooth part is a
CSS `transition` on the property, not the timeline. Give the catch-all pause a tiny
**`0.01s` delay**, purely to cover the case where the first click is the 0s state and the
playhead is already there; the micro-delay lets the state action run for a frame before
pause freezes it.

*This is deliberately over-engineered as a teaching device. In production, one
interaction per button is usually simpler — reach for the state machine when a single
interaction genuinely earns its elegance.*

## Interaction repeat vs action repeat

They are **not** interchangeable once a timeline holds more than one action.

| | Loops |
|---|---|
| **Interaction repeat** | The whole timeline, every action together, as one group |
| **Action repeat** | That one action, independently of the others |

- **One action?** Equivalent. A marquee moving a strip `0% → -100%` on X loops the same
  either way.
- **Several actions that must stay in sync?** Interaction repeat. A four-action cascading
  text loop (text-out, text-in, arrow-out, arrow-in) set to repeat per-action desyncs
  into chaos — the text resets while the arrow is mid-flight.
- **One part loops forever while the rest stops?** Only action repeat can do this.

**`back and forth`** plays an action forward then in reverse on each cycle. Paired with a
repeat *count* it composes exact patterns: `repeat: 3` + back-and-forth gives
out-in-out-in-out-in, ending settled in place.

## Driving a page-load timeline from other events

A native page-load interaction **accepts no extra events** — there is no add-event
button. If you need a page-load animation that also responds to hover or click, build the
interaction on a **custom event** instead. That unlocks adding events, and Webflow
generates a snippet you paste into a page-load function (*Before `</body>` tag*) so your
code fires the interaction:

```html
<!-- Before </body> tag -->
<script>
window.onload = function () {
  // Fire the page-load interaction bound to this custom event
  const wfIx = Webflow.require("ix3");
  wfIx.emit("Blurred Background");
};
</script>
```

Rename the event string to match yours.

## Two settings that are not about motion

**`Runs on`** decides which pages load the interaction. The default is the page you built
it on, deliberately: set it to all pages and Webflow ships, parses and evaluates that
JavaScript everywhere, checking for targets that may not exist. On a 30- or 50-page site
that adds up. Scoping it is a free performance win.

**`Conditional playback`** is the accessibility lever, and it does two things. It reads
**`prefers-reduced-motion`** — the OS-level setting people with vestibular disorders or
motion sensitivity turn on — and lets you disable the interaction or skip it to the end
for those users. Parallax, fast movement, scaling and looping can genuinely cause
discomfort, so honoring it is not optional polish. It also selects the **breakpoints**
an interaction runs on, so a heavy desktop effect can simply not run on a low-powered
phone.

## Related

Scroll interactions have their own control vocabulary — scrub versus discrete trigger
actions, and enter/leave/enter-back events. See [[scroll-driven]].
