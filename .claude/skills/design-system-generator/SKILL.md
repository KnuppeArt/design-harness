# Skill: design-system-generator

## Purpose
Generate a complete, valid DESIGN.md file from a project brief, following Google's DESIGN.md format extended with Brad Frost Subatomic 3-tier token architecture.

## When to load
Only when `/gen-design-system` is invoked.

## Prerequisites
- Project brief available (resolved via `project-context-resolver`)
- `design-system-reader` loaded (to understand the output format)

## Process

### 1. Extract design direction from brief
From the brief, extract:
- **Aesthetic intent** — What feeling should the UI convey? (e.g., "Eternal Clean — antiquity that feels contemporary")
- **Color direction** — Any mentioned colors, moods, or references
- **Typography intent** — Display font personality, UI font needs
- **Platform** — Mobile (Flutter/RN/iOS/Android), web, desktop, cross-platform
- **Brand colors** — Any hex values or color names explicitly specified

If any of these are missing and critical, ask before generating.

### 2. Build Tier 1 primitives
Generate the full primitive palette:
- Neutral scale (light to dark)
- Primary accent color(s)
- Secondary accent (if applicable)
- Status colors (success, warning, error, info)
- Sky/gradient stops (if the app has dynamic backgrounds)
- Font family tokens

Rules:
- 5–8 steps per color scale is sufficient
- Name primitives descriptively: `gold-500`, `slate-900`, `cedar-700`
- Never use generic names like `color-1` or `shade-light`

### 3. Build Tier 2 semantic tokens
Map primitives to UI roles. This is what agents consume. Must cover:

**Text:** text.primary, text.secondary, text.tertiary, text.disabled, text.inverse, text.on-accent
**Surface:** surface.primary, surface.secondary, surface.elevated, surface.overlay, surface.disabled
**Interactive:** accent, accent.pressed, accent.disabled
**Status:** success, warning, error, info (and their subtle/surface variants)
**Border:** border.default, border.subtle, border.strong
**Shadow:** shadow.card, shadow.elevated, shadow.modal

**Typography:**
- Display scale: display.xl, display.lg, display.md, display.sm
- Body scale: body.lg, body.md, body.sm
- Label scale: label.lg, label.md, label.sm
- Caption: caption.md
Each entry needs: fontFamily, fontSize, fontWeight, lineHeight, letterSpacing

**Spacing:** spacing.1 through spacing.16 (multiples of 4px base)
**Radius:** radius.sm (4px), radius.md (8px), radius.lg (16px), radius.xl (24px), radius.full (9999px)

### 4. Build Tier 3 component tokens (optional)
Only add if the brief or design direction calls for specific component-level overrides. Keep sparse.

### 5. Add special sections
For apps with dynamic behavior:
- Animation tokens (duration, easing)
- Elevation/blur tokens (for frosted glass, etc.)
- Platform-specific notes

### 6. Validate
Run: `npx @google/design.md lint DESIGN.md`

Expected output: `✓ 0 errors, 0 warnings`

If errors exist, fix before presenting to user.

## Output
`DESIGN.md` written to project root (or specified path).

## Quality bar
- Every Tier 2 token references a Tier 1 primitive — no raw hex values in Tier 2
- Typography covers all text sizes visible in the brief/UX spec
- Color system handles both light and dark mode if the app has both
- No orphan primitives (every Tier 1 value is used by at least one Tier 2 token)
