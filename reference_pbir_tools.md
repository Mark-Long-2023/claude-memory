---
name: pbir.tools CLI Reference
description: CLI tool for Power BI PBIR report automation — commands, installation, and agent integration details
type: reference
originSessionId: 0329a652-99b7-41a9-9cc0-86f2655d22ea
---
# pbir.tools (pbir-cli)

GitHub: https://github.com/maxanatsko/pbir.tools  
Current version: 0.9.7  
Tagline: "Built for humans, optimized for agents"

## Installation

```bash
uv tool install pbir-cli   # recommended
pip install pbir-cli       # alternative
```

Native installers (.exe/.pkg) also available via GitHub Releases for Windows/macOS.

## Core Commands

Paths use folder-based PBIR format: `Report.Report/Page.Page/Visual.Visual`

| Command | Description |
|---|---|
| `pbir ls` | List reports |
| `pbir tree "Report.Name" -v` | Show full report structure with fields |
| `pbir model "Report.Name" -d` | Display model schema |
| `pbir add visual card "Path" --title "Name"` | Add a visual to a page |
| `pbir set "Path/**/*.Visual.property" --value X -f` | Bulk set a property across visuals |
| `pbir validate "Report.Name"` | Run health checks on a report |
| `pbir publish "Workspace.Workspace/Report.Name"` | Deploy report to Microsoft Fabric |

## Agent Integration

Supports: Claude Code, GitHub Copilot, Cursor, Gemini CLI, Codex

Setup options:
```bash
pbir setup           # interactive agent detection
pbir setup --all     # install all plugins
pbir setup --agent claude --plugin power-bi-agentic-development
```

Also installable via the power-bi-agentic-development marketplace (already installed on this machine).

## Safety Notes

- Always backup reports before edits
- Run `pbir validate` after mutations
- Use `-f` (force/bulk) flag cautiously
- Treat `publish`, `convert`, and `merge` as higher-risk operations
