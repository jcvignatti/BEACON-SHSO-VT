# BEACON — Data Sources

**Vermont SHSO · Vermont Agency of Transportation**

All crash data in BEACON covers the years **2020–2024** and applies to **all Vermont counties** unless otherwise noted.

---

## Primary Sources

### Vermont Public Crash Query Tool
The primary source for Vermont-specific FSI (Fatalities and Serious Injuries) counts, including all breakdown dimensions (age/sex, month, day of week, time of day, crash type, posted speed limit).

Maintained by the VTrans Data Unit. Access is available to SHSO staff and authorized partners.

**URL:** [Vermont Public Crash Query Tool](https://vtrans.vermont.gov/highway/traffic-research/crash-data)

---

### NHTSA FARS (Fatality Analysis Reporting System)
Used for fatality-specific validation and federal-level comparisons. FARS is the national census of fatal motor vehicle crashes.

**URL:** [https://www.nhtsa.gov/research-data/fatality-analysis-reporting-system-fars](https://www.nhtsa.gov/research-data/fatality-analysis-reporting-system-fars)

---

### NHTSA Traffic Safety Facts
Used for state-level summaries and national context. Annual state fact sheets are published by NHTSA for each state.

**URL:** [https://www.nhtsa.gov/research-data/traffic-safety-facts](https://www.nhtsa.gov/research-data/traffic-safety-facts)

---

### VTrans Power BI Dashboards
Supplementary reference for trend visualization and internal validation. Links to specific dashboards are embedded in the BEACON Resources section.

**Maintained by:** VTrans Data Unit

---

## VTrans Data Unit

The VTrans Data Unit is the authoritative internal source for Vermont crash data and is the recommended point of contact for data questions, validation requests, or access to raw crash records.

Contact through the Vermont Agency of Transportation:
[https://vtrans.vermont.gov](https://vtrans.vermont.gov)

---

## Data Validation Process

All values in BEACON — including 5-year averages, 5-year totals, trend directions, and breakdown subtotals — were mathematically cross-validated before publication:

- Annual FSI totals were verified against source query outputs
- Breakdown sub-totals (monthly, daily, time-of-day, age/sex) were reconciled against annual FSI totals
- Discrepancies were attributed to records with unknown or missing values, which is consistent with standard crash data reporting
- Averages and totals were computed independently and checked against displayed values

---

## Annual Update Checklist

When updating BEACON for a new data year:

- [ ] Pull updated FSI counts from Vermont Public Crash Query Tool for all 10 CEA categories
- [ ] Pull breakdown distributions (age/sex, month, day of week, time of day, crash type, speed limit) for the 5 full sections
- [ ] Validate new-year totals against FARS fatality counts
- [ ] Cross-validate all breakdown subtotals against annual FSI totals
- [ ] Update `const DATA` arrays in `index.html` (add new year, drop oldest if maintaining 5-year window, or extend)
- [ ] Update `SECTION_META` stat card values (current-year value, recalculated average, recalculated total, updated trend)
- [ ] Test all export functions (Excel, PDF, JPG) for each section after update
- [ ] Increment version number in footer
- [ ] Obtain data sign-off from SHSO Records Officer before publishing

---

## Data Contact

**Evelyn McFarlane** — SHSO Program Manager & Records Officer (data sign-off authority)
[Evelyn.McFarlane@vermont.gov](mailto:Evelyn.McFarlane@vermont.gov)
