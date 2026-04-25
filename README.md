# MBTA Transit Equity Explorer

Interactive, frontend-only web demo that cross-references MBTA bus service with neighborhood demographics across the Boston metro area.

**Live demo:** https://jakobzhao.github.io/mbta_equity/equity.html
**v1 (OTP only):** https://jakobzhao.github.io/mbta_equity/

## What it shows

Two pages:

- **`equity.html`** — pick any pairing of neighborhood variable × transit outcome and see the correlation, scatter plot, map, and Justice40 comparison.
- **`index.html`** — simpler first version showing MBTA on-time performance vs. ACS median income.

### Neighborhood dimensions (x-axis)
- Median household income
- % zero-vehicle households
- % non-white / non-Hispanic (BIPOC)
- % workers with 60+ min commute
- % workers commuting by transit

### Transit outcome dimensions (y-axis)
- On-time performance (MBTA OTP, Sep–Nov 2025)
- Weekday bus trips served (frequency, from GTFS)
- Mean daily service span (hours)
- Jobs reachable by direct bus ride (LEHD LODES proxy)

### Justice40 overlay
165 CEJST v1.0 "disadvantaged" tracts are highlighted with red borders on the map and red markers on the scatter plot.

## Data sources

| Data | Source | Notes |
|---|---|---|
| MBTA bus reliability | [MassDOT ArcGIS Feature Service](https://services1.arcgis.com/ceiitspzDAHrdGO1/arcgis/rest/services/MBTA_Rail_and_Bus_Reliability/FeatureServer) | Aggregated 2025-09-01 to 2025-11-29 |
| Bus routes, stops, schedules | [MBTA GTFS](https://cdn.mbta.com/MBTA_GTFS.zip) | Spring 2026 feed |
| Tract boundaries | US Census TIGER 2022 cartographic boundaries | Suffolk, Middlesex, Norfolk, Essex counties |
| Demographics / commute | US Census ACS 5-year 2022 | B19013, B25044, B03002, B08301, B08303 |
| Jobs | LEHD LODES v8 WAC 2021 | Tract-level aggregation |
| Justice40 disadvantaged tracts | [CEJST v1.0](https://web.archive.org/web/20231021120320id_/https://static-data-screeningtool.geoplatform.gov/data-versions/1.0/data/score/downloadable/1.0-communities.csv) | Retrieved via Wayback Machine (official site archived in 2025) |

## Stack

- **Map**: MapLibre GL JS 4.7 + OpenStreetMap raster tiles (no API key)
- **Charts**: Plotly.js 2.35
- **Data**: static GeoJSON / JSON in `data/`, pre-processed with Python (GeoPandas, pandas)
- **Deployment**: GitHub Pages — pure static, no backend

## Key findings

The naive "poor neighborhoods get worse bus service" hypothesis does **not** hold in Boston along the dimensions we measured:

| Metric | DAC (Justice40) mean | Non-DAC mean |
|---|---|---|
| Median income | $66k | $129k |
| Weekday bus trips (tract) | **967** | 575 |
| Service span | **18.5 h** | 17.2 h |
| On-time performance | **67.1%** | 64.7% |
| Jobs reachable (direct ride) | **170k** | 126k |
| % workers 60+ min commute | **14.5%** | 12.0% |

MBTA service is heavily concentrated in low-income urban cores — more trips, longer hours, slightly better punctuality. **Yet** residents of disadvantaged tracts still have longer commutes. That gap is the real equity question: service volume ≠ realized accessibility. The next step is isochrone-based 30-minute accessibility analysis using OpenTripPlanner / r5py.

## Structure

```
demo/
├── equity.html       # main page
├── index.html        # v1 — OTP × income only
├── data/
│   ├── routes.geojson    # 142 bus routes + OTP
│   ├── tracts.geojson    # 730 tracts + all variables
│   └── summary.json      # headline stats
└── README.md
```

## Limitations

- Job accessibility is a **direct-ride proxy**, not a true 30-minute isochrone. It does not account for transfers, walk access, headway-based waiting, or non-MBTA modes.
- Reliability = schedule/headway adherence only. Does not measure crowding, cancellations in detail, or service changes that widened headways to improve on-time performance.
- CEJST v1.0 uses 2010 tract GEOIDs; they mostly align with 2020 tracts but a small number of tract splits are not accounted for.
- "Weekday service" is a single-date snapshot (2026-05-06, a typical Wednesday).

## Credits

Built for a Boston-area transit equity research project. Data pipelines and visualization by Bo Zhao (github: [jakobzhao](https://github.com/jakobzhao)).
