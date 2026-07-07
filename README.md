# design-fidelity

A skill that stops AI coding agents from imposing their default aesthetic when you hand them a reference design.

Agents drift toward their own look — Inter/system-ui, soft shadows, rounded corners, generic spacing — one small "reasonable" invention at a time, even with the reference in front of them. This skill replaces taste with measurement: **no style value ships that wasn't measured from the source or explicitly disclosed as an extension.**

## Install

**Claude Code:**

```bash
mkdir -p ~/.claude/skills/design-fidelity
curl -o ~/.claude/skills/design-fidelity/SKILL.md \
  https://raw.githubusercontent.com/DiegoGarcimartin/design-fidelity/main/SKILL.md
```

Or clone this repo and copy `SKILL.md` into `~/.claude/skills/design-fidelity/`. For other agents that support the [Agent Skills](https://agentskills.io) format (Codex: `~/.agents/skills/`), same file, same layout.

## How it works

1. **EXTRACT** — measure the reference's real values (exact hex, font stacks, spacing, shadows… and its *absences*: "no radius", "no transitions").
2. **CONTRACT** — write them to a tokens file, every value citing where it was measured, *before* any UI code.
3. **BUILD** — no style value outside the contract. In any channel: CSS, utility classes, component-library themes, SVG, canvas.
4. **COMPOSE** — new components (pricing, FAQ, forms) reuse the reference's existing patterns; anything genuinely new is an itemized, disclosed extension.
5. **VERIFY** — a style diff table against the reference, element by element, delivered in the handoff.

## How to check your agent complied

You don't need to read CSS. Ask for two artifacts the skill forces the agent to produce:

- **The tokens contract** (`tokens.css`) — every value must cite where it was measured. A value without a citation is a fingerprint.
- **The diff table** — reference vs. output, with OK / FIX / REPORT verdicts and its method declared.

If either is missing, the agent didn't follow the skill.

## How it was built

Test-driven, adversarially: baseline runs measuring how agents drift without the skill, forensic audits classifying every output value as measured/derived/invented, temptation scenarios where the client *invites* the agent to deviate ("improve it if you see fit"), red-team passes hunting textual loopholes (framework default themes, invented rgba alphas, unverifiable citations), and final exams against live production sites.

## Author

Diego Garcimartín Rey — diego.garcimartin@gmail.com

## License

[MIT](LICENSE)
