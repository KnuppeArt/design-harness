# Design Harness

**A 2-agent UI specification pipeline for Claude Code.**

Design Harness produces a complete, agent-consumable SSOT from a project brief — so any coding agent can implement pixel-perfect UI without improvisation.

> "The spec is the product. Code is just the compiled output."

---

## What it produces

```
DESIGN.md          ← Design tokens (3-tier: primitive → semantic → component)
                     Google DESIGN.md format. Stays .md — it's an open standard.
components.json    ← Component registry with full anatomy + behavior specs
views.json         ← View layouts with explicit positions, spacing, breakpoints
```

Any coding agent (Claude Code, Codex, Cursor, Gemini CLI) consumes these three files and implements the UI. Zero interpretation required.

---

## The pipeline

```
@ui-architect
  /gen-design-system  → DESIGN.md
  /plan-components    → ordered build plan (atoms → molecules → organisms)
  /design-component   → designs each layer in Pencil (visual)
  /spec-component     → writes layer into components.json (structured)

    ↓ handoff: components.json passes to @ui-composer

@ui-composer
  /wireframe          → skeleton layout (wireframe.json + .pen)
  /design-view        → detailed view composition in Pencil
  /spec-view          → generates views.json (absolutely complete)

OUTPUT: DESIGN.md + components.json + views.json
```

Human approval gates after each layer. Nothing advances without sign-off.

---

## Two agents

| Agent | Role | Output |
|-------|------|--------|
| `@ui-architect` | Design system + component specs | `DESIGN.md`, `components.json` |
| `@ui-composer` | View composition + layout specs | `views.json` |

---

## Kit contents

```
.claude/
  agents/
    ui-architect.md        ← @ui-architect persona + context
    ui-composer.md         ← @ui-composer persona + context
  skills/
    project-context-resolver/  ← Detects available docs, resolves info sources
    design-system-reader/      ← Reads DESIGN.md, applies 3-tier tokens
    design-system-generator/   ← Generates DESIGN.md from brief
    build-planner/             ← Plans component build order by atomic layer
    component-specifier/       ← Produces components.json entries
    brownfield-decomposer/     ← Decomposes existing UI into atoms/molecules/organisms
    wireframer/                ← Maps components to views (wireframe.json + .pen)
    view-composer/             ← Composes components into detailed layouts
    view-specifier/            ← Generates complete views.json
    design-qa/                 ← Visual QA skill for coding agents (consumer-side)
  commands/
    gen-design-system.md
    plan-components.md
    design-component.md
    spec-component.md
    decompose.md
    wireframe.md
    design-view.md
    spec-view.md
    init-design.md             ← Bootstrap a new project
schemas/
  components-registry.v1.schema.json
  views-registry.v1.schema.json
```

---

## Quick start

### Prerequisites
- Claude Code installed
- Pencil.dev MCP connected
- (Optional) Figma MCP for import

### Install

```bash
# Clone into your Claude user skills directory
git clone https://github.com/KnuppeArt/design-harness.git ~/.claude/design-harness

# Copy agents and skills into your project
cp -r ~/.claude/design-harness/.claude/agents /your-project/.claude/
cp -r ~/.claude/design-harness/.claude/skills /your-project/.claude/
cp -r ~/.claude/design-harness/.claude/commands /your-project/.claude/
cp -r ~/.claude/design-harness/schemas /your-project/

# Or use the bootstrap command
# In Claude Code: /init-design
```

### Run

```
# Start with a project brief. Then in Claude Code:

@ui-architect /gen-design-system    # → DESIGN.md
@ui-architect /plan-components      # → component build plan

# For each layer (atoms → molecules → organisms):
@ui-architect /design-component     # → .pen file (visual)
# [Review and approve]
@ui-architect /spec-component       # → components.json entries

# Once all layers complete:
@ui-composer /wireframe             # → wireframe.json + .pen
# [Review and approve]
@ui-composer /design-view           # → detailed layouts
# [Review and approve]
@ui-composer /spec-view             # → views.json
```

---

## Design principles

**1. SSOT over code** — The three output files are the source of truth. Code is a generated artifact.

**2. Structured over markdown** — `components.json` and `views.json` are machine-readable, schema-validated, diff-friendly. `DESIGN.md` stays `.md` because it's a Google open standard with its own CLI tooling.

**3. Atomic layering** — Components are designed and approved in strict order: atoms first, then molecules (which use approved atoms), then organisms. No skipping layers.

**4. Approval gates** — Nothing advances without human sign-off. The pipeline is designed for collaboration, not automation.

**5. Stack-agnostic** — The SSOT works for Flutter, React Native, Swift UI, React Web, or anything else. Coding agents translate.

**6. Progressive disclosure** — Skills load on demand. Agents only receive context relevant to their current task.

---

## Case study

**Kairos** — Biblical Hebrew calendar app (Flutter, iOS + Android) is the first production use of this pipeline. See [KnuppeArt/kairos-app](https://github.com/KnuppeArt/kairos-app).

---

## Token architecture

Design Harness uses Brad Frost's Subatomic 3-tier token system:

| Tier | Name | Role | Example |
|------|------|------|---------|
| 1 | Primitive | Raw values. Never used directly. | `gold-500: #B8960C` |
| 2 | Semantic | UI roles. What agents consume. | `color.accent: gold-500` |
| 3 | Component | Component-specific overrides. | `Button.primary.bg: accent` |

---

## `components.json` format

```json
{
  "$schema": "https://design-harness.dev/schemas/components-registry.v1.json",
  "version": "0.1.0",
  "project": "YourProject",
  "components": {
    "Button": {
      "level": "atom",
      "anatomy": { },
      "variants": { },
      "states": { },
      "tokens": { }
    }
  }
}
```

## `views.json` format

```json
{
  "$schema": "https://design-harness.dev/schemas/views-registry.v1.json",
  "version": "0.1.0",
  "project": "YourProject",
  "components_registry_version": "0.1.0",
  "views": {
    "HomeScreen": {
      "id": "HomeScreen",
      "title": "Home",
      "type": "screen",
      "navigation": { "entry_points": ["tab-bar-home"] },
      "sections": [
        {
          "id": "nav-bar",
          "component": "NavBar",
          "position": { "placement": "fixed", "anchor": "top-safe-area" },
          "dimensions": { "width": "full", "height": "56px" }
        }
      ]
    }
  }
}
```

One file. All view specs. Single validation point.

---

## Design decisions

| ID | Decision |
|----|----------|
| DA-14 | `components.json` replaces `COMPONENTS.md` — structured + validable wins |
| DA-18 | `views.json` replaces `VIEWS.md` — same logic. `DESIGN.md` stays `.md` (Google standard) |

---

## License

Apache 2.0 — free to use, modify, and distribute.

---

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md).

---

*Design Harness v2.0 — Built by KnuppeArt*
