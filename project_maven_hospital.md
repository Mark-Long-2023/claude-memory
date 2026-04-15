---
name: Maven Hospital Report - Current State
description: D3.js analytics report built on Power BI data — current progress and architecture
type: project
originSessionId: 99784ff6-8ea8-48cb-b8cb-ddd233901303
---
Work-in-progress D3.js Maven Hospital analytics report. Standalone encrypted HTML + data.bin delivery.

**Source file:** `maven-hospital-report-2026-03-31.html` (edit this, never the output)
**Output files:** `maven-hospital-report-encrypted.html` + `data.bin`
**Serve:** `python -m http.server 8765`

## Current state (as of 2026-04-06)

**Encrypted delivery is COMPLETE.** Report opens automatically with no password prompt — loading spinner shows briefly while AES-256-GCM decryption runs in the browser, then full dashboard renders.

**Layout bugs fixed:**
- `min-width: 0` added to `.kpi-card` and `.chart-card` — all grid rows now equal width
- Sparkline width formula corrected to `getBoundingClientRect().width - 28` (was `-8`) — sparklines no longer overflow cards

### Data architecture

- `encounter_classes` — 6 rows hardcoded in build script (not in master_facts.json)
- `enc_fact` — 19,400 rows: RACE, AGE_GROUP, AGE_GROUP_ID, ENCOUNTERCLASS, FY, FY Num, MonthOfYear, MonthName, Year, DayOfWeekName, DayInWeek, NAME + measures [Encounters], [Patients], [TotalCost], [Coverage], [AvgCost], [AvgLOS]
- `enc_desc_fact` — 3,432 rows: DESCRIPTION × RACE × AGE × ENCCLASS × FY + measures [Count], [TotalCost], [AvgCost]
- `procs_fact` — 9,603 rows: DESCRIPTION × RACE × AGE × ENCCLASS × FY + measures [ProcCount], [TotalProcCost], [AvgProcCost]
- `readmission_fact` — 672 rows: Admission_Type × AGE_GROUP_ID × DOW × ENCCLASS × RACE × FY + measures [Count], [TotalCost], [AvgCost], [AvgInterval]

### Encryption

- **Algorithm:** AES-256-GCM, random 32-byte key per build (no password, no PBKDF2)
- **data.bin format:** `[12-byte IV][ciphertext + 16-byte GCM tag]` (~10.4 MB)
- **Key:** embedded as base64 in HTML (`const _dk = "..."`)
- `encrypt_data.py` is now OBSOLETE — `rebuild_report_encrypted.py` does everything

### Rebuild workflow (current — one command)

1. Get fresh PBI token from Chrome DevTools → paste into `.pbi_token.json`
2. `python query_master_facts.py` → writes `master_facts.json`
3. `python rebuild_report_encrypted.py` → encrypts + builds HTML shell (no prompts)
4. `python -m http.server 8765`
5. Open: `http://localhost:8765/maven-hospital-report-encrypted.html`

**Important:** `encounter_classes` is injected by the build script before encrypting — it is NOT in `master_facts.json`.

DATASET_ID: `26908456-dce1-4bc8-92be-ab91eeb46c91`

### Auth

Token in `.pbi_token.json` expires ~1hr. Get fresh token from Chrome DevTools (Network tab, any api.powerbi.com request, copy Authorization header value after "Bearer ").

### JS architecture

**`filterAll(data, opts)`** — central filter function used by all render functions:
- Applies selectedFYs, selectedAges (or AGE_GROUP_ID for readmission_fact), selectedEncClasses, crossFilter
- opts: `useAgeId`, `skipFYFilter`, `skipAgeFilter`, `skipEncFilter`, `skipCrossFilter`
- `AGE_LABEL_TO_ID = {"0-19":1, "20-29":2, ..., "80+":8}` maps age labels to IDs for readmission_fact

**Cross-filter behavior per fact:**
- `age`: all facts (readmission_fact via AGE_GROUP_ID)
- `race`: all facts
- `dow`: enc_fact + readmission_fact only
- `payer`: enc_fact only
- `enc`: enc_desc_fact only
- `procs`: procs_fact only

**`setupFilters()`** — rebuilds all 3 dropdown checkbox lists from real DATA after decryption; called by `initReport()` before `renderAll()`.

### Remaining / future items

- readmission chart shows cards (not bars); no click-to-cross-filter from it (responds to others)
- **Viz in tooltip** — mini chart inside tooltip on hover (discussed, not yet implemented)
- **Drill up/down** — IMPLEMENTED on monthly trend chart. Three states: Year view / All-months / Single-year months. Up/Down buttons in card header greyed when unavailable.
