# /design-component

Design components for the current atomic layer in Pencil.

## Agent
@ui-architect

## Skills loaded
1. `design-system-reader` — internalize DESIGN.md tokens
2. `project-context-resolver` — resolve project context as needed

## Usage

```
/design-component              ← batch: all components in current layer
/design-component Button       ← single: design one specific component
/design-component Button,Icon  ← subset: comma-separated list
```

## Requires
- Approved component build plan (from `/plan-components`)
- Pencil MCP connected
- For molecules: atom layer approved
- For organisms: molecule layer approved

## Process

### Batch mode (no argument)
1. Identify the current layer: which is the first layer with no approved designs yet? (atoms → molecules → organisms)
2. List all components in that layer from the build plan
3. For each component in order:
   a. Read its description and context from the build plan
   b. Read DESIGN.md tokens
   c. Create a Pencil frame named `[ComponentName]/[variant]/[state]` convention
   d. Design all variants × all states
   e. Use DESIGN.md tokens for all visual properties — no raw values
4. Present all designs to user for review
5. Wait for explicit approval before `/spec-component` can proceed

### Single/subset mode (with argument)
Same process as batch but only for the named component(s). Useful for corrections after approval.

## Design quality requirements

Before presenting for approval, every component must have:
- [ ] Default state designed
- [ ] All variants designed (even if visually similar — document the difference)
- [ ] Pressed/active state designed
- [ ] Disabled state designed (typically: opacity 0.4 + no interaction)
- [ ] Loading state (if the component triggers async operations)
- [ ] Error state (if the component involves user input)
- [ ] Mobile-first sizing (44px minimum touch target for interactive elements)
- [ ] All typography from DESIGN.md font tokens
- [ ] All colors from DESIGN.md semantic tokens
- [ ] All spacing from DESIGN.md spacing tokens

## Pencil frame naming convention
```
ComponentName/               ← root group
  default/                   ← default variant
    default                  ← default state
    pressed
    disabled
  secondary/
    default
    pressed
    disabled
```

## Approval gate
User must explicitly approve the batch before `/spec-component` runs. Look for: "approved", "looks good", "proceed", "ok".

If the user requests changes to specific components, redesign those and present again. Do not re-present the entire batch unless the user asks.
