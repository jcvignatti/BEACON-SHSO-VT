# BEACON
### Baseline Evidence & Crash Outreach Navigator
**Vermont Highway Safety Office (SHSO) · Vermont Agency of Transportation (VTrans)**

---

[![Live Tool](https://img.shields.io/badge/Live%20Tool-BEACON-2a5c3f?style=for-the-badge)](https://jcvignatti.github.io/beacon/)
[![Data Range](https://img.shields.io/badge/Data-2020–2024-c9973a?style=for-the-badge)](#)
[![CEA Sections](https://img.shields.io/badge/CEA%20Sections-10-1b3d2b?style=for-the-badge)](#)

---

## What is BEACON?

BEACON is a single-page data companion tool built by the Vermont SHSO to support education grantees and partners working through the Section 402 NOFO application process. It presents verified Vermont crash data organized by Critical Emphasis Area (CEA), formatted specifically for grantees writing program justifications.

It requires no login, no installation, and no technical background to use.

---

## What it covers

Ten CEA-aligned sections, each with:

| Section | Type |
|---|---|
| Statewide Overview | Full |
| Distracted Driving | Full |
| Impaired Driving | Full |
| Occupant Protection | Full |
| Speeding & Aggressive Driving | Full |
| Older Drivers | Lite |
| Motorcyclists | Lite |
| Younger Drivers | Lite |
| Pedestrians | Lite |
| Bicyclists | Lite |

**Full sections** include four stat cards (2024 value, 5-year average, 5-year total, trend), three Chart.js combo charts (fatalities, serious injuries, F+SI combined), and an expandable breakdown panel covering age & sex, month, day of week, time of day, crash type, and posted speed limit.

**Lite sections** include four stat cards and three charts. Breakdown panels are omitted where breakdown data is limited.

**Additional features:**
- Curated Resources section with links to NHTSA Traffic Safety Facts, FARS, Vermont Public Crash Query Tool, and VTrans Power BI dashboards
- 141-term SHSO Acronym Glossary, exportable as a formatted PDF
- Export to Excel, PDF, and JPG per CEA section

---

## Data integrity

All values — including averages, totals, trend directions, and breakdown subtotals — have been mathematically cross-validated. Monthly, daily, time-of-day, and age/sex breakdowns reconcile against FSI totals within expected unknowns. Data range: 2020–2024.

---

## Architecture

One HTML file. No backend, no database, no server calls, no user data collected. Runs entirely in the browser. Straightforward to embed in SharePoint or host on any state web infrastructure.

See [`DOCUMENTATION.md`](DOCUMENTATION.md) for full technical detail.

---

## Data sources

See [`DATA-SOURCES.md`](DATA-SOURCES.md) for the full list of sources and update process.

---

## Deployment status

BEACON was built to get a functional, data-accurate tool into grantees' hands before the NOFO deadline. It has been carefully built and internally reviewed. Formal multi-department testing, accessibility review, and IT security vetting are in progress through Vermont Agency of Digital Services (ADS).

---

## Contact

**JC Vignatti** — Vermont SHSO, Vermont Agency of Transportation
[LinkedIn](https://www.linkedin.com/in/jcvignatti/)

**Evelyn McFarlane** — SHSO Program Manager & Records Officer
[Evelyn.McFarlane@vermont.gov](mailto:Evelyn.McFarlane@vermont.gov)

---

## Version

Current: **v37** | See [`CHANGELOG.md`](CHANGELOG.md) for full history.
