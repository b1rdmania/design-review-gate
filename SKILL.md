---
name: design-review-gate
description: Scored 0–100 design review gate for UI/frontend code — tells you exactly why it "looks AI-generated" and how to fix it, ordered by score gain. Use when a React/Tailwind/HTML interface looks off, generic, or unfinished; when the user asks for a "design score", "design gate", "does this look AI-generated", or to make UI look "designed, not generated"; and as a self-gate right after generating any UI, before showing the user. Self-contained; cherry-picked from the open-source StyleSeed design engine (MIT), with Andy's house rules layered on top.
license: MIT (https://github.com/bitjaru/styleseed/blob/main/LICENSE)
---

# Design Review Gate

## Overview

A UI reads as "AI-generated" not because the components are ugly, but because the **parts
don't agree with each other** — mixed corner radii, three accent colors, pure-black text,
no hierarchy, missing states, robotic copy. This skill reviews a UI file (or a whole
directory) against a concrete design rubric, scores it 0–100, and returns a prioritized
fix list. It reviews and recommends; it never edits or deletes without being asked.

## House rules (Andy overrides — these beat the rubric on conflict)

Andy's register is minimal/Swiss: type + grid + whitespace + restraint, one discordant note
(see memory `andy-design-taste`). Two rubric adjustments follow:

- **Cardlessness is a valid choice, not a violation.** The Layout deduction "content on a
  bare page background, not in cards (−6)" applies only when the design language is a
  card-based one executed lazily. A deliberate hairline-ruled index — flush-left grid, big
  margins, rules instead of boxes — scores FULL marks. What you deduct for instead: rules
  and margins applied inconsistently (that's a Coherence violation, same −6).
- **One discordant gesture is allowed and encouraged** (a single off-grid color bar, one
  oversized number). Don't deduct it under Coherence — deduct only a *second* discordant
  gesture (restraint everywhere else is the rule that makes the first one work).

Everything else in the rubric stands as written.

## When to use

- A React / Tailwind / HTML UI "looks off," generic, or unfinished and you can't say why.
- You want a design score / pre-ship check.
- The user asks to make UI "look professional / polished / designed, not AI-generated."
- After generating UI, to verify it before shipping.

## How to review

Read the file(s). Score these **seven categories** (total 100); start each at full marks
and subtract for violations you can cite by line. Be specific and evidence-based.

### 1. Coherence — 20  (the #1 "AI-generated" tell)
One choice per axis, applied everywhere. Deduct for each **mixed** axis:
- mixed corner radii — e.g. a sharp card with pill buttons (−6)
- two or more accent colors used for emphasis (−5)
- **emoji used as UI icons** (🚗🧺⭐ as list/nav/status/category markers) — injects many uncontrolled hues; use one line-icon set in currentColor (−6)
- mixed shadow languages / light directions (−3)
- mixed icon families, fill modes, or stroke weights (−3)
- inconsistent control heights (buttons/inputs differ) (−3)

### 2. Color discipline — 16
- pure black (`#000` / `text-black`) text — the refined black is ~`#2A2A2A` (−4 each, cap −8)
- hardcoded hex where a semantic token exists (−2 each, cap −6)
- **a normal / OK / default state shown in a status color** instead of neutral grey (−4)
- **status color on most/every row** (no severity hierarchy — color should mark the minority that needs attention) (−4)
- **decorative hues** — gold stars, rainbow category dots, a different color per card — instead of accent/grey (−3)
- status conveyed by color alone, no icon/text (−4)
- contrast below WCAG AA (4.5:1 body, 3:1 large/UI) (−6)

### 3. Hierarchy & typography — 16
- number and its unit not ~2:1 (48px number / 24px unit) (−4)
- everything the same size and weight, no clear primary (−5)
- arbitrary font sizes; no scale (−4)
- wrong line-height (loose on display, cramped on body) (−3)

### 4. Layout & spacing — 12
- content structure absent — no cards AND no rules/grid discipline (see house rules) (−6)
- off-grid spacing (7/13/19px instead of an 8px scale) (−3)
- the gap *around* a group not larger than the gap *inside* it (−3)
- the same section type repeated in a row (−4)

### 5. States — 12
- missing empty / loading / error state on a data surface (−5 each, cap −10)
- empty state with no next action; error that blames instead of helping (−4)
- dead link — a button/anchor pointing at `#` with no action and no visible disabled styling (−3)
- current page/section not indicated in nav (−2)

### 6. UX writing — 12
- buttons that don't name the action ("Submit" / "OK" instead of "Send $2,400") (−4)
- error copy that blames or uses system-speak ("Invalid input", "An error occurred") (−4)
- two terms for one concept (delete vs remove); filler words ("please", "successfully") (−2)
- Lorem Ipsum, placeholder brand names ("Acme Corp", "SmartFlow"), or round fake numbers ("99.99%", "$100.00") instead of realistic content (−3)
- AI-cliché copy ("Elevate", "Seamless", "Unleash", "Next Gen", "Delve") (−2)
- Title Case Headers instead of sentence case (−2)
- exclamation marks in success messages, or "Oops!" in error copy, instead of a direct statement (−2)

### 7. Motion & polish — 12
- ad-hoc fades instead of one consistent, named feel (−3)
- motion that delays content or blocks an action (−4)
- no `prefers-reduced-motion` handling on custom motion (−3)
- a single hard black shadow instead of a layered, low-opacity, tinted one (−2)

Clamp each category at 0; sum to a total. Bands: 90+ A · 80–89 B · 70–79 C · 60–69 D · <60 F.

## Output format

```
## Design Score: 72 / 100   (src/Dashboard.tsx)   C

Coherence            13/20   sharp cards (l.22) + pill buttons (l.48); 3 accent hues
Color discipline     12/16   #000 headings (l.12, 40)
Hierarchy & type     15/16   number/unit 1:1 on hero (l.18)
Layout & spacing     10/12   two identical KPI rows (l.22-31)
States                7/12   no empty/loading state on the orders list
UX writing            8/12   "Submit" button (l.55); "Invalid input" (l.61)
Motion & polish      10/12   one hard black shadow (l.22)

### Fix first (highest score gain)
1. Unify radius (pick soft 8–12px) + collapse to one accent   → +11 coherence/color
2. Add empty + loading states to the orders list              → +7  states
3. Rename "Submit" → "Send $2,400"; "Invalid input" → "Check the card number" → +6 copy

Re-score after: ~90 / 100.
```

## Ship checklist (pass/fail, not scored)

Separate from the 100-point rubric — these are completeness gaps, not design-quality ones.
Run through this list once the score clears ~80. Flag anything missing; don't deduct points for it.

- Favicon, `<title>`, meta description, `og:image`, and social sharing tags present
- Alt text on every meaningful image
- Semantic HTML (`<nav>`, `<main>`, `<article>`, `<aside>`, `<section>`) instead of div soup
- Skip-to-content link for keyboard users
- Client-side form validation (email format, required fields)
- Custom, branded 404 — not the framework default
- Legal footer links (privacy, terms) where the site needs them

## Rules

- Review from real evidence (cite line numbers); never guess.
- Order the fix list by **score gain**, not severity alone — fastest path to a better number.
- For a directory: one-line score per file, then the lowest file's full breakdown.
- **Don't auto-edit.** This skill measures and recommends. Apply fixes only when asked.
- Use it as a **quality gate**: review right after generating UI, apply the fix list, and
  re-review until the score clears ~80 *before showing the user* — no first-draft, incoherent
  UI (rainbow status lists, emoji icons, two accents, missing states) should reach them. The
  bar is a floor, not a ceiling: clear 80 and ship; don't chase 100 to delay.
- **Pixel gate on top (when a renderer is available):** the code score is necessary but not
  sufficient. If you can render (headless Chrome screenshot, playwright MCP, or a running dev
  server), screenshot the surface, actually look at the image, and fix what the pixels show.
  Never claim visual verification without having seen a screenshot; if you can't render, say
  so — a code-only review is disclosed as such.

---

Cherry-picked 2026-07-26 from **StyleSeed** (https://github.com/bitjaru/styleseed, MIT) after
a full quality + security audit; rubric verbatim except the house-rules layer and the pixel-gate
rule (the latter distilled from StyleSeed's ss-verify). The full engine (74 rules, skins,
20 skills) was deliberately NOT installed — heavy token weight, an ungated update channel,
and doctrine overlap with the installed design stack.

Content-realism, dead-link/nav, and ship-checklist items added 2026-07-30 from **elayadesign/ai-design-skills**
`landing-page-design` SKILL.md (MIT), after a skill-auditor pass. Only the content-realism (B8),
states (B9), and ship-requirements (B10) items were taken — the visual system (Tailwind-locked
type/spacing tokens, gradient hero text, glass-morphism island nav, mandatory tagline reveal)
was left out as it conflicts with Andy's minimal/Swiss house rules above.
