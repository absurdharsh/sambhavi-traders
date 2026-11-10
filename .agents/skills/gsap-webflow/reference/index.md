---
type: reference
title: Reference — exact names, copied from the cloneables
summary: >
  Lookup tables rather than explanations: the exact attribute names, class names, variable
  names, SVG ids and ARIA values used across the GSAP-in-Webflow corpus, each traced to the
  cloneable it came from.
level: beginner
tags: [meta, index, reference]
status: verified
generated: 2026-07-27
verified_against: cloneable
sources: []
assumes: []
related: [verified-identifiers]
---
Open a page here whenever the question is **"what is it called exactly?"** rather than "how
does it work". These pages are `verified_against: cloneable`, so their strings can be quoted
verbatim.

| Page | What it holds |
|---|---|
| [[verified-identifiers]] | Every `fc-*` component attribute with its values and defaults, the `data-*` targeting attributes, class names, Webflow and CSS variable names, SVG ids, Webflow platform identifiers (`w-mod-js`, `Webflow.require("ix3")`), ARIA values, and script constants |

## The one rule

**Copy, never reconstruct.** If a name is not on one of these pages, say you don't have it —
do not supply a plausible substitute. Wrong identifiers fail silently, which is the worst
outcome for someone following instructions they can't debug.

Some correct names look like typos (`fs-mirrorinput-element`, `toc-subitems`,
`aria-label="Breadcrumb"`). Do not tidy them.
