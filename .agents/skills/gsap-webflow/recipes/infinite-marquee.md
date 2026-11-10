---
type: recipe
title: Build a seamless infinite marquee
summary: >
  Duplicate the content into two identical trail groups side by side and animate them by -100%
  of their own width on an infinite linear loop, so the second group lands exactly where the
  first began and the reset is invisible; -100% rather than a measured width is what makes it
  work for any item count.
level: intermediate
tags: [gsap, marquee, loop, attributes, reusability, webflow]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources:
  - lesson: how-to-build-infinite-looping-marquees-webflow
    cloneable: https://webflow.com/made-in-webflow/website/060-gsap-infinite-marquee
  - lesson: master-webflow-interaction-settings
    cloneable: https://webflow.com/made-in-webflow/website/gsap-interaction-settings
assumes: [interactions-model, timeline-control, target-filters]
related: [timeline-control, target-filters]
---

## The rule

**Two identical trail groups, animated by `-100%`, looping forever.**

```
main container            ← is-marquee combo: overflow: hidden, no horizontal padding
  section_marquee         ← display: flex, data-animate="horizontal-marquee"
    section_marquee-trail ← all your items
    section_marquee-trail ← an exact duplicate
```

Set both trail groups' flex sizing to **don't shrink or grow** so they take their natural width.

The interaction — page load, GSAP:

| Setting | Value |
|---|---|
| Target | `Any element` · **Direct child of** · `Attribute` `data-animate=horizontal-marquee` |
| First match only | **unchecked** — there are two children and both must move |
| Animation | `move X` **to `-100%`** |
| Duration | ~`40s` |
| Easing | **linear** — anything else makes the scroll visibly pulse |
| Repeat | **infinite** |

## How and why

**Why two groups.** One row can't loop: when it scrolls out there is nothing behind it, so you
get a gap and a jarring reset. With a duplicate alongside, the moment the first group has fully
left, the second sits exactly where the first started — reset there and the eye reads an endless
strip.

**Why `-100%` and not a measured width.** The naive version animates by item width × count.
That breaks the moment items have different widths or you add one. Because both trail groups are
identical, `-100%` **of the group's own width** always lands the second group where the first
began — for any item count, any sizes. That single choice is what makes this a system instead of
a one-off.

**`overflow: hidden` on the container, never the body.** Put it on the main container through a
**combo class** (`is-marquee`), not the base class, so you don't clip every container on the
site. This also keeps the visible marquee bounded to your content max-width, which usually looks
better than edge-to-edge. Setting `overflow: hidden` on the body is a hard no — it breaks
scrolling for the whole page.

**Why the attribute, not a class.** One interaction drives every marquee on the site regardless
of its class names. Duplicating a marquee needs no interaction edits — change the attribute
*value* and the action's target to match, and you have a second, independently configured
marquee. See [[target-filters]].

## Direction is axis + sign

Every variant is a one-line change from the base:

| Variant | Animation |
|---|---|
| Horizontal | `move X` `0` → `-100%` |
| Reversed horizontal | `move X` `-100%` → `0` |
| Vertical | `move Y` `0` → `-100%` |
| Reversed vertical | `move Y` `-100%` → `0` |
| Diagonal | `move X` **and** `move Y` together |
| Reversed diagonal | flip both signs |

Give each variant its own attribute value (`reversed-horizontal-marquee`, and so on) and point
its action at that value.

## Pause on hover

A separate hover interaction on the marquee: mouse-enter → control **`pause`**, mouse-leave →
control **`resume`**. Pause holds the playhead where it is, so the strip continues rather than
restarting — see [[timeline-control]].

You can layer an ordinary CSS hover on the items themselves for extra life; it needs no
interaction.

## Interaction repeat or action repeat?

**Here they are equivalent**, because the timeline holds a single action. Set repeat to infinite
on either.

That equivalence stops the moment you add a second action — then the whole sequence must loop
**as a group**, which is *interaction* repeat. Per-action infinite repeat desyncs them. This is
the single most common marquee bug once someone adds a second moving part.

## Guard it

A 40-second linear loop is continuous motion, which is exactly what `prefers-reduced-motion`
exists for. In **conditional playback**, disable the interaction or skip it to the end for
reduced-motion users. Set **`runs on`** to only the pages that have a marquee.

## Verify

Publish and watch one full cycle without blinking. If you can see the reset, the two trail
groups are not identical — usually a stray margin on the last item of one group, or flex sizing
left on *grow*.
