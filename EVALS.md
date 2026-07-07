# How this skill was tested

Method: TDD applied to process documentation ([superpowers/writing-skills](https://github.com/obra/superpowers)). Every rule in SKILL.md exists because an agent was observed violating it. All runs used multi-agent Claude Code sessions (builder agents + independent forensic audit agents + red-team agents), July 2026. Audit transcripts are summarized here, not published raw.

**Model disclosure:** builder agents in rounds 1–4 ran on Claude Fable 5 (frontier). Round 5 tests non-frontier models explicitly — read it before assuming these numbers transfer to your model.

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

## Round 5 — non-frontier models & screenshot-only input (skill v3.1)

Same forensic protocol; fidelity and process compliance scored separately, because a weaker model can produce faithful pixels while confabulating its own paperwork:

| Exam | Builder model | Fidelity | Compliance | What breaks first |
|---|---|---|---|---|
| File reference, new pricing section | Sonnet | 9.5/10 | 8.5/10 | Verification layer: diff-table coverage drops selectors; RENDERED claimed without shipping evidence |
| File reference, testimonials + FAQ | Haiku | 9/10 | 6/10 | Self-audit confabulates: 4 fabricated token citations survived a claimed "12/12, 100%" spot-check; method falsely declared RENDERED; EXTENSIONS orphaned from the contract. Pixel fidelity still held: byte-identical copies, zero generic-AI markers |
| **Screenshot-only** reference, membership page | Sonnet | 8/10 | 8/10 | All 6 pixel-sampled colors exactly right (auditor re-sampled the cited coordinates); breakpoint correctly shipped as UNAPPROVED OVERRIDE. Keyword/unitless values (font-weight, cursor, width:100%) slipped the contract; marking applied only to "uncertain" values instead of everything pixel-derived |

Takeaway for users: the skill's mechanical guardrails (measure → contract → disclose) hold down to Haiku-class models, but **treat a smaller model's verification claims as unverified** — re-run the diff yourself, or run the VERIFY step on a stronger model. Skill v3.2 (this repo) hardens each crack found here: keyword/unitless values named in the Iron Law, mark-everything-pixel-derived in rung 3, extensions must live in the contract *and* the handoff, RENDERED without shipped evidence is fabrication, handoff counts must reconcile with shipped tables.

## Reproducing

The eval harness is straightforward to rebuild: give a builder agent a reference and a task with the skill loaded; give an independent auditor the reference, the output, and the builder's handoff, and have it classify every style value as measured / contracted / invented, verify citations against the source, and score. The skill's own artifacts (cited tokens file + diff table) are what make the audit mechanical.
