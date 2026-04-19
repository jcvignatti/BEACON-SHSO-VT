# BEACON — Changelog

**Vermont SHSO · Vermont Agency of Transportation**

This is the full development history of BEACON across 37 iterations. Entries are grouped by phase rather than listed individually where multiple iterations addressed the same area.

---

## v37 — Production Release (Current)
*Final production-hardening pass before ADS outreach and public deployment.*

- Drafted and finalized formal ADS partnership email covering BEACON's capabilities, pre-NOFO build rationale, and five specific ADS asks (review/testing, cross-department feedback, maintenance model, permanent state URL, future enhancements roadmap)
- Confirmed all SRI integrity hashes active on jsPDF and xlsx CDN tags
- Added production note in source comments for generating Chart.js SRI hash via `openssl`

---

## v36 — Production Hardening & Bug Fixes

**CDN and dependency cleanup:**
- Removed dead Cloudflare dependency that had been silently failing
- Removed orphaned CSS rules referencing the removed dependency
- Upgraded all remaining `http://` links to `https://`
- Added SRI `integrity` hashes to jsPDF (2.5.1) and xlsx (0.18.5) script tags via npm hash verification

**JavaScript quality fixes:**
- Removed anti-pattern: function declared inside another function (caused silent failures in strict environments)
- Resolved broken glossary PDF logic that prevented export in some browser contexts
- Restored Evelyn McFarlane's email address in the footer, which had been mangled by a Cloudflare script tag interaction in a previous version

**Data and copy fixes:**
- Corrected BEACON acronym expansion in footer and PDF exports ("Baseline Evidence & Crash Outreach Navigator")
- Updated stale agency name references from "Vermont AOT" to "Vermont VTrans" throughout
- Standardized en-dash (–) formatting across all stat card notes and section descriptions

**Critical bug fix — Younger Drivers exports:**
- Identified and resolved a silent section ID mismatch: the section key `"younger"` was being looked up against an HTML element with `id="young"`, causing all three export buttons (Excel, PDF, JPG) to silently do nothing
- Fixed by adding explicit `id` handling in `prefixMap` and `names` lookup objects

---

## v35 — Glossary PDF Feature

- Built `exportGlossaryPDF()` function generating a standalone formatted PDF of the 141-term SHSO Acronym Glossary
- Implemented two-column alphabetical layout with consistent page headers and footers matching the CEA section PDF style
- Added Glossary export button to the Resources section
- Validated glossary term count and alphabetical ordering

---

## v34 — Export System: Lite Section Coverage

*Previously, the five "lite" sections (Older Drivers, Motorcyclists, Younger Drivers, Pedestrians, Bicyclists) had export buttons that appeared in the UI but silently failed because SECTION_META entries were missing.*

- Added `SECTION_META` entries for all five lite sections: older, motorcycle, younger, pedestrian, bicycle
- Implemented conditional export logic: lite sections without breakdown data export Excel files with annual trend sheets only (no breakdown sheets), and PDFs without breakdown chart pages
- Verified Excel, PDF, and JPG exports for all five lite sections
- Resolved the pattern where missing SECTION_META caused buttons to render but produce no output

---

## v30–v33 — Data Refresh: Full DATA Object Update

*Systematic refresh of all crash data to include 2020–2024 values.*

- Updated `const DATA` for all 10 CEA sections: Statewide Overview, Distracted Driving, Impaired Driving, Occupant Protection, Speeding & Aggressive Driving, Older Drivers, Motorcyclists, Younger Drivers, Pedestrians, Bicyclists
- Recalculated 5-year averages and totals for all stat cards
- Updated trend direction indicators (↑/↓/→) based on 2023→2024 year-over-year change
- Cross-validated all breakdown sub-totals (monthly, daily, time-of-day, age/sex) against FSI annual totals for the 5 full sections
- Attributed residual discrepancies to unknown/missing values in source data, consistent with standard crash data reporting

---

## v25–v29 — Export System: Full Section Coverage

- Built `exportExcel(sectionId)` producing multi-sheet workbooks per section
- Built `exportPDF(sectionId)` producing formatted PDFs with stat cards, charts, and resource links
- Built `exportJPG(sectionId)` producing flat image captures of section DOM
- Implemented `addChartToPDF()` helper to capture Chart.js canvas elements into PDF pages
- Removed html2canvas dependency; replaced with native Canvas API for JPG capture
- Validated all export functions for the 5 full sections

---

## v20–v24 — Dig-Deeper Breakdown System

- Built `toggleDigDeeper(prefix)` toggle system for expandable breakdown panels
- Implemented 6 breakdown chart types per full section: age/sex (grouped bar), month (bar), day of week (bar), time of day (bar), crash type (donut), speed limit (bar)
- Added `groupedBar()`, `donutChart()`, and additional `barChart()` builder functions
- Separated `buildCharts()` (full) from `buildChartsLite()` (lite) chart initialization paths
- Integrated breakdown data into `const DATA` structure for 5 full sections

---

## v15–v19 — Section Layout & Navigation

- Built all 10 CEA section HTML layouts with consistent structure: section header, stat grid, chart row, dig-deeper toggle
- Implemented sticky navigation bar with section anchor links and active-state highlighting
- Added hero banner with CEA label, title, description, and data range pills
- Built Resources section with curated external links
- Built SHSO Acronym Glossary section (141 terms, alphabetical, searchable)

---

## v10–v14 — Chart System & Data Architecture

- Established `const DATA` structure and populated initial values for all 10 sections
- Built `comboBar()` chart function for fatalities/serious injuries/F+SI trend visualization using Chart.js 4.4.1
- Implemented Chart.js CDN loading with crossorigin attribute
- Added jsPDF and xlsx CDN dependencies for export system groundwork
- Defined `SECTION_META` structure as the driver for all export operations

---

## v5–v9 — Branding & Design System

- Implemented Vermont CMO-aligned color palette via CSS custom properties
- Set typography stack: Arial and Franklin Gothic for body; Franklin Gothic Heavy/Demi/Arial Black for display
- Built stat card component with color-coded top-border indicators (gold/red/green/gray)
- Built section header component with gradient background and CEA tag badge
- Integrated VTrans logo from official SHSO server
- Implemented responsive grid layouts for stat cards and chart rows

---

## v1–v4 — Initial Concept & Single-File Architecture

- Established project scope: a pre-NOFO data companion for Vermont Section 402 education grantees
- Decided on single-file HTML architecture: no backend, no database, browser-only, SharePoint-embeddable
- Selected Chart.js, jsPDF, and xlsx as the CDN library stack
- Built initial HTML skeleton with header, navigation, and one prototype CEA section (Statewide Overview)
- Confirmed Vermont Public Crash Query Tool and FARS as primary data sources
- Aligned section list to Vermont SHSO Critical Emphasis Areas

---

## Notes on development approach

BEACON was built iteratively with an emphasis on data integrity at every step. The guiding principles that shaped decisions across all 37 versions:

- **Data accuracy first.** No value was published without cross-validation against source totals.
- **Single-file discipline.** The constraint of one HTML file was a feature, not a limitation — it keeps deployment simple and eliminates infrastructure dependencies.
- **Targeted edits over full rewrites.** After early versions established that full-file regeneration introduced subtle breakage, all later changes were applied as discrete, scoped string replacements with explicit before/after verification.
- **Silent failures are the hardest bugs.** Several issues in this project produced no visible error — missing SECTION_META keys, section ID mismatches, function scope violations. Systematic audit passes were used to surface these.
