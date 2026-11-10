---
type: recipe
title: Switch between discrete states with jump to and pause
summary: >
  Treat the timeline as a set of states rather than something you play through — a zero-duration
  action per state at a fixed time, each trigger jumping the playhead to its own time with control
  play, and one catch-all event with control pause freezing it there; a CSS transition, not the
  timeline, does the smooth part.
level: advanced
tags: [gsap, state-machine, jump-to, timeline, attributes, css-transition, webflow]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: master-webflow-gsap-state-machines
    cloneable: https://webflow.com/made-in-webflow/website/gsap-event-settings
assumes: [interactions-model, timeline-control, target-filters]
related: [timeline-control, dark-mode-toggle]
---

## The rule

**You don't play through the timeline — you jump between points on it.**

| Piece | Setting |
|---|---|
| One **zero-duration** action per state, at a fixed time | `0s`, `1s`, `2s` … |
| One event per trigger | control **`play`**, **jump to** its state's time |
| One catch-all event over *all* triggers | control **`pause`**, delay **`0.01s`** |
| The smooth part | a **CSS `transition`** on the property, not the timeline |

Each trigger carries the same attribute with a distinct value, so the events can be told apart.

## Build it

**1. The CSS transition.** On the element that changes, e.g. `background-color 800ms ease-out-expo`.
This is what makes the switch look animated. The timeline only teleports between end states.

**2. The states.** Three zero-duration actions on the timeline at `0s`, `1s`, `2s`, each setting the
property to one value.

**3. The triggers.** Give each control the same attribute name with its own value — e.g.
`element-change-color-role` set to purple / blue / orange.

**4. One event per state.** A click event per trigger, control **`play`**, **jump to** matching its
action's time.

**5. The catch-all.** A fourth click event targeting the **shared attribute** (so, all the triggers),
control **`pause`**, delay **`0.01s`**.

## The two details that make it work

**Control `play`, not the default play-from-beginning.** The default restarts the timeline from `0`,
which destroys the state logic — every click would run through the earlier states on its way. `play`
continues from wherever the playhead was put by `jump to`.

**The `0.01s` delay on the pause is not superstition.** It covers one specific edge case: your very
first click is the `0s` state and the playhead is *already* there. Without the delay, pause freezes
the timeline before the state action has had a frame to run, and nothing happens. The micro-delay
lets it apply, then freezes.

## When to actually use this

**Be honest with the reader here: this is over-engineered for most cases.** It is a teaching exercise
in how far event settings go.

| Situation | Approach |
|---|---|
| A handful of buttons each setting a value | **One interaction per button**, animating from the current value to the target. Simpler, easier to hand over |
| Many states, and one interaction's elegance is worth it | The state machine |
| Two states that alternate | Neither — use `toggle play/reverse` on a single timeline. See [[timeline-control]] |

The two-state case is worth calling out because it is the common one and it has a much better answer:
a theme switch is not a state machine, it is one timeline played forward and backward — see
[[dark-mode-toggle]].

## The transferable idea

Even when you don't build this, keep the pattern: **a timeline is addressable.** `jump to` plus
`pause` turns it from a thing that runs into a thing you can index into. That is occasionally exactly
what an interaction needs — a progress indicator parked at step 3, a diagram frozen on one labeled
stage — and nothing else in the panel does it.

And note where the animation actually lives: in CSS. Whenever states must fade rather than snap, GSAP
sets the value and a CSS transition smooths it. Same bridge as [[hover-border-fill]].

## Verify

Publish and click through the states in order, then **out of order**, then click the same one twice.
Any of those restarting the sequence from the first state means an event is still on
play-from-beginning. Then reload and make your **first** click the `0s` state — if nothing happens,
the catch-all pause is missing its delay.
