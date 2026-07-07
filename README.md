# design-fidelity

A skill that stops AI coding agents from imposing their default aesthetic when you hand them a reference design.

Agents drift toward their own look — Inter/system-ui, soft shadows, rounded corners, generic spacing — one small "reasonable" invention at a time, even with the reference in front of them. This skill replaces taste with measurement: **no style value ships that wasn't measured from the source or explicitly disclosed as an extension.**

The defensible claim, stated narrowly: this makes an agent **faithful to an existing reference** — a public site, a brand guide, or a logged-in product it can measure through your browser session (something canvas tools can't even see). It is not a hosting platform, a canvas editor, or a Figma importer; see [Scope](#scope-what-this-is-and-isnt).

## Requires

- [Claude Code](https://claude.com/claude-code) (terminal app or desktop). A *skill* is a markdown file Claude Code reads automatically when a task matches it — the install below just puts the file where Claude looks. Other agents supporting the [Agent Skills](https://agentskills.io) format work too (Codex: `~/.agents/skills/`).

## Install

```bash
mkdir -p ~/.claude/skills/design-fidelity
curl -o ~/.claude/skills/design-fidelity/SKILL.md \
  https://raw.githubusercontent.com/DiegoGarcimartin/design-fidelity/main/SKILL.md
```

(The file is [MIT-licensed](LICENSE); the license grant travels with this repo.)

**Verify the install:** start Claude Code and ask *"do you have the design-fidelity skill?"* — or check the file exists at `~/.claude/skills/design-fidelity/SKILL.md`.

## Usage

You don't invoke anything. Mention a reference design and the skill triggers on its own:

> Here's my reference: https://linear.app — build me a pricing page that could ship as part of that site.

> Our product is this (I'm logged in, in Chrome): measure its design system and prototype a "Follow-ups" panel that looks native.

Works with a URL, a live logged-in product, screenshots, or a brand guide — the skill has a measurement ladder for each, and values it cannot measure must be confirmed with you, never guessed.

## How it works

1. **EXTRACT** — measure the reference's real values (exact hex, font stacks, spacing, shadows… and its *absences*: "no radius", "no transitions").
2. **CONTRACT** — write them to a tokens file, every value citing where it was measured, *before* any UI code.
3. **BUILD** — no style value outside the contract. In any channel: CSS, utility classes, component-library themes, SVG, canvas.
4. **COMPOSE** — new components (pricing, FAQ, forms) reuse the reference's existing patterns; anything genuinely new is an itemized, disclosed extension.
5. **VERIFY** — a style diff table against the reference, element by element, delivered in the handoff.

## How to check your agent complied

You don't need to read CSS. Ask for two artifacts the skill forces the agent to produce — both appear in your project folder and in the agent's final handoff message:

**The tokens contract** (`tokens.css`) — every value cites where it was measured. From a [real run against live Gmail](examples/gmail-followups/):

```css
--btn-primary-bg: rgb(194, 231, 255);  /* [gh=cm] compose backgroundColor */
--row-divider: rgba(100, 121, 143, 0.12); /* tr.zA boxShadow: 0 -1px 0 0 inset */
--col-sender: 168px;              /* .zA .yX max-width computed; rendered 168.0px */
```

**The diff table** — reference vs. output with OK / FIX / REPORT verdicts and its method declared (`RENDERED` or `STATIC`):

```
selector      property        Gmail (live)         output    verdict
.card         border-radius   16px                 16px      OK
.btn-primary  background      rgb(194,231,255)     idem      OK
.card         overflow        auto hidden          hidden    REPORT: E1, disclosed
iconography   (rows)          checkbox/star/avatar omitted   REPORT: out of scope
```

A value without a citation, or a missing table, means the agent didn't follow the skill.

## Evidence

Developed test-driven against real failures, then examined against live production sites (Linear, Zara, Teenage Engineering, plus a "improve it if you see fit" temptation trap): average fidelity 9/10 as scored by independent forensic audit agents, **zero generic-AI styling markers** in the final round, and every remaining loophole they found patched into the skill text. Numbers, method, and a complete worked example: [EVALS.md](EVALS.md) and [examples/gmail-followups/](examples/gmail-followups/).

These evals were run by the author using multi-agent Claude Code sessions; audit transcripts are summarized, not published raw.

## Scope: what this is and isn't

**This is** a fidelity discipline: measured extraction, a token contract with provenance, composition inside an existing system, and auditable verification — including against logged-in products via your own browser session.

**This isn't** what Lovable or Framer are: no hosting, no deploy button, no direct-manipulation canvas, no native Figma import, and it front-loads measurement, so time-to-first-render is slower. If your job is "spin up something pretty fast with no reference", those tools (or plain Claude) are the right hammer. If your job is "make it look like *ours*", this is.

## Cost & limits

- The discipline deliberately makes the agent slower and more token-hungry (measurement + verification passes).
- Screenshot-only references block on your confirmation of proposed values — that's a feature.
- Greenfield art direction (no reference at all) is explicitly out of scope.
- Use it on your own products, references you're authorized to work from, or as private prototyping. Cloning a third party's trade dress for production is your legal call, not the skill's.

## Author

Diego Garcimartín Rey — diego.garcimartin@gmail.com

## License

[MIT](LICENSE)
