# Skill: build-planner

## Purpose
Analyze project documentation and produce an ordered component build plan organized by atomic layer (atoms → molecules → organisms), so `/design-component` knows exactly what to build in each batch.

## When to load
Only when `/plan-components` is invoked.

## Prerequisites
- `project-context-resolver` already run (docs location known)
- `design-system-reader` loaded (DESIGN.md internalized)
- PRD or brief available with screen/feature descriptions

## Process

### 1. Identify all UI surfaces
From the project docs (UX spec, PRD, or brief), list every:
- Screen / view
- Modal / sheet
- Overlay / tooltip
- Navigation element (tab bar, nav bar, drawer)

### 2. Inventory UI elements per surface
For each surface, list every distinct UI element visible. Include:
- Every button variant
- Every text treatment that's a distinct component (not raw text)
- Every icon usage (as a component if it has consistent sizing/behavior)
- Every card, list item, badge, chip
- Every input, toggle, selector
- Every specialized widget (calendar cells, progress indicators, etc.)

### 3. Classify by atomic level

**Atom criteria:** Cannot be broken into smaller reusable UI pieces. Has no `uses` dependency on other components.
- Buttons, icons, badges, text nodes with specific treatment, inputs, toggles, avatars, dividers

**Molecule criteria:** Combines 2+ atoms into a functional unit.
- Search bar (input + icon + button), date badge (icon + text), feature card header (avatar + text stack)

**Organism criteria:** Complex UI section combining molecules and/or atoms.
- Navigation bars, hero sections, card grids, list sections, modals with content

**Template criteria:** Full page layout structure (rare — most layout lives in views.json).
- Only if the same layout skeleton repeats across multiple views

### 4. Deduplicate
If the same element appears in multiple views, it's ONE component. List it once with a note about all the views it appears in.

### 5. Order within each layer
Within each atomic layer, order by dependency:
- If Component B uses Component A, A comes first
- Otherwise, sort by complexity (simpler first)

### 6. Flag ambiguities
If a UI element could be classified as atom OR molecule, note the ambiguity and ask the user to decide before finalizing the plan.

## Output format

```markdown
# Component Build Plan — [Project Name]

## Summary
- Atoms: N components
- Molecules: N components  
- Organisms: N components
- Total: N components

---

## LAYER 1: ATOMS (build first)

| # | Component | Description | Appears in |
|---|-----------|-------------|------------|
| 1 | Button | Primary action trigger. Variants: primary, secondary, ghost, destructive. | All screens |
| 2 | Icon | Scalable icon wrapper with consistent sizing and color. | All screens |
| 3 | Badge | Small label for status, classification, or count. | Home, Circular |

---

## LAYER 2: MOLECULES (requires approved atoms)

| # | Component | Description | Uses | Appears in |
|---|-----------|-------------|------|------------|
| 1 | SearchBar | Text input with leading icon and clear button. | Input, Icon, Button | Explore |
| 2 | DateBadge | Hebrew date display with day number and month name. | Badge, Text | Home, Widget |

---

## LAYER 3: ORGANISMS (requires approved molecules)

| # | Component | Description | Uses | Appears in |
|---|-----------|-------------|------|------------|
| 1 | NavBar | Top navigation with title and actions. | Icon, Button | All screens |
| 2 | HeroSection | Large feature section with background and primary content. | DateBadge, Button | Home |

---

## Open questions
- [ ] Should [ComponentX] be an atom or molecule? (see rationale above)
```

## Quality bar
- Every component in the plan has a clear, unambiguous description
- No component is missing that appears in the UX spec/screens
- No duplicate components (same element listed twice under different names)
- Dependencies are explicit (molecules list their atom dependencies)
