# Skill: design-qa

## Purpose
Evaluate implemented UI against the Design Harness SSOT (DESIGN.md + components.json + views.json) and produce a structured gap report.

## When to load
Used by coding agents and developers AFTER implementation — not part of the spec pipeline. Load when reviewing a built UI against the spec.

## This skill is for coding agents that consume the SSOT

@ui-architect and @ui-composer produce the SSOT. This skill is for whoever implements it.

---

## Evaluation checklist

### 1. Token compliance
For every visual property in the implementation:
- [ ] Colors reference DESIGN.md Tier 2 semantic tokens (or Tier 3 component tokens)
- [ ] No raw hex values in component code (all via token variables)
- [ ] Typography matches DESIGN.md font families, sizes, weights, line heights
- [ ] Spacing matches DESIGN.md spacing scale
- [ ] Border radius matches DESIGN.md radius tokens

### 2. Component anatomy compliance
For each component, compare implementation to components.json:
- [ ] All required anatomy parts are present
- [ ] Optional parts are conditionally rendered (not always shown, not always hidden)
- [ ] All variants are implemented and match spec
- [ ] All states are implemented and match spec (especially disabled, loading, error)
- [ ] Tier 3 component token overrides are applied

### 3. Behavior compliance
- [ ] All gestures from `behavior.gestures` are implemented with correct actions
- [ ] Haptic feedback matches spec (light, medium, heavy, selection, etc.)
- [ ] Animations match spec (type, duration, easing)
- [ ] Accessibility: role, label, hint, focusable as specified

### 4. View layout compliance
For each view, compare implementation to views.json:
- [ ] All sections present in correct top-to-bottom order
- [ ] Fixed elements correctly anchored (top/bottom + safe area)
- [ ] Flow elements in correct order with correct margins
- [ ] Dimensions match spec (width, height)
- [ ] Spacing between sections matches spec
- [ ] Conditional sections only render when condition is true
- [ ] Scroll behavior matches spec
- [ ] Background matches spec (including dynamic behaviors)
- [ ] Safe area handling correct (top/bottom)

### 5. Responsive compliance
For each breakpoint override in views.json:
- [ ] Changes are applied at the correct breakpoint
- [ ] No other changes introduced at that breakpoint

### 6. Interactions compliance
- [ ] Every tap/gesture defined in views.json is implemented
- [ ] Navigation actions route to the correct view

---

## Report format

```markdown
# Design QA Report — [ViewName or "All Views"]
Date: YYYY-MM-DD

## Summary
- ✅ Passing: N checks
- ⚠️ Warnings: N (minor deviations)
- ❌ Failures: N (spec violations)

## Failures (must fix)
### [ComponentName] — anatomy missing: error state
- Spec: components.json defines error state with border {colors.error}
- Found: No error state implemented
- Fix: Add error state with `border: {colors.error}` token

## Warnings (should fix)
### [ViewName] — margin_top deviation
- Spec: views.json specifies margin_top: spacing.6 (24px) above HeroSection
- Found: 20px hardcoded margin
- Fix: Use spacing token spacing.6

## Passing
- ✓ Button variants: primary, secondary, ghost — all match spec
- ✓ TabBar: fixed bottom with safe area — correct
```

---

## What this skill does NOT do
- Does not modify the SSOT (views.json, components.json, DESIGN.md)
- Does not make implementation decisions
- Does not suggest alternative designs — only evaluates against the spec
- If the spec is wrong, flag it and ask a human to update the spec first
