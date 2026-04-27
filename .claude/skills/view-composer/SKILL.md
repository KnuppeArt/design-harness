# Skill: view-composer

## Purpose
Design complete, detailed view layouts in Pencil — placing approved components with exact positions, grid systems, and spacing — ready for human approval before spec generation.

## When to load
Only when `/design-view` is invoked.

## Prerequisites
- Approved wireframe.json exists
- components.json fully populated (all layers approved)
- `design-system-reader` loaded
- Pencil MCP available

## Process

### 1. Read wireframe.json
Load the approved skeleton. For each view, know:
- Which components appear
- Their rough order and placement type (fixed/flow/etc.)
- Any conditional rules

### 2. Establish the grid system
For each view, define:
- Column count (typically 4 for mobile, 8–12 for tablet/desktop)
- Gutter width (from DESIGN.md spacing tokens)
- Margin from screen edge (from DESIGN.md spacing tokens)
- Maximum content width

All layouts start from this grid. No ad-hoc positioning.

### 3. Design each section in Pencil
For each component in a view:

**Fixed elements (NavBar, TabBar, FAB):**
- Define exact height
- Anchor to correct edge (top/bottom + safe area)
- Width is always 100% of screen

**Flow elements (main content):**
- Define width relative to grid (full-width, half, etc.)
- Define height (auto = content-driven, or explicit)
- Define margin_top from previous section using spacing tokens

**Floating elements (overlays, tooltips):**
- Define anchor point explicitly
- Define z-ordering note

### 4. Apply tokens from DESIGN.md
Every visual property in the layout must reference a DESIGN.md token:
- Background: `colors.surface.primary` or special values like `sky-dynamic`
- Section backgrounds: always via tokens, never raw hex
- Spacing: always via spacing tokens

### 5. Design all breakpoints
For mobile-first apps (Flutter, React Native):
- **Mobile (default):** Full design required
- **Tablet (≥600dp):** Note which sections change layout
- **Large screens (≥840dp):** Only if applicable to the project

For web apps:
- **Mobile (320–767px), Tablet (768–1023px), Desktop (1024px+)**

### 6. Design interactions in Pencil
For each section, add interaction annotations:
- Tap/gesture → resulting action (use component behavior as source of truth)
- Swipe gestures → direction + action
- Scroll behavior of the view

### 7. Present for approval
Pencil link per view. Present all views together for batch approval, or one by one if the user prefers.

Human MUST approve all views before /spec-view proceeds.

## Quality bar

A designed view is complete when:
- [ ] All sections from wireframe.json are placed
- [ ] Every section has explicit width + height
- [ ] Every gap between sections uses a spacing token
- [ ] Fixed/sticky elements have explicit anchor points
- [ ] All conditional sections are annotated
- [ ] All interactive sections have gesture annotations
- [ ] Tablet/desktop variants exist (if applicable)
- [ ] Safe area handling is visible (top/bottom padding)
