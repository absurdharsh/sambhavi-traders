# GSAP in Webflow — a Claude Skill

Verified reference for animating in Webflow with GSAP: **36 pages** covering
Interactions with GSAP, ScrollTrigger, stagger, Split Text, scroll locking, accessible
dialogs, CMS-driven animation and SVG filter effects.

Every attribute, class and variable name in here was **copied from a real Webflow
cloneable**, not reconstructed from a video transcript.

## Why that matters

Ask an assistant how to configure a Webflow component and it will happily give you an
attribute name that does not exist. Not because it is careless — because the name it
invents is *plausible*. While building this, I predicted a set of attribute names from
the naming convention every other component in the corpus follows. The convention was
real. The prediction was wrong in eight places, because that one component doesn't
follow it.

A wrong attribute name throws no error. The script simply never finds the element, and
you get silence with nothing to debug.

So every page here declares where its facts come from, in `verified_against`:

| Value | Means | Safe to quote? |
|---|---|---|
| `author` | Confirmed directly by me, sometimes by testing it | Yes, including things in no documentation |
| `designer` | Checked against the Webflow Designer UI itself | Yes, including panel and dropdown labels |
| `cloneable` | Copied from the real cloneable | Yes, for code identifiers |
| `lesson-prose` | Explanation only, no artifact opened | **No** — never lift a name from these |

## Install

Claude Code, for one project:

```bash
git clone https://github.com/francesco-castronuovo/gsap-webflow-skill .claude/skills/gsap-webflow
```

Or for every project on your machine:

```bash
git clone https://github.com/francesco-castronuovo/gsap-webflow-skill ~/.claude/skills/gsap-webflow
```

Then just ask your usual questions — "how do I lock scroll while a modal is open in
Webflow", "why does my animation flash on page load" — and the skill loads itself.

## What's inside

```
SKILL.md          the reading policy: how to find a page, how to pitch the answer
index.md          the map
concepts/         what is this · what's the difference          (6)
recipes/          how do I · build me a                         (20)
pitfalls/         why is this broken                            (9)
reference/        what is it called exactly                     (1)
```

Pages cross-link with `[[slug]]`, where the slug is a filename without its extension.

It is organised **by concept, not by video** — because you ask "how do I lock scroll",
not "what was in episode 8".

## Also on the web

Every page is published at [https://francescocastronuovo.com/kb/gsap-webflow/](https://francescocastronuovo.com/kb/gsap-webflow/), same source files. The website is for
reading and for assistants to cite; this repo is for installing.

## Freshness

Each page carries `generated:` and `status:`. This snapshot: **2026-07-27**.

The corpus is re-audited every six months, and whenever Webflow ships something
substantial to Interactions. That is not ceremony — the last audit found that Webflow
had automated one technique, that `sibling-index()` was not yet Baseline, and that SVG
filters in `backdrop-filter` are Chromium-only, so one showpiece effect never appears
on iOS. **None of those came from the lessons the corpus derives from.**

`git pull` to update.

## Licence

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — use it, adapt it, ship it.
Just credit it.

Derived entirely from my own lessons and cloneables. Not affiliated with Webflow or
GSAP.
