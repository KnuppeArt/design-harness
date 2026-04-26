# Design Harness — Architecture

**Version:** 2.0  
**Date:** 2026-04-26  
**Status:** Production

---

## Overview

Design Harness is a spec-driven pipeline that bridges project briefs and code implementation. Two specialized Claude Code agents collaborate to produce a complete, agent-consumable UI specification — the SSOT (Single Source of Truth) that any coding agent consumes to implement pixel-perfect UI.

---

## Core architecture

```
INPUT: project-brief.md (+ optional: prd.md, ux-spec.md, trd.md, domain-rules.md)

┌─────────────────────────────────────────────────────────────────┐
│ @ui-architect                                                   │
│                                                                 │
│  /gen-design-system ──────────────────────────→ DESIGN.md      │
│  /plan-components  ──────────────────────────→ build-plan.md   │
│                                                                 │
│  ATOMS LAYER:                                                   │
│    /design-component ──→ atoms.pen  ──[approval]──→            │
│    /spec-component   ──→ components.json (atom entries)        │
│                                                                 │
│  MOLECULES LAYER (uses approved atoms):                         │
│    /design-component ──→ molecules.pen ──[approval]──→         │
│    /spec-component   ──→ components.json (molecule entries)    │
│                                                                 │
│  ORGANISMS LAYER (uses approved molecules):                     │
│    /design-component ──→ organisms.pen ──[approval]──→         │
│    /spec-component   ──→ components.json (organism entries)    │
└─────────────────────────────────────────────────────────────────┘
                              │
                    components.json handoff
                              │
┌─────────────────────────────────────────────────────────────────┐
│ @ui-composer                                                    │
│                                                                 │
│  /wireframe    ──→ wireframe.json + wireframe.pen ──[approval]──→ │
│  /design-view  ──→ views.pen ──[approval]──────────────────→   │
│  /spec-view    ──→ views.json                                   │
└─────────────────────────────────────────────────────────────────┘

OUTPUT: DESIGN.md + components.json + views.json
        → coding agent implements
```

---

## Agents

### @ui-architect

**Responsibility:** Design system + component specification.

**Skills loaded:**
| Skill | Purpose |
|-------|---------|
| `project-context-resolver` | Detects available docs, resolves info sources dynamically |
| `design-system-reader` | Reads DESIGN.md, understands 3-tier token architecture |
| `design-system-generator` | Generates DESIGN.md from brief, validates with Google CLI |
| `build-planner` | Analyzes docs, identifies components, plans build order by atomic layer |
| `component-specifier` | Produces `components.json` entries, validates against schema |
| `brownfield-decomposer` | Receives existing UI, decomposes into atoms/molecules/organisms |

**External skills:**
- `ui-ux-pro-max` — UX strategy, design principles
- `frontend-design` (Anthropic) — Anti-slop: spacing, typography, states

**Commands:**
| Command | Behavior |
|---------|----------|
| `/gen-design-system` | Generates DESIGN.md from project brief |
| `/plan-components` | Analyzes brief, produces ordered build plan |
| `/design-component` | No arg = batch current layer / arg = specific component |
| `/spec-component` | No arg = batch entire .pen → components.json / arg = specific |
| `/decompose` | Brownfield: receives existing UI, generates specs |

---

### @ui-composer

**Responsibility:** View composition + layout specification.

**Skills loaded:**
| Skill | Purpose |
|-------|---------|
| `project-context-resolver` | Shared with @ui-architect |
| `design-system-reader` | Shared with @ui-architect |
| `wireframer` | Reads brief + components.json → wireframe.json + .pen |
| `view-composer` | Composes components into layouts with spacing/grids |
| `view-specifier` | Generates complete views.json from approved views |

**External skills:**
- `frontend-design` (Anthropic) — Shared with @ui-architect

**Commands:**
| Command | Behavior |
|---------|----------|
| `/wireframe` | No arg: from prd.md + components.json / `--from-image`: from wireframe image / `--from-pen`: import existing .pen |
| `/design-view` | No arg = all views / arg = specific view |
| `/spec-view` | Generates views.json from approved views |

---

## Skills architecture

Skills use progressive disclosure — they load only when explicitly needed by a command. This keeps agent context lean and task-focused.

```
.claude/skills/
  project-context-resolver/   ← Shared. Loaded on agent init.
  design-system-reader/       ← Shared. Loaded when reading DESIGN.md.
  design-system-generator/    ← Architect only. Loaded by /gen-design-system.
  build-planner/              ← Architect only. Loaded by /plan-components.
  component-specifier/        ← Architect only. Loaded by /spec-component.
  brownfield-decomposer/      ← Architect only. Loaded by /decompose.
  wireframer/                 ← Composer only. Loaded by /wireframe.
  view-composer/              ← Composer only. Loaded by /design-view.
  view-specifier/             ← Composer only. Loaded by /spec-view.
  design-qa/                  ← Consumer-side. Used by coding agents post-implementation.
```

---

## SSOT format

### DESIGN.md

Google DESIGN.md format extended with Brad Frost Subatomic 3-tier token architecture. Stays `.md` — it's an open Google standard with its own CLI tooling (lint, diff, export).

```yaml
primitive:           # Tier 1: Raw values. Never used directly.
  gold-500: "#B8960C"

colors:              # Tier 2: Semantic roles. What agents consume.
  accent: "{primitive.gold-500}"

components:          # Tier 3: Component-specific overrides.
  Button:
    primary-bg: "{colors.accent}"
```

### components.json

Machine-readable, schema-validated component registry. One file, all components, single validation point.

```json
{
  "$schema": "https://design-harness.dev/schemas/components-registry.v1.json",
  "version": "0.1.0",
  "project": "ProjectName",
  "components": {
    "ComponentName": {
      "level": "atom | molecule | organism | template",
      "anatomy": { },
      "variants": { },
      "states": { },
      "tokens": { },
      "behavior": { }
    }
  }
}
```

### views.json

Machine-readable, schema-validated view layout registry. No ambiguity — every view documents:

- All sections in explicit order (top to bottom)
- Which component goes in each section (ref by name to `components.json`)
- Grid spec: columns, gap, container max-width
- Position of each component: alignment, width, height, placement type
- Spacing between sections and components
- Responsive breakpoints: what changes at mobile/tablet/desktop
- Behavior: scroll, sticky, fixed elements, background, safe areas
- Interactions: what happens on tap/swipe/gesture

```json
{
  "$schema": "https://design-harness.dev/schemas/views-registry.v1.json",
  "version": "0.1.0",
  "project": "ProjectName",
  "components_registry_version": "0.1.0",
  "views": {
    "HomeScreen": {
      "id": "HomeScreen",
      "title": "Home",
      "type": "screen",
      "navigation": { "entry_points": ["tab-bar-home"] },
      "behavior": { "scroll": "vertical", "background": "sky-dynamic" },
      "sections": [
        {
          "id": "tab-bar",
          "component": "TabBar",
          "position": { "placement": "fixed", "anchor": "bottom-safe-area" },
          "dimensions": { "width": "full", "height": "auto" }
        }
      ]
    }
  }
}
```

---

## Design decisions

| ID | Decision | Rationale |
|----|----------|-----------|
| DA-01 | 2 agents, not 1 | Architect (design system + components) and Composer (views) have fundamentally different context needs. |
| DA-02 | `components.json` is the only SSOT for components | Single file = single validation point. |
| DA-03 | Atomic layering is enforced | Molecules cannot be designed until atoms are approved. Prevents inconsistency cascades. |
| DA-04 | Human approval gates at every layer | Pipeline is collaborative, not autonomous. |
| DA-05 | Stack-agnostic output | SSOT is framework-independent. |
| DA-06 | `project-context-resolver` not hardcoded filenames | Pipeline works with 2–6 input docs. Agents discover what's available. |
| DA-07 | Skills over large agent files | Progressive disclosure. Each skill loads when needed. |
| DA-08 | Google DESIGN.md format as base | CLI tooling (lint, diff, export) included. |
| DA-09 | Brad Frost Subatomic 3-tier tokens | Cleanest separation: primitives → semantic → component. |
| DA-10 | Pencil.dev as primary visual tool | Free. `.pen` = JSON (inspectable). Native MCP. |
| DA-11 | `/decompose` for brownfield | Handles existing UI in code or images. |
| DA-12 | Pipeline is platform-agnostic | SSOT is universal. No stack-specific questions in `/init-design`. |
| DA-14 | `components.json` replaces `COMPONENTS.md` | Structured + validable beats human-readable for agent consumption. |
| DA-18 | `views.json` replaces `VIEWS.md` | Same logic as DA-14. `DESIGN.md` stays `.md` — it's a Google open standard. |

---

## File structure (project using Design Harness)

```
your-project/
  .claude/
    agents/
      ui-architect.md
      ui-composer.md
    skills/
      project-context-resolver/SKILL.md
      design-system-reader/SKILL.md
      design-system-generator/SKILL.md
      build-planner/SKILL.md
      component-specifier/SKILL.md
      brownfield-decomposer/SKILL.md
      wireframer/SKILL.md
      view-composer/SKILL.md
      view-specifier/SKILL.md
      design-qa/SKILL.md
    commands/
      gen-design-system.md
      plan-components.md
      design-component.md
      spec-component.md
      decompose.md
      wireframe.md
      design-view.md
      spec-view.md
      init-design.md
  schemas/
    components-registry.v1.schema.json
    views-registry.v1.schema.json
  docs/
    brief.md
    prd.md           ← Required for /plan-components
    ux-spec.md       ← Optional. More precision = better output.
    domain-rules.md  ← Optional. Domain-specific logic.
  DESIGN.md          ← Generated by /gen-design-system
  components.json    ← Built up by /spec-component
  views.json         ← Generated by /spec-view
  CLAUDE.md          ← Project-level rules for Design Harness
```

---

*Design Harness — Architecture v2.0*
