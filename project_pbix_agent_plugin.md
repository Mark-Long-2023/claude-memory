---
name: PBIX Agentic Development Plugin - Project Concept
description: Personal experiment to build an organisation-specific Claude Code plugin for Power BI Report Server (.pbix) agentic development
type: project
status: concept / early planning
originSessionId: 0329a652-99b7-41a9-9cc0-86f2655d22ea
---
# PBIX Agentic Development Plugin

## Goal
Build a Claude Code plugin (same structure as power-bi-agentic-development) tailored to the organisation's Power BI Report Server (.pbix) environment. The end goal is to delegate all manual Power BI development work to an agent, with the plugin providing comprehensive guidance on how to do things correctly.

## Motivation
- No existing conventions in the team — opportunity to build right habits from scratch
- Eventual showcase to manager as a feasibility case for agentic Power BI development adoption
- Long-term: enterprise-level guidelines for the analytics team's agentic development workflow

## Plugin Structure (same as power-bi-agentic-development)
- **Skills** — domain knowledge: naming conventions, DAX standards, semantic model design, report layout guidelines
- **Hooks** — automated guardrails: BPA validation before save, naming checks, .pbix round-trip integrity
- **Agents** — autonomous subprocesses: full model review, report audit, new report scaffold from template

## Technical Stack (all free/open source)
| Tool | License | Role |
|---|---|---|
| pbi-tools | MIT | .pbix extract/compile (round-trip) |
| Tabular Editor 2 (TE2) | MIT | Data model manipulation via BIM |
| Layout JSON (direct) | — | Report layer manipulation |
| PBRS REST API | Free | Publish to Power BI Report Server |

Note: Tabular Editor 3 (TE3) is paid (~$595/yr) — use TE2 instead, covers all needed CLI capabilities.

## Key Architecture Insight
The .pbix is a ZIP containing:
- **DataModel** — binary VertiPaq/AS format (manipulated via TE2 after pbi-tools extract)
- **Layout** — JSON (report pages, visuals, formatting — directly editable)
- **Metadata / Connections** — JSON/binary mix

Round-trip workflow: `pbi-tools extract` → edit model (TE2) + edit layout (JSON) → `pbi-tools compile`

## BPA Rules Strategy
- Start with Daniel Otykier's community BPA rules as baseline
- Run against personal .pbix file first to assess current gaps
- Prune rules that don't fit the context
- Add organisation-specific rules incrementally as team matures

## Next Steps (when ready)
1. Install pbi-tools and Tabular Editor 2
2. Run BPA against a personal .pbix file to see baseline
3. Study power-bi-agentic-development plugin file structure as blueprint
4. Start building plugin with first skills encoding community best practices

## Context
- Experiment starts on personal computer with a personal .pbix project
- If successful, may be showcased to manager for potential team adoption
- Not tied to pbir-cli in any way — independent project
- Power BI Report Server target (not Fabric/cloud) — .pbix binary format, not PBIR
