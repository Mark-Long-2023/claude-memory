---
name: PBIX Agentic Development Plugin
description: Custom Claude Code plugin for Power BI Report Server agentic development — PBIX-first workflow using pbi-tools + TE2, distinct from the installed cloud-oriented plugin
type: project
originSessionId: ecee92a0-e890-426c-ac5e-6f6b74a2d2f2
---
Goal: delegate all manual Power BI development work to a Claude Code plugin/agent, specifically for a Power BI Report Server (PBRS) environment where PBIX is the permanent format.

**Status:** Design phase — tools installed, report layout explored, ready to draft plugin structure.

## Key Constraint: PBRS = PBIX Forever

The workplace runs Power BI Report Server (not Power BI Service). PBIP/PBIR are cloud-only formats and will never be supported by PBRS. All automation must work with PBIX files via pbi-tools extract/compile cycle.

## Installed Plugin (power-bi-agentic-development v0.24.5)

An existing plugin is installed at:
`C:\Users\e612775\.claude\plugins\cache\power-bi-agentic-development\`

It has 6 components: tabular-editor, pbi-desktop, semantic-models, reports, pbip, fabric-cli.

**Problem:** It targets PBIP/PBIR (cloud format) — most skills are not applicable to PBRS workflow. Useful parts:
- `tabular-editor` skills — TE2 CLI is format-agnostic, fully usable
- `pbi-desktop` hooks — TOM/AMO scripts work against PBI Desktop RS
- `semantic-models` skills — logic is sound, tooling varies

Use this plugin as **reference and inspiration** only, not as the foundation.

## Tools Installed (2026-04-15, company laptop)

- **pbi-tools 1.2.0** — `C:\Tools\pbi-tools\pbi-tools.exe`
  - Env var set: `PBITOOLS_PBIInstallDir = C:\Program Files\Microsoft Power BI Desktop RS\bin`
  - Required because pbi-tools doesn't auto-detect PBI Desktop RS registry key
- **Tabular Editor 2 v2.28.0** — `C:\Tools\TabularEditor\TabularEditor.exe`
  - Portable install, no admin rights needed
- Both added to user PATH permanently
- Downloads done via PowerShell (curl blocked by SSL proxy, PowerShell uses Windows cert store)

## Architecture (three-part, to be drafted)

- **Skills** — domain knowledge: naming conventions, DAX standards, design patterns
- **Hooks** — automated validation: BPA checks, naming verification, integrity checks
- **Agents** — autonomous processes: model review, report auditing, scaffold generation

## Core Workflow

```
pbi-tools extract PBIX → edit schema/layout JSON → pbi-tools compile → deploy to PBRS
```

- Model layer: TE2 CLI handles table/column/measure/relationship edits
- Report layer: direct JSON manipulation of extracted visualContainer.json and config.json files
- Data layer: NEVER touch — Import mode PBIX files contain real data rows

## Report Layout Structure (explored 2026-04-15)

Extracted from: `C:\Users\e612775\Desktop\Test\Business Development Dashboard (New Definitions).pbix`
Extracted to: `C:\Users\e612775\Desktop\Test\extracted\`

```
Report/
├── report.json, config.json, filters.json
├── bookmarks/
└── sections/          ← one folder per page (26 pages in this file)
    └── 002_Overview/
        ├── section.json           ← page name, size (1280x720)
        ├── filters.json
        └── visualContainers/      ← one folder per visual
            └── 04000_Total Surgical Revenue/
                ├── visualContainer.json   ← x, y, width, height, z, tabOrder
                ├── config.json            ← visualType, projections, prototypeQuery, objects, vcObjects
                └── filters.json
```

Key fields in visual config.json:
- `singleVisual.visualType` — e.g. kpi, card, slicer, barChart
- `singleVisual.projections` — field/measure bindings per visual slot
- `singleVisual.prototypeQuery` — DAX query structure (tables/measures referenced)
- `singleVisual.objects` — visual-specific formatting (colours, units, direction)
- `singleVisual.vcObjects` — container formatting (title, background, border, subtitle)

## Data Safety Rule

- After extract, NEVER read/edit the `Model\` data cache files
- Safe to read: Report/, Model/database.json (schema), ReportMetadata.json, ReportSettings.json
- Connection strings in Connections/ may reveal internal server/database names — review before sharing

## Next Steps

1. Draft the plugin structure (CLAUDE.md, skills, hooks, scripts)
2. Design schema-only workflow for model layer automation
3. Design report layout automation based on explored JSON structure
4. Experiment on a test PBIX before applying to work files

**Why:** User wants to minimise manual PBI development and demonstrate agent-driven workflow to their team.
