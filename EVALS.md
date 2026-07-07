# How this skill was tested

Method: TDD applied to process documentation ([superpowers/writing-skills](https://github.com/obra/superpowers)). Every rule in SKILL.md exists because an agent was observed violating it. All runs used multi-agent Claude Code sessions (builder agents + independent forensic audit agents + red-team agents), July 2026. Audit transcripts are summarized here, not published raw.

## Round 1 — baseline (RED)

4 builder agents, no skill, given clean single-file reference sites and asked for new components. Average fidelity as scored by forensic auditors: **9.3/10** — but with a consistent tail of silent inventions: undisclosed breakpoints (900px, 760px), values interpolated into gaps of the type scale (a 12px in an 11→14 scale), silent rounding (746.67px shipped as 760px), extensions "flagged" only in code comments. The skill was written to kill exactly that tail; the auditors' verbatim rationalizations became its rationalization table.

## Round 2 — red team

2 adversarial agents attacked the skill text for loopholes. Verdicts: "ship-after-fixes". **17 exploits found and patched**, including: Tailwind/component-library default themes shipping untyped style values; inventing an entire tint system via "trivial" rgba alphas; closed property lists (opacity/transform/animation escaping the rule); an unfalsifiable verify step when no browser is available; recorded absences laundered by disclosure; dark mode unaddressed; invalid CSS in the skill's own template.

## Round 3 — live production sites (with skill v2)

Same forensic-audit protocol, real sites fetched headless (no browser), including sites behind bot protection:

| Exam | Fidelity | Generic-AI markers | Notes from independent auditor |
|---|---|---|---|
| Linear — new "customer stories" section | 9.5/10 | 0 | Global stylesheet adopted byte-exact (sha256 verified); every spot-checked citation reproduced |
| Zara — new capsule landing (behind Akamai) | 8.5/10 | 0 | Passed the proof-of-work honestly; dinged for two false "source lacks it" claims against an already-downloaded bundle |
| Teenage Engineering — new product page | 9/10 | 0 | ~100 literals verified digit-for-digit; refused to copy TE's hand-drawn SVG artwork on IP grounds, routed through disclosed extensions |
| Linear — temptation trap: *"give it a more modern feel if you think it looks better — you're the designer"* | 9/10 | 0 | Declined to invent: routed "modern" to disclosed content composition; 49 stylesheets byte-identical to CDN |

Every weakness those audits found (omission-type deltas passing as "identical", under-searching already-fetched bundles, authored-artwork geometry escaping itemization) was patched into skill v3 — the version in this repo.

## Round 4 — logged-in product

A worked end-to-end example against live Gmail through a real browser session — measured contract, prototype composed only from it, rendered verification: [examples/gmail-followups/](examples/gmail-followups/).

## Reproducing

The eval harness is straightforward to rebuild: give a builder agent a reference and a task with the skill loaded; give an independent auditor the reference, the output, and the builder's handoff, and have it classify every style value as measured / contracted / invented, verify citations against the source, and score. The skill's own artifacts (cited tokens file + diff table) are what make the audit mechanical.
