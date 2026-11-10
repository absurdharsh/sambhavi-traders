---
type: concept
title: The interactions model — trigger, target, action, timeline
summary: >
  A Webflow GSAP interaction is a trigger that fires one or more actions, each applied
  to a set of targets and laid out on a horizontal timeline; the action's type (to /
  from / from to) absorbs the initial state, so there is no separate initial-state step
  the way Classic Interactions had.
level: beginner
tags: [gsap, interactions, model, vocabulary, timeline, webflow, ix3]
status: verified
generated: 2026-07-27
verified_against: lesson-prose
sources:
  - lesson: master-webflow-gsap-interactions
    cloneable: https://webflow.com/made-in-webflow/website/gsap-interactions-core-concepts
  - lesson: master-webflow-gsap-staggered-animations
    cloneable: https://webflow.com/made-in-webflow/website/mastering-gsap-split-text-animations
  - lesson: webflow-gsap-hover-micro-interactions
    cloneable: https://webflow.com/made-in-webflow/website/gsap-microinteractions
assumes: []
related: [timeline-control, target-filters, stagger, split-text, scroll-driven]
---

## The model

Four nested pieces, in this order:

| Piece | What it is |
|---|---|
| **Trigger** | What starts the interaction — page load, click, hover, scroll, a custom event |
| **Action** | One group of property changes with its own duration, easing and start time |
| **Target** | The element or elements an action applies to |
| **Animation** | The individual animated property inside an action, each with its own **type** |

Actions are laid out left to right on a **horizontal timeline**. One interaction holds
many actions; the timeline is where you place, overlap and offset them.

**The type is the initial state.** Every animated property has a type:

| Type | Meaning |
|---|---|
| `to` | Animate from the element's designer state **to** a value you set |
| `from` | Start at a value you set and animate back **to** the designer state |
| `from to` | Set both ends explicitly |

For a page-load reveal you want **`from`** — you declare where things start (invisible,
pushed down) and they land on their real, designed state. There is no separate
initial-state step to create.

## How and why

**Why the horizontal timeline matters.** In Classic Interactions a single logical
sequence often had to be split across several separate interactions just to stay
readable, because everything stacked vertically. Horizontally you can see, edit,
duplicate and reorder every staggered action in one view. That is the practical reason
complex motion is tractable here and wasn't before.

**Complex animations are many simple actions.** This is the load-bearing habit. A
button hover that reads as sophisticated — scale up with an elastic bounce, darken the
background, an arrow that slides out of its circle then re-enters from below — is four
independent single-purpose actions timed to converge. Do not try to express a
multi-part effect as one clever action.

**Three kinds of action.** *Animate* tweens values over time. **`set`** applies a value
instantly at a point on the timeline — use it for anything that is a state change rather
than motion (`display`, a class, `overflow`). **Animate variable** tweens a Webflow
design variable, which is how you drive several CSS properties from one number. See
[[timeline-control]] for placing them in time.

**Classic and GSAP coexist.** Both engines can live in the same project without their
actions colliding, so you can keep a simple Classic mouse-move trigger while a GSAP
timeline handles the page-load reveal. Interactions with GSAP is the default on new
sites and the only version that receives new features, so build new work there.

**Two triggers fighting over the same element** is the most common structural bug in a
page-load build: a reveal and a hover effect both live from the first frame, and the
visitor's cursor disrupts the entrance. The fix is a **gatekeeper element** — a
full-bleed absolutely-positioned wrapper that is the *sole* listener for the hover or
mouse-move trigger, kept at `display: none` and switched to `block` by a `set` action at
the very end of the intro timeline. The second effect simply waits for its cue.

## Where GSAP stops and CSS starts

Two boundaries, both worth internalising before reaching for an interaction.

**Webflow's hover state styles only the element itself — never its children or
siblings.** So: if a hover effect changes only the hovered element's own properties
(background, text color, `box-shadow`), the hover state plus a CSS transition is
enough, ships no JavaScript, and is easier to reuse. The moment you must animate a
child — rotating an icon inside a button — that is your cue to add a GSAP interaction.
Each interaction adds JavaScript Webflow injects into the page, so the choice is not
free.

**Some properties still need the class bridge.** Blur and other CSS `filter` values are
not animatable in the interactions panel. The workaround is a hybrid: define a helper
class carrying the end value, give the base element an ordinary CSS `transition` for
that property, and use a `set` action to add or remove the class. GSAP never touches the
property; the CSS transition does the smooth part. This "GSAP toggles a class, CSS
animates" pattern recurs across the corpus — see [[stagger]] for the case where it beats
GSAP's own stagger.

## Currency note — variables (checked 2026-07-27)

The source lesson (November 2025) states that GSAP interactions cannot animate custom
CSS variables. **That is no longer true.** Webflow has since shipped an **Animate
variable** action supporting **color, size, percentage and number** variables, and the
later lessons in the corpus use it. Border-radius, which the same lesson routes through
the class bridge, can now be driven by an animated size variable instead.

The class bridge is still required for blur / `filter`.

## Sources

Lessons: `master-webflow-gsap-interactions` ·
[cloneable](https://webflow.com/made-in-webflow/website/gsap-interactions-core-concepts) ·
`webflow-gsap-hover-micro-interactions` ·
[cloneable](https://webflow.com/made-in-webflow/website/gsap-microinteractions) ·
[Webflow: Animate variables in Interactions with GSAP](https://help.webflow.com/hc/en-us/articles/50267494904211-Animate-variables-in-Interactions-with-GSAP)
