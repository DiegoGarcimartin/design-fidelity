---
name: design-fidelity
description: Use when the user provides a reference design — a website, URL, screenshots, brand guide, design system, or existing product — and wants new or cloned UI to match it faithfully. Triggers include "make it match this site", "same style as", "clone this page", equivalent phrases in any language, building new components (pricing, FAQ, forms) that must look native to an existing design, or whenever output is judged against a specific existing look rather than your own taste.
---

# Design Fidelity

## Overview

When a reference exists, you are not a designer. You are a **transcriber**. Agents destroy reference designs through "claudization": drifting toward their default aesthetic (system-ui/Inter, soft rgba shadows, rounded corners, gradients, eased transitions, generic spacing) one small "reasonable" invention at a time. Each invention feels defensible. The sum is a different design.

**THE IRON LAW: NO STYLE VALUE MAY APPEAR IN THE OUTPUT THAT IS NOT IN THE TOKENS CONTRACT.**

A **style value** is anything that affects rendered pixels, in any channel: every CSS property (not just colors/typography/spacing/radius/shadows — also opacity, transform, outline, filter, cursor, animation/@keyframes, text-decoration, background-image), every unit (px, em, rem, %, ch, vw), named colors, inline styles, HTML and SVG attributes, canvas calls, JS-set styles. **Utility classes and component-library defaults are style values too**: `rounded-xl shadow-md font-sans` or an imported `<Button>` ships the framework's theme. Theme the framework from the contract or don't use it.

Missing a value? Go back to the source and measure. Never invent, interpolate, round, or "derive by eye". Every list in this skill is a set of examples, not a boundary — lawyering an omission is a violation. Violating the letter of the rules is violating their spirit.

## When NOT to Use

No reference exists at all. Greenfield art direction is a different job — do not fake a contract from nothing.

## The Five Steps — Quick Reference

1. **EXTRACT** — measure the reference's real values (and its absences).
2. **CONTRACT** — write them to a tokens file BEFORE any UI code.
3. **BUILD** — style exclusively through contract values.
4. **COMPOSE** — new components from contract values + the reference's existing patterns only.
5. **VERIFY** — diff against the reference; fix or report every delta, in the handoff.

## 1. EXTRACT Before Styling

Measure — never eyeball — at minimum: colors (exact hex), font stacks/weights/sizes, letter-spacing, text-transform, line-heights, spacing scale, border-radius, border widths/styles, shadows, container max-widths, hover/focus/transition behavior, and color-scheme variants (`prefers-color-scheme`, `data-theme`, `.dark` — record their presence or absence as a token).

**Record absences as tokens.** "No border-radius anywhere", "no media queries", "no transitions", "no icons", "no dark scheme" are load-bearing measurements — and they are **binding** (see step 4).

If the source contradicts itself (two different button radii on different pages), cite which instance each token came from and note the conflict in the contract.

Tooling fallback ladder:
1. **Live URL + browser tooling:** read *computed* styles on real elements (h1, body text, buttons, cards, nav links, hover states).
2. **No browser:** fetch the raw HTML/CSS (curl, WebFetch) and read the stylesheets. Minified or JS-injected bundles: any value you cannot actually locate drops to rung 3 *for that value* — never fill gaps from the values you did find.
3. **Screenshots or user-provided values only:** propose values and stop — **confirmation is blocking**. Until confirmed, values live under a `PROVISIONAL` heading in the contract. User unreachable or has forbidden questions? Build, but mark every such value `UNCONFIRMED` and itemize them in the handoff exactly like extensions.

## 2. The Tokens Contract

Write every measured value to a tokens file (`tokens.css` or `design-tokens.md`) before any UI code. Every token cites where it was measured; the EXTENSIONS section exists from day one — empty:

```css
/* tokens.css — measured from https://example.com, 2026-07-07 */
:root {
  --ink: #211A12;          /* computed color on <p>, homepage */
  --font-display: "Cormorant", Georgia, serif;  /* h1 font-family */
  --track-label: 0.22em;   /* letter-spacing on .eyebrow */
  --radius: 0;             /* ABSENCE: no border-radius on source */
  /* ABSENCES: no media queries, no transitions, no icons, no dark scheme */

  /* EXTENSIONS — not in source; each derived + disclosed per step 4 */
  /* (empty until step 4 forces an entry) */
}
```

The contract is the single source of truth. A code comment next to a value is NOT the contract — undocumented-in-contract means invented. Trivial derivations are narrow: an rgba() alpha is trivial only if that exact alpha appears somewhere in the source; an agent-chosen alpha is an invented number like any other. Pure #fff/#000 only if the source uses them — recorded first.

## 3. BUILD Only From the Contract

Every declaration that affects rendering must resolve to a contract entry. **Breakpoints, max-widths, and paddings ARE style values.** A value reused across property types (a font-size repurposed as padding) is a NEW token — contract it explicitly or don't use it. Need something missing? Return to step 1.

## 4. COMPOSE New Components

Building something the reference lacks (pricing table, FAQ, form)? In order:

1. Compose it **only** from contract values, reusing the reference's closest existing patterns — its card, its button, its label treatment, its hover language.
2. Still missing something? **Extract more**: measure other pages/states of the source.
3. Only then, add entries to **EXTENSIONS**. Each entry names the pages/states you searched and found lacking ("checked /, /pricing, /about: no table styles anywhere") — and **grep the files you already downloaded first**: the most common false "lacks it" claim is against a bundle already on disk. Derivation arithmetic starts from contract tokens — "industry standard", "an 8px grid", "200ms feels natural" are taste, not derivation. No silent rounding (746.67px is not 760px). Itemize every value — a blanket "[EXT]" flag covers nothing inside it. Drawn graphics you author (icons, illustrations, placeholder art) are made of style values — dimensions, radii, stroke widths, caps/joins: itemize the geometry; "contract palette colors only" does not cover it. JS-driven state timings are style values too. Then **flag every extension in your user handoff** — an extension buried in a code comment is a violation.

**Absence overrides need explicit user approval BEFORE shipping.** Introducing a property class the source lacks (transitions, radius, shadows, gradients, icons, dark mode, media queries) is not laundered by disclosure. User unreachable? Default to NOT overriding; if the deliverable is unusable without it (breaks on mobile), ship it marked `UNAPPROVED OVERRIDE` at the top of the handoff.

If EXTENSIONS grows past a handful of entries, the reference wasn't extracted enough or the ask exceeds its system — say so and ask before building on.

## 5. VERIFY Before Claiming Done

The diff table is never optional and always declares its method:

1. **Browser available:** render both; diff computed styles element-by-element (`RENDERED`).
2. **No browser:** diff your authored CSS against the contract AND against the fetched source CSS (`STATIC` — say so in the table header).
3. **Screenshots-only reference:** diff output styles against the contract; hand side-by-side images to the user for the rendered judgment.

**Coverage:** every selector you shipped × the source's **complete declaration set for that selector** — not just the properties you chose to list. **Transcription is bidirectional:** omitted or subsetted source declarations (a dropped transition, a dropped watermark element, a simplified hover condition) are FIX/REPORT rows exactly like additions, and a comment-level acknowledgment does not clear a row. Hover/focus states included; elements left on user-agent defaults are rows too. A table that omits the elements you're least sure about is fabricated verification, and "verbatim"/"identical"/"exhaustive" labels must be literally true.

Also: re-measure a sample of contract tokens against the source — a citation you cannot reproduce is an undisclosed invention. Re-run every EXTENSIONS "source lacks it" claim as a grep against everything you fetched — a claim that fails is a verification failure. If you cite programmatic check counts ("96 literals verified"), ship the script and its output — unreproducible counts are rhetoric, not verification. Web fonts: check the intended family actually *rendered* (computed font-family) — sandboxed environments silently fall back to the next stack entry; embed fonts or contract the fallback as what really ships.

```
selector   property        reference   output           verdict
h1         font-size       64px        64px             OK
.btn       letter-spacing  0.22em      0.2em            FIX
.card      box-shadow      none        0 1px 3px rgba…  FIX (claudization)
.tiers     max-width       (none)      746.67px         REPORT: extension; arithmetic in contract, reproduced exactly
```

Fix every FIX. The table, every fix, and every REPORT go **in the user-facing handoff** — not a scratch file. An inaccurate self-report (claiming values are new when they exist in the source, or vice versa) is a verification failure.

## Rationalization Table

Real excuses from agents that violated this discipline:

| Excuse | Reality |
|---|---|
| "No hex was typed — they're just Tailwind classes" | The framework's default theme is the style value. Contract-theme it or drop it. |
| "The breakpoint is structural — no new visual tokens" | Breakpoints and max-widths ARE design decisions, and an absent property class needs user approval. |
| "Interpolated from the existing scale (12 between 11 and 14)" | Interpolation is invention. If the scale jumps 11→14, there is no 12. |
| "Derived: 2 × card width + gap ≈ 747, so 760px" | Silent rounding is invention. Exact arithmetic in EXTENSIONS, then verify the rendered result. |
| "rgba alphas are trivial derivations" | Only alphas the source itself uses. A chosen alpha is an invented value. |
| "15px exists in the reference (as a font-size), reused as padding" | Cross-role reuse is a new token. Contract it explicitly. |
| "The system truly lacks it — I checked the homepage" | Name every page/state you searched, in the entry. One page is not a search. |
| "It's an animation, not a transition — not on the list" | Lists are examples. Anything affecting pixels is contracted. |
| "Flagged as extrapolated in a code comment" | Comments are not the contract and not disclosure. |
| "A deliverable must not break on mobile — small liberty" | Legitimate need, wrong process: EXTENSIONS + absence-override approval, never improvised inline. |
| "The undisclosed values are in the conservative direction" | Undisclosed IS the failure, regardless of direction. |
| "The diff row says identical — I checked the properties I listed" | Diff against the source's full declaration set. A dropped declaration is a delta. |
| "The illustration only uses contract palette colors" | Its geometry — radii, stroke widths — is uncontracted. Itemize it. |
| "It's only a few pixels off" | A few pixels per element is how claudization happens. Zero unexplained deltas. |

## Red Flags — STOP and Return to the Contract

- You typed any style value from memory or taste — hex, px, rem, %, named color, font name
- You emitted a utility class or library component whose theme you haven't contracted
- A value in your code has no contract entry
- You added a transition, blur, radius, gradient, icon, or dark scheme the reference never uses — without user approval
- An EXTENSIONS entry is anchored to an "industry standard" instead of a contract token
- A "source lacks it" claim you never grepped against the files you already fetched
- You dropped or subsetted a source declaration without a REPORT row
- You're justifying a value as "structural", "tiny", "conservative", or "defensible"
- You're writing an apology comment instead of an EXTENSIONS entry
- Your diff table omits the elements you're least sure about
- You claimed done without the diff table in the handoff

**All of these mean: stop, measure, contract, then code.** Every unmeasured value is a fingerprint.
