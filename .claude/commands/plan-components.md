# /plan-components

Analyze project documentation and produce an ordered component build plan by atomic layer.

## Agent
@ui-architect

## Skills loaded
1. `project-context-resolver` — locate and read project docs
2. `design-system-reader` — internalize DESIGN.md tokens
3. `build-planner` — analyze screens, classify components, produce ordered plan

## Requires
- DESIGN.md must exist (run `/gen-design-system` first if not)
- PRD or brief must be available with screen/feature descriptions

## Process

1. Run `project-context-resolver`
2. Load DESIGN.md via `design-system-reader`
3. Run `build-planner`:
   - Extract all views/screens from project docs
   - Inventory all UI elements per view
   - Classify each as atom, molecule, or organism
   - Deduplicate across views
   - Order by dependency within each layer
   - Flag ambiguous classifications
4. Present the plan in tabular format (see `build-planner` output format)
5. Wait for human approval before any `/design-component` work begins

## Output
Build plan presented in chat. Optionally saved to `docs/component-plan.md` if the user asks.

## Approval gate
The user must explicitly approve the plan. Look for: "ok", "approved", "looks good", "proceed", or similar. If the user requests changes, update the plan and present again.

## Notes
- If PRD doesn't exist yet, run this against the brief. The plan may be less precise but is still useful.
- The plan is a living document — if new screens are added during the project, re-run this command to update.
