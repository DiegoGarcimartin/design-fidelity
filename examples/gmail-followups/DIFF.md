# Verify diff — "Follow-ups" panel vs. live Gmail

A complete worked example of the skill's five steps against a **logged-in product**: Gmail, measured through the user's own browser session (`getComputedStyle` on live elements, 2026-07-07, light theme). The task: prototype a "Follow-ups" panel — a feature Gmail doesn't have — that looks native to Gmail's system.

- [`tokens.css`](tokens.css) — the measured contract, every value cited, absences recorded, extensions itemized (E1–E6).
- [`index.html`](index.html) — the prototype; every declaration resolves to the contract.
- Fictional content throughout; concept prototype, not affiliated with Google.

**Method:** `RENDERED` for element styles (computed styles of the prototype vs. values measured on live Gmail); `STATIC` for hover/focus recipes (transcribed verbatim from Gmail's stylesheets — a `:hover` can't be computed without a pointer on it).

| selector | property | Gmail (live) | output | verdict |
|---|---|---|---|---|
| body | background / color / font / size | `#F8FAFD` / `#202124` / Google Sans stack / 16px | idem | OK |
| .card | background / radius / max-width | `#FFF` / 16px / 1200px | idem | OK |
| .card | overflow | `auto hidden` (.nH) | `hidden` | REPORT: E1 — no horizontal scroll content; render-equivalent |
| .btn-primary | bg / ink / type / height / radius / transition | `#C2E7FF` / `#001D35` / 14px·500·lh32 / 56px / 16px / box-shadow .08s + min-width .15s | idem | OK |
| .btn-primary | padding-left | 0 (icon slot in source) | 24px | REPORT: E4 — no icon; mirrors measured padding-right |
| .btn-primary:hover/:focus | shadow+bg / ring | `.z0>.L3:hover`, `.aOd.T-I:focus` rules | verbatim | STATIC |
| .row | padding-v / divider / hover | 10px / inset `rgba(100,121,143,.12)` / `.zA:hover` rule | idem / verbatim | OK · STATIC |
| .row | padding-h | (source pads via cell structure; cells 10px) | 24px | REPORT: E5 |
| .row.resolved | background | `#F2F6FC` (read row) | idem | OK |
| .who | size/weight/width | 14px · 700→400 · 168px (`.yX max-width`) | idem | OK |
| .st / .when | color / size | `#5F6368` / 12px, ink↔secondary | idem | OK |
| .chip idle / active | search-bar recipe / sidebar pill | `#E9EEF6`·13px·r24 / `#D3E3FD` | idem | OK (E3) |
| h1 | 16px + 700 | (combination exists on no measured element) | — | REPORT: E2 |
| iconography | row checkbox, star, avatars | present in source rows | **omitted** | REPORT: out of prototype scope, disclosed |
| web font | Google Sans rendered | Gmail loads it as a webfont | renders where installed; else falls to the contracted stack | REPORT |

**Notable moment from the run:** mid-build the agent typed `168px` for the sender column from taste, with an apology comment — the skill's red flags catch exactly that ("an apology comment instead of a measurement"). Re-measured against Gmail: the real column is `max-width: 168px`. The guess happened to be right; now it's measured and cited, which is the difference between luck and a system.
