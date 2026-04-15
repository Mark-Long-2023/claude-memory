---
name: Power BI D3 Report Project
description: Building D3.js reports from Case Study semantic model in personal Power BI workspace on novypro.com tenant
type: project
---

User wants to connect to their personal Power BI workspace (app.powerbi.com, novypro.com tenant) and generate D3.js reports from existing semantic models.

**Current status:** Paused — company laptop has corporate SSL proxy blocking API calls. Will resume on personal laptop.

**What's built:**
- Power BI MCP server (Python/MSAL) at `C:\Users\e612775\AppData\Roaming\Claude\mcp-servers\powerbi\server.py`
- Uses device code auth flow with novypro.com tenant, Azure PowerShell client ID
- Token caching to disk implemented
- Tools: list_datasets, get_dataset_tables, execute_dax_query, list_reports, get_dataset_refresh_history
- Configured in `claude_desktop_config.json`

**Case Study.pbix schema (extracted from Report/Layout):**
- Pages: Episodes, Procedures, Charges, Page 1, Page 2, Page 3, Page 5
- Tables: Fact_Main (17 cols), Fact_Proc, Fact_Diag, Dim_Date (12 cols), Dim_Gender, Dim_UOA, Dim_CareType, Dim_SOR, Dim_MOS, Dim_Sameday, Key_Metrics (35 measures), Date_Range, Episode_Summary, ParetoThreshold, Trend_Legend
- Data is personal public data, not healthcare/sensitive

**Blockers on company laptop:**
- Corporate SSL proxy causes certificate verification failure on Power BI API calls
- Python 3.14 can't build xpress9 (needed by pbixray to decompress DataModel)
- No Node.js (installation blocked)
- Power BI Desktop for RS can't open cloud .pbix files

**Why:** User wants minimal manual intervention — prefers live MCP connection over manual exports.

**How to apply:** On personal laptop, try MCP live connection first. If that works, query data via DAX and build D3 visualizations.
