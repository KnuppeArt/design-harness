# Skill: design-system-reader

## Purpose
Read and internalize DESIGN.md using the Brad Frost Subatomic 3-tier token architecture, so any subsequent design or specification work applies tokens correctly.

## When to load
Any command that needs to reference design tokens: `/gen-design-system` (to validate output), `/design-component`, `/spec-component`, `/design-view`, `/spec-view`.

## The 3-tier model

```
Tier 1 — PRIMITIVE: Raw values. The full palette. Never used directly.
  gold-500: "#B8960C"
  ink-900: "#1A1814"

Tier 2 — SEMANTIC: Roles in the UI. THIS is what agents consume by default.
  colors:
    text.primary: "{primitive.ink-900}"
    accent: "{primitive.gold-500}"

Tier 3 — COMPONENT: Component-specific overrides. Highest specificity.
  components:
    Button:
      primary-bg: "{colors.accent}"
```

**Rule: Use Tier 2 by default. Use Tier 3 only when a component has its own entry in DESIGN.md. Never use Tier 1 directly.**

## How to read DESIGN.md

1. Read the entire file before proceeding
2. Build an internal token map:
   - Index all Tier 1 primitives (name → hex value)
   - Index all Tier 2 semantic tokens (name → Tier 1 reference)
   - Index all Tier 3 component tokens (component.part → Tier 2 reference)
3. When a component needs a color, typography, or spacing value, look up the appropriate Tier 2 semantic token
4. Reference tokens using `{section.token-name}` syntax in specifications

## Token reference syntax in specs

When writing components.json or views.json:
- Color: `{colors.text.primary}`, `{colors.accent}`, `{colors.surface.primary}`
- Typography: `{typography.display.lg}`, `{typography.label.sm}`
- Spacing: `{spacing.4}`, `{spacing.8}`
- Radius: `{radius.md}`, `{radius.full}`
- Shadow: `{shadow.card}`, `{shadow.elevated}`

## What to flag

If DESIGN.md is missing tokens that a component clearly needs (e.g., an error state color with no `colors.error` defined), note the gap and ask the user before proceeding.

## Platform-specific considerations

DESIGN.md is platform-agnostic. When specifying components for a specific platform (Flutter, React, etc.), coding agents translate token names to platform idioms. Do not embed platform-specific syntax in specs.
