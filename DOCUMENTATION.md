# BEACON — Technical Documentation

**Vermont SHSO · Vermont Agency of Transportation**
Version: v37 | Data range: 2020–2024

---

## Table of Contents

1. [Architecture Overview](#1-architecture-overview)
2. [Dependencies](#2-dependencies)
3. [Data Structure](#3-data-structure)
4. [Section System](#4-section-system)
5. [Chart System](#5-chart-system)
6. [Export System](#6-export-system)
7. [Glossary PDF](#7-glossary-pdf)
8. [Branding & Styling](#8-branding--styling)
9. [SharePoint Embedding](#9-sharepoint-embedding)
10. [Known Fragility Patterns](#10-known-fragility-patterns)
11. [How to Update Data](#11-how-to-update-data)

---

## 1. Architecture Overview

BEACON is a **single-file HTML application**. All HTML, CSS, JavaScript, and embedded data live in `index.html`. There is no backend, no database, no server-side logic, and no user data collected or transmitted.

The file is self-contained except for three CDN-loaded JavaScript libraries (see [Dependencies](#2-dependencies)). It runs entirely in the browser and can be:

- Opened locally from the filesystem
- Embedded in SharePoint via iframe
- Hosted on any static web server (GitHub Pages, state web, ADS infrastructure)

---

## 2. Dependencies

All three libraries are loaded from the Cloudflare CDN with Subresource Integrity (SRI) hashes where applicable.

| Library | Version | Purpose | SRI |
|---|---|---|---|
| Chart.js | 4.4.1 | All data visualizations | Pending (see note) |
| jsPDF | 2.5.1 | PDF export | ✅ `sha384-JcnsjUP...` |
| xlsx | 0.18.5 | Excel export | ✅ `sha384-vtjasyid...` |

**Chart.js SRI note:** The SRI hash for Chart.js must be generated from the exact CDN-served file using:
```bash
curl -s https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js \
  | openssl dgst -sha384 -binary | openssl base64 -A
```
Then add `integrity="sha384-<hash>" crossorigin="anonymous"` to the script tag.

**html2canvas** is no longer a dependency as of v36+. It was removed and replaced with a native Canvas API approach for JPG export.

---

## 3. Data Structure

All crash data lives in a single `const DATA` object in the JavaScript section of `index.html`.

### Top-level structure

```javascript
const DATA = {
  statewide: { fatalities: [...], serious: [...], years: [...], ... },
  distracted: { ... },
  impaired:   { ... },
  occupant:   { ... },
  speeding:   { ... },
  older:      { ... },
  motorcycle: { ... },
  younger:    { ... },
  pedestrian: { ... },
  bicycle:    { ... }
}
```

### Per-section fields (full sections)

Each full section object contains:
- `years` — array of year labels, e.g. `[2020, 2021, 2022, 2023, 2024]`
- `fatalities` — array of annual fatality counts
- `serious` — array of annual serious injury counts
- `fsi` — array of fatalities + serious injuries combined
- `breakdown` — object containing sub-arrays by dimension:
  - `ageSex` — female/male split by age group
  - `month` — by calendar month
  - `dayOfWeek` — by day of week
  - `timeOfDay` — by time block
  - `crashType` — by crash type category
  - `speedLimit` — by posted speed limit range

### Per-section fields (lite sections)

Lite sections (older, motorcycle, younger, pedestrian, bicycle) contain `years`, `fatalities`, `serious`, and `fsi` only. Breakdown sub-arrays are omitted because granular breakdown data is limited for these populations.

---

## 4. Section System

### Full sections vs. lite sections

| Type | Sections | Charts | Breakdown Panel |
|---|---|---|---|
| Full | overview, distracted, impaired, occupant, speeding | 3 combo charts | ✅ Yes |
| Lite | older, motorcycle, younger, pedestrian, bicycle | 3 combo charts | ❌ No |

Full sections call `buildCharts(prefix, data)`. Lite sections call `buildChartsLite(prefix, data)`. The difference is that `buildCharts` also builds the six breakdown sub-charts rendered inside the dig-deeper panel.

### Dig-deeper toggle

Each full section has a "Dig Deeper" button that toggles a hidden breakdown panel via `toggleDigDeeper(prefix)`. The panel contains donut charts, grouped bar charts, and additional combo charts for the breakdown dimensions listed above.

### Section IDs and prefixes

Each section has two identifiers:

| Section Key | HTML `id` | Chart prefix |
|---|---|---|
| `overview` | `overview` | `ov` |
| `distracted` | `distracted` | `dd` |
| `impaired` | `impaired` | `imp` |
| `occupant` | `occupant` | `op` |
| `speeding` | `speeding` | `sp` |
| `older` | `older` | `old` |
| `motorcycle` | `motorcycle` | `mc` |
| `younger` | `young` | `yd` |
| `pedestrian` | `pedestrian` | `ped` |
| `bicycle` | `bicycle` | `bic` |

> ⚠️ **Important:** The `younger` section key maps to the HTML element with `id="young"` (not `"younger"`). This is a known inconsistency that is handled explicitly in the export system's `prefixMap`. Do not "fix" the HTML id without updating every reference.

---

## 5. Chart System

BEACON uses Chart.js 4.4.1. Four chart builder functions handle all visualizations:

| Function | Chart type | Used for |
|---|---|---|
| `comboBar(id, labels, barData, lineData, ...)` | Bar + line combo | Primary FSI trend charts |
| `barChart(id, labels, data, color, label)` | Simple bar | Breakdown sub-charts |
| `groupedBar(id, labels, female, male)` | Grouped bar | Age/sex breakdown |
| `donutChart(id, d)` | Donut | Proportional breakdowns |

All chart canvases are addressed by ID. Chart IDs are constructed from a prefix + a suffix (e.g., `dd-fatal`, `imp-si`, `op-fsi`). The prefix/section mapping is in `prefixMap` inside the export functions.

---

## 6. Export System

Every CEA section exposes three export buttons: Excel, PDF, and JPG.

### SECTION_META

All export behavior is driven by `const SECTION_META`, defined separately from `DATA`. Each key corresponds to a section key and contains:

```javascript
SECTION_META = {
  overview: {
    title: "Statewide Overview",
    subtitle: "...",
    names: ["Fatalities", "Serious Injuries", "F+SI"],
    statCards: [ { label, value, note }, ... ],
    hasBreakdown: true   // full sections only
  },
  ...
}
```

> ⚠️ **Silent failure pattern:** If a section key exists in `DATA` but is missing from `SECTION_META`, the export buttons will render but do nothing. Always verify `SECTION_META` is populated when adding new sections.

### Excel export (`exportExcel(sectionId)`)

Builds a multi-sheet `.xlsx` workbook using the SheetJS library. Sheets included:
- Annual trend data (fatalities, serious injuries, F+SI)
- Breakdown sheets (full sections only): age/sex, month, day of week, time of day, crash type, speed limit
- A metadata sheet with section title, data range, and source information

### PDF export (`exportPDF(sectionId)`)

Builds a formatted PDF using jsPDF. Contents:
- Cover page with section title, SHSO header, and data range
- Stat cards rendered as a layout grid
- Charts captured from canvas elements via `addChartToPDF()`
- Resource links specific to the section
- Consistent header/footer on every page

### JPG export (`exportJPG(sectionId)`)

Captures the rendered section DOM element using the native Canvas API (not html2canvas). Returns a single flat image of the full section including stat cards and charts.

---

## 7. Glossary PDF

The `exportGlossaryPDF()` function builds a standalone formatted PDF of the 141-term SHSO Acronym Glossary. It does not depend on `SECTION_META`. Output includes:
- Cover page with SHSO header
- Two-column alphabetical term layout
- Matching page header and footer (agency name, date, page numbers)

---

## 8. Branding & Styling

BEACON follows Vermont CMO-aligned guidelines.

### Color palette (CSS variables)

| Variable | Value | Use |
|---|---|---|
| `--green-dark` | `#1b3d2b` | Header, section headers |
| `--green-mid` | `#2a5c3f` | Navigation |
| `--green-light` | `#3a7a55` | Positive indicators |
| `--gold` | `#c9973a` | Accent, borders |
| `--gold-light` | `#e8c87a` | Nav active state |
| `--off-white` | `#f4f3ef` | Page background |
| `--charcoal` | `#1e261e` | Body text |
| `--red` | `#b84141` | Alert indicators |

### Typography

- **Body:** Arial, Franklin Gothic Medium (sans-serif fallback stack)
- **Display/headers:** Franklin Gothic Heavy, Franklin Gothic Demi, Arial Black

### Logo

VTrans logo sourced from the official SHSO server:
`https://shso.vermont.gov/sites/ghsp/files/images/VTrans_Logo_RGB.png`

---

## 9. SharePoint Embedding

BEACON can be embedded in SharePoint using the **Embed web part**:

1. Add a new web part → search for **Embed**
2. Paste the BEACON URL (GitHub Pages or state-hosted URL)
3. Adjust the iframe height as needed (recommended: 900px+)

No Content Security Policy (CSP) modifications should be needed for read-only display. If SharePoint blocks CDN script loading, ADS may need to add `cdnjs.cloudflare.com` to the allowed external scripts list.

---

## 10. Known Fragility Patterns

These are patterns discovered through development that are important to preserve:

**Single-file integrity:** Full-file rewrites are high-risk. Even a small truncation or encoding issue during a save/upload can break the JavaScript silently. The safe update pattern is targeted string replacement of specific values, with before/after assertions.

**Function scope:** JavaScript functions must be declared at the top level of the script block. Functions declared inside other functions cause silent failures in some browsers and linting tools.

**Chart.js canvas reuse:** Chart canvases cannot be re-initialized without destroying the existing chart instance. If charts are rebuilt (e.g., on a dynamic reload), the existing `Chart` instance must be destroyed first.

**Export button without SECTION_META:** If `SECTION_META[sectionId]` is undefined, `exportExcel()`, `exportPDF()`, and `exportJPG()` all return silently. There is no visible error. Always check SECTION_META when adding sections.

**Section ID vs. key mismatch (Younger Drivers):** The section key is `"younger"` but the HTML element uses `id="young"`. This is handled in `prefixMap` and `names` lookup objects inside the export functions. Do not normalize without updating all references.

---

## 11. How to Update Data

Annual data updates (e.g., adding 2025 values) follow this process:

1. Pull updated counts from the Vermont Public Crash Query Tool and/or VTrans Data Unit
2. Cross-validate: monthly totals, daily totals, time-of-day totals, and age/sex totals must all reconcile against the FSI annual total within known unknowns
3. In `index.html`, locate `const DATA` and update each section's arrays — add the new year value to every array (`fatalities`, `serious`, `fsi`, and all breakdown sub-arrays)
4. Update `years` arrays to include the new year label
5. Update stat cards in `SECTION_META` to reflect the new 2024→2025 current-year value and recalculated averages/totals
6. Test all export functions after updating
7. Increment the version reference in the footer

See [`DATA-SOURCES.md`](DATA-SOURCES.md) for source links.
