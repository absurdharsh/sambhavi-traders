---
type: concept
title: Target filters — resolving a target relative to the trigger
summary: >
  An action's target is not one setting but three composed into a sentence — what to target,
  a filter, and the element the filter is measured against — so "the sidebar that is the
  previous sibling of the trigger" is Previous sibling of + Trigger element, not a single
  menu option; this is what lets one interaction serve every card in a CMS collection.
level: advanced
tags: [gsap, interactions, targeting, attributes, cms, reusability, webflow]
status: verified
generated: 2026-07-27
verified_against: designer
sources:
  - lesson: master-accessible-cms-sidebars-webflow
    cloneable: https://webflow.com/made-in-webflow/website/cms-jquery-detailed-info-sidebar
  - lesson: how-to-build-infinite-looping-marquees-webflow
    cloneable: https://webflow.com/made-in-webflow/website/060-gsap-infinite-marquee
  - lesson: growing-border-animations-webflow
    cloneable: https://webflow.com/made-in-webflow/website/gsap-growing-border-animation
assumes: [interactions-model]
related: [interactions-model, timeline-control]
---

## The rule

**One interaction, many instances.** If an action targets a class, every element with that
class animates — a click on card 3 opens all twelve sidebars. Targeting by class is wrong for
any repeated component.

Under **Target settings** an action's target is **three dropdowns composed into a sentence**,
read left to right as `<target> <filter> <reference>`:

| Control | Answers | Options |
|---|---|---|
| **Target** | What to look for | `Trigger element` · `Element` · `Class` · `Attribute` · `Custom selector` · `Any element` |
| **Filter** | How the target must relate to the reference | `None` · `Within` · `Direct child of` · `Contains` · `Direct parent of` · `Next to` · `Next sibling of` · `Previous sibling of` |
| **Reference** | What the filter is measured against | `Trigger element` · `Parent of trigger` · `Element` · `Class` · `Attribute` · `Custom selector` |

Plus a **First match only** checkbox at the bottom.

**The filter narrows the target set by the target's own relationship to the reference.** So a
chip reading `.hero-section within Trigger element` means: *find elements with class
`hero-section`, keep only those nested inside the trigger.*

That composition is the whole concept. There is no single "previous sibling of trigger"
option — you build it.

## The compositions you actually need

| You want… | Target | Filter | Reference |
|---|---|---|---|
| The panel sitting just before the button that was clicked | `Attribute` | **Previous sibling of** | `Trigger element` |
| The wrapper the trigger lives directly inside | `Attribute` | **Direct parent of** | `Trigger element` |
| The panel that wraps whichever close control fired | `Attribute` | **Contains** | `Trigger element` |
| Anything inside the collection item the trigger belongs to | `Attribute` | **Within** | `Parent of trigger` |
| Every direct child of a marked wrapper | `Any element` | **Direct child of** | `Attribute` |

The pattern around them never changes:

1. Mark the elements with a **custom attribute** (`data-animate`, `data-sidebar-open`,
   `data-sidebar-close`, `data-animate-link-border`), not a class.
2. Set **Target** to `Attribute` (or `Any element` when the attribute belongs on the
   *reference* instead).
3. Add the **Filter** + **Reference** pair that describes the structure.

Class names then stop mattering: duplicate the component and it works.

## How and why

**Pick by reading the DOM, not by trial and error.** The filter is a statement about
structure. If it doesn't resolve, the structure is not what you think it is — go look at the
navigator before you try another option.

**Two axes, not one, is why this covers everything.** `Within` + `Trigger element` and
`Within` + `Parent of trigger` are different queries, and the second one is the one that
makes CMS components work: it reaches *sideways* into the shared collection item rather than
downward from the button. Most people never open the third dropdown and conclude the feature
is missing.

**The CMS case is the hard one, and it drives the layout.** Put one panel *inside* each
collection item, so every card renders its own sidebar bound to that item's fields. There is
no shared panel to wire up and no id-matching script. Then one interaction serves all of them:
the sidebar is `Previous sibling of` → `Trigger element` relative to the open button; the
overlay and content are `Within` → `Parent of trigger`; and because the close control lives
inside the panel, the panel is `Contains` → `Trigger element` relative to it. Same
interaction, correct target every time.

**`First match only` is a real setting and it matters.** A marquee wrapper carrying
`data-animate="horizontal-marquee"` has **two** trail groups as direct children and both must
move, so leave it unchecked. Check it when the relationship should resolve to exactly one
element.

**Hover on a repeated element needs the parent composition.** A growing-border link where each
instance must animate only *its own* border: a `set` action toggles an `on-hover` class,
target `Attribute` = `data-animate-link-border`, filter `Direct parent of` → `Trigger
element`. Without it, every link's border fills at once.

## The reusability test

Duplicate the component and publish. If the second copy animates on its own, correctly, with
no interaction edits, the targeting is right. If duplicating means touching the interaction,
you are still targeting by class somewhere.

## Small unverified detail

The Filter menu groups its options with separators — descendant (`Within`, `Direct child of`),
ancestor (`Contains`, `Direct parent of`), sibling (`Next to`, `Next sibling of`, `Previous
sibling of`). **`Next to` versus `Next sibling of` is the one distinction the source corpus
never exercises**, so their exact difference is not established here. Neither appears in any
of the three cloneables. If a reader needs a forward sibling relationship, tell them both
exist and to check which matches their DOM.

## Related

Attribute-driven targeting is what makes a single interaction serve a whole CMS collection;
the recipes that depend on it are [[accessible-dialog]] and [[infinite-marquee]]. For the
events that drive those targets, see [[timeline-control]].
