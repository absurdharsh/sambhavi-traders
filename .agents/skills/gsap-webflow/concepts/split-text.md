---
type: concept
title: Split Text — turning one text element into many targets
summary: >
  Split Text breaks a single text element into words, lines or characters so each piece
  becomes its own animation target, which is what gives stagger something to spread across;
  the mask option wraps every piece in its own clipping container so it stays hidden until
  it moves into view.
level: intermediate
tags: [gsap, split-text, stagger, text, reveal, webflow]
status: verified
generated: 2026-07-27
verified_against: lesson-prose
sources:
  - lesson: master-webflow-gsap-staggered-animations
    cloneable: https://webflow.com/made-in-webflow/website/mastering-gsap-split-text-animations
  - lesson: master-webflow-gsap-interactions
    cloneable: https://webflow.com/made-in-webflow/website/gsap-interactions-core-concepts
  - lesson: build-3d-cms-cubic-gallery-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-cubic-scrollable-image-gallery
assumes: [interactions-model, stagger]
related: [stagger, scroll-driven, interactions-model]
---

## The rule

Turn on **Split Text** on an action targeting a text element and choose the split unit:

| Split by | Result |
|---|---|
| **word** | Each word becomes a target |
| **line** | Each line becomes a target (behaves like *word* when every word is on its own line) |
| **letter** | Each character becomes a target |

That is the whole point of it: **one element becomes many targets**, which is the
precondition for [[stagger]] doing anything at all.

Set **mask** to match the split unit for a clean reveal. The mask wraps each piece in its
own clipping container, so the piece is invisible until it moves into view — paired with an
upward motion each word or letter looks like it slides out from behind a hidden edge. Far
cleaner than a bare fade, and it is the difference between "animated text" and a reveal that
looks designed.

**Use type `from`, not `to`.** Declare where the pieces start — `opacity: 0%`, `move Y:
100%` — and they land on their real designer state. Keep only the properties you need;
opacity and move-Y carry almost every text reveal in the corpus.

## How and why

**Nothing is broken into separate elements by hand.** The whole value is that an `h1` stays
one `h1` in the Designer and in the DOM you author. You do not maintain a span per letter,
and the text remains editable and translatable.

**Targeting a heading with no class.** Headings usually carry no class, so switch the
action's target to a **custom selector** and type `h1`. With correct SEO there is exactly
one per page, so "all matching elements" is safe. If a page has several headings of the same
level, give the one you're animating an attribute and target that instead — see
[[target-filters]].

**Letter-by-letter is a choice, not an upgrade.** Letters give a finer, more mechanical
texture and multiply the target count, so the same total time produces much tighter gaps.
Words read faster and stay legible while animating. For body copy or anything long, split by
word or line; reserve letters for short display headings.

**Two rows moving in opposite directions is the workhorse composition.** Split both, then
give row one a `from` of `100%` on Y so the words rise into view and row two a `to` of
`-100%` on Y so they slide out of sight, each with an offset-time stagger around `0.1s`.
That one pairing covers most hero reveals.

## Combining it with other systems

**Split Text staggers inside a scrubbed scroll timeline.** The pieces are ordinary targets,
so a scrub-on-scroll interaction can run a split-text stagger as scroll progresses rather
than on a clock — the CMS cube gallery does exactly this, cross-fading images while each
item's label reveals. See [[scroll-driven]].

**Split Text inside a looping interaction** needs the loop at the interaction level so every
action stays in sync; a horizontal split-text loop repeats the interaction, not the
individual actions. See [[timeline-control]].

## Verify

Publish before judging a split-text reveal. Masking and per-piece clipping are the parts most
likely to look different outside the Designer canvas, and a reveal that reads well at full
speed usually needs its total time exaggerated once — briefly — to check the ordering is what
you intended.
