---
type: reference
title: Concepts — the vocabulary and mental model
summary: >
  Six pages covering what the pieces of a Webflow GSAP interaction are and how they relate:
  the trigger/target/action model, timeline control, relationship-based targeting, stagger,
  Split Text, and scroll-driven animation.
level: beginner
tags: [meta, index, concepts]
status: verified
generated: 2026-07-27
verified_against: lesson-prose
sources: []
assumes: []
related: [interactions-model]
---
Answers to *"what is this"* and *"what's the difference between…"*. Recipes and pitfalls
point here through `assumes:`, so this is where a beginner's traversal starts and where an
expert's stops.

**Read [[interactions-model]] first if you read only one.** Every other page in the bundle
assumes it.

| Page | What it answers |
|---|---|
| [[interactions-model]] | Trigger, target, action, animation, timeline — and how the action's *type* replaces the old initial-state step |
| [[timeline-control]] | Control, speed, jump to, delay, and why interaction repeat is not action repeat |
| [[target-filters]] | How one interaction serves twelve identical CMS cards: the target/filter/reference composition |
| [[stagger]] | Offset time vs total time, the *from* origin, the two easings, and when GSAP's stagger is the wrong tool |
| [[split-text]] | Splitting one text element into words, lines or characters, and what the mask option does |
| [[scroll-driven]] | Scrub on scroll vs trigger actions, the tall-section-plus-sticky-window scaffold, thresholds |

## Reading order for a beginner

[[interactions-model]] → [[timeline-control]] → then whichever of [[stagger]] /
[[split-text]] / [[scroll-driven]] the question needs. [[target-filters]] is advanced and only
matters once a component is repeated.

## Status

All six pages are **`verified`**. [[target-filters]] is verified against the Designer UI
itself, so its dropdown labels can be quoted to a reader verbatim.
