# /design-view

Design detailed, pixel-complete view layouts in Pencil using approved wireframe as the structure guide.

## Agent
@ui-composer

## Skills loaded
1. `design-system-reader`
2. `view-composer`

## Usage

```
/design-view              ← all views
/design-view HomeScreen   ← one specific view
```

## Requires
- Approved wireframe (wireframe.json must exist and be approved)
- components.json fully populated
- DESIGN.md available
- Pencil MCP connected

## Process

Run `view-composer` for each view:
1. Load wireframe.json for structural skeleton
2. For each view, design in Pencil:
   - Establish the grid system
   - Place each component from the wireframe with exact dimensions and spacing
   - Apply DESIGN.md tokens to all visual properties
   - Design all breakpoint variants (tablet/desktop if applicable)
   - Add interaction annotations
3. Present all views for approval (or one at a time if user prefers)

## Approval gate
User must explicitly approve all views before `/spec-view` can proceed.

User is approving:
- ✓ This is the correct layout for each view
- ✓ Components are positioned correctly
- ✓ Spacing and sizing feel right
- ✓ The visual design matches the project aesthetic

Changes requested → redesign the affected view(s) → present again.

## Notes
- If designing a single view (`/design-view HomeScreen`), only that view is presented for approval
- All-view approval is preferred to minimize back-and-forth before `/spec-view`
