# Design Harness — Project Rules

This project uses the Design Harness pipeline. Claude Code agents operating in this project must follow these rules.

---

## SSOT — The Three Files

The source of truth for this project's UI is three files:

| File | Owner | Contains |
|------|-------|---------|
| `DESIGN.md` | @ui-architect | Design tokens (3-tier: primitive → semantic → component) |
| `components.json` | @ui-architect | Component registry (anatomy, variants, states, behavior) |
| `views.json` | @ui-composer | View layouts (sections, positions, spacing, breakpoints) |

**Rule: If it's not in these three files, it doesn't exist. Do not improvise.**

---

## Token rules

- Use Tier 2 semantic tokens by default (`{colors.accent}`, `{typography.display.lg}`)
- Use Tier 3 component tokens when a component has its own DESIGN.md entry
- Never use Tier 1 primitives or raw hex values in components or views
- Never hardcode spacing values — always use spacing tokens

---

## Agent responsibilities

- **@ui-architect** — Generates and maintains `DESIGN.md` + `components.json`. Does NOT touch `views.json` or write implementation code.
- **@ui-composer** — Generates and maintains `views.json`. Does NOT modify `components.json` or `DESIGN.md`.
- **Coding agents** — Read all three SSOT files and implement. Do NOT modify SSOT files during implementation. If the spec is wrong, report it and update the spec first.

---

## Validation commands

```bash
# Validate DESIGN.md
npx @google/design.md lint DESIGN.md

# Validate components.json
npx ajv validate -s schemas/components-registry.v1.schema.json -d components.json

# Validate views.json
npx ajv validate -s schemas/views-registry.v1.schema.json -d views.json
```

All three must pass before implementation begins.

---

## Atomic design rules

Components are built in strict order: **atoms first, then molecules, then organisms**.
Molecules may only use approved atoms. Organisms may only use approved molecules.
No layer advances without human approval.

---

## File structure

```
.claude/
  agents/      ← @ui-architect, @ui-composer
  skills/      ← Load on demand (progressive disclosure)
  commands/    ← Pipeline commands
schemas/       ← JSON validation schemas
docs/          ← Project docs (brief.md, prd.md, ux-spec.md, domain-rules.md)
DESIGN.md      ← Token system (Google format)
components.json ← Component registry
views.json     ← View layout registry
```

---

## What NOT to do

- Do not write platform-specific code based on assumptions — implement what the SSOT specifies
- Do not create components not in `components.json`
- Do not create views with layout not in `views.json`
- Do not use colors, fonts, or spacing not in `DESIGN.md`
- Do not make UX decisions — if something is missing from the spec, ask
