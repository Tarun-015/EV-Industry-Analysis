# India EV Charging Infrastructure Intelligence

Geographic coverage, demand-supply mismatch, and expansion-priority analysis of India's public EV charging network — built from real government and crowdsourced data, not synthetic samples.

**[Read the full report (DOCX)](./EV_Charging_Infrastructure_India_Report.docx)** · **[Explore the Excel model](./EV_Charging_Infrastructure_India.xlsx)**

---

## TL;DR

- India's public charging network grew **5,151 → 29,277 stations (Dec 2022 → Aug 2025)**, per the Ministry of Power / PIB.
- **Uttar Pradesh carries ~6x Karnataka's demand pressure per station** (172 vs 28 new EVs sold per existing charger in 2025) — the clearest single expansion priority in the dataset.
- **Crowdsourced platforms (OpenChargeMap, and by extension most OSM-linked apps) cover only ~4% of India's real network**, and the coverage bias runs backwards from intuition — Kerala looks well-served on free map data and is actually mid-pack nationally by real station count.
- **Rajasthan has the weakest fast-DC share of any state with meaningful sample size (5.9%)**, despite a respectable total station count — a highway-corridor gap invisible from total counts alone.
- Found and fixed a live data-quality bug in the raw crowdsourced dataset: a single-port site listed at **1,000,000 kW** (~2,500x the most powerful real DC charger in India).

---

## Why this project exists

Most public "EV charging in India" analyses do one of two things: repeat the official PIB total with no granularity, or plot crowdsourced map data as if it were the whole network. Neither is right on its own. This project deliberately triangulates **three independently sourced datasets** — geolocated site data, official government counts, and state EV sales — specifically to expose where those two pictures diverge, and to quantify demand pressure rather than just describing supply.

---

## Repo structure

```
.
├── README.md
├── EV_Charging_Infrastructure_India.xlsx      # 6-sheet model, 1,270+ live formulas, 0 recalc errors
├── EV_Charging_Infrastructure_India_Report.docx  # Full written report
└── data/
    ├── india_charging_stations_clean.csv      # 1,178 cleaned OCM site records
    ├── state_summary_ocm.csv                  # Aggregated state-level OCM stats
    └── state_gap_analysis.csv                 # Official vs. OCM vs. EV-sales gap table
```

### Excel workbook — sheet by sheet

| Sheet | What it does |
|---|---|
| `README` | Data sources and known limitations, inside the workbook itself |
| `OCM_Raw_Data` | 1,178 geolocated public charging sites (id, name, city, state, lat/long, ports, power, fast-DC flag) |
| `State_Summary` | State-level rollups computed live with `COUNTIFS` / `SUMIFS` / `AVERAGEIFS` against the raw sheet — not hardcoded |
| `Official_vs_Crowdsourced` | PIB official station counts vs. OCM-mapped counts, by state, with coverage % |
| `Demand_Supply_Gap` | EV sales volume vs. station count, by state, with a stations-per-vehicle stress ratio |
| `Recommendations` | Five prioritized, evidence-linked recommendations |

Every formula recalculates from the raw data sheet — change a row in `OCM_Raw_Data` and every downstream number updates. Verified with a full LibreOffice recalculation pass (0 errors across 1,274 formulas).

---

## Data sources

| Source | What it provides | Link |
|---|---|---|
| OpenChargeMap API (via [tarekmasryo/global-ev-infra-dataset](https://github.com/tarekmasryo/global-ev-infra-dataset), CC BY-SA 4.0) | 1,178 geolocated India charging sites: lat/long, ports, power rating, fast-DC flag | GitHub |
| Press Information Bureau, Ministry of Heavy Industries — PRID 2154127 (8 Aug 2025) | Official national + top-7-state public EVCS counts, year-wise growth 2022→2025 | [pib.gov.in](https://www.pib.gov.in/PressReleasePage.aspx?PRID=2154127) |
| PIB / e-Vahan portal — PRID 2101635 | Cumulative national EV registrations | pib.gov.in |
| Industry reporting (Deccan Herald, Navionyx/Vahan-derived) | State-level 2025 EV sales volumes, cross-checked against Vahan-sourced figures | see report footnotes |

## Data cleaning performed

- Removed 1 OCM record with an obviously corrupted power rating (1,000,000 kW, 1-port site)
- Canonicalized 10+ inconsistent state-name variants in raw OCM free-text (`"Keraka"`, `"Mahrashtra"`, `"KA"`, `"Tamil Nasdu"` → standard names); dropped 10 unresolvable rows
- Cross-validated OCM's implied station counts against PIB's official counts rather than presenting crowdsourced totals as the national picture

## Limitations (stated plainly, not buried)

- OCM's crowdsourced coverage bias means every OCM-derived percentage (fast-DC share, avg power, ports/site) reflects the sites that happen to be *mapped*, which skew toward Kerala, Karnataka, and Tamil Nadu — not the true national distribution.
- State EV sales data was only available, from a citable public source, for 3 of 36 states/UTs (Karnataka, Maharashtra, Uttar Pradesh). No figures are estimated or interpolated for the rest.
- PIB's official state-wise breakdown, as sourced here, covers only the top 7 states; the remaining ~10,000 stations are not attributed to specific states in any source found during this research.
- No highway-corridor gap analysis (distance between consecutive fast chargers) — the lat/long fields exist to do this, but given OCM's ~4% national coverage, a corridor map built on it alone would be confidently wrong without ground-truthing against official highway-charger lists.

---

## Tech stack

`Python` (pandas) for data cleaning and aggregation · `openpyxl` for the Excel model (formulas, conditional formatting, charts) · `docx` (Node) for the written report · Data sourced via public API / government press releases, not synthetic or scraped-without-attribution.

## License

Underlying OpenChargeMap data is CC BY-SA 4.0 (© Open Charge Map, processed by Tarek Masryo, CC BY 4.0). PIB and e-Vahan data is Government of India open data, licensed under the [Government Open Data License – India](https://data.gov.in/Godl). This analysis and all derived files: MIT.
