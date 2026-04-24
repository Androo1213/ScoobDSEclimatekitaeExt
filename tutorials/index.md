---
title: Tutorials
---

## Tutorial Walkthrough

[Full tutorial notebook](tutorial-walkthrough.ipynb) — setup, Coiled configuration, library deep dive

## ParkCatalog Demo

[ParkCatalog Demo](parkcatalog-demo.ipynb) — fuzzy search across ~437 NPS units + `what_is_available()` to check what data exists for a given park

## Full Data Extraction

[Full Data Extraction](full-data-extraction.ipynb) — production workflow pulling T_Max / T_Min across all scenarios for Joshua Tree + Mojave, exports CSVs, computes T_Avg

## Spatial Comparison

[Spatial Comparison](spatial-comparison.ipynb) — 2x2 heatmaps comparing Historical vs SSP scenarios inside park boundaries, plus anomaly plots with a Historical reference panel

## Demo Template — any park, any variable

[Demo Template](demo-template.ipynb) — live-presentation harness. Three blanks to fill in on stage: a fuzzy search query, the exact park name you pick from the search, and a variable. Everything else is hardcoded (all 4 scenarios, monthly, 1995-2014 historical baseline, 2050-2069 future window). Runs `what_is_available()` live after you pick a park so the audience can see exactly what variables, timescales, and scenarios exist for it. Fetches time series + spatial snapshots in parallel on a small Coiled cluster and produces two plots: an annual-trajectory time series (ensemble spread + smoothing), and a 2×2 grid of three SSP anomaly maps with a Historical reference panel in the bottom-right. Validated locally for Yosemite × T_Max / T_Min / Precip.

## Flex — every California NPS unit, every main variable, all scenarios

[Flex](flex.ipynb) — pure scale-and-speed showcase. Geometry-filter every NPS unit in California off the mega layer, drop micro-units, spin a 45-worker Coiled cluster (with a proper `wait_for_workers` warmup plus per-park retry), fetch monthly `T_Max` + `T_Min` + `Precip` 1950-2100 across all four scenarios for every park in parallel, concat + compute `T_Avg`, then compute three real climatological aggregates per (park × SSP): **ΔT_Avg (°C)**, **ΔPrecip (%)**, and **year of emergence** (Hawkins & Sutton-style — first year the 10-yr-smoothed MMEM annual T_Avg exceeds the historical ceiling). No plots; the point is the summary table fills in during a coffee break. Baseline: ~36 hours on a MacBook to fetch one variable for one park; this notebook does 15 parks × 3 variables.

## Feather River: seasonal precipitation at the watershed's upper end

[Feather River seasonal precip](feather-river-seasonal-precip.ipynb) — library-first user story for the North Fork Feather headwaters. Walks through the probing chain (`search("feather river")` returns nothing confident → pivot to `search("lassen")` → `what_is_available("Lassen Volcanic National Park")` confirms full LOCA2 coverage), fetches monthly precipitation 1950-2100 across all four scenarios via a small Coiled cluster, reshapes to xarray, and uses the library's built-in meteorological-season handling (`time="QS-DEC"`) so DJF correctly straddles calendar years. Produces a monthly climatology plot (historical vs 2041-2060 mid-term), the full annual trajectory 1950-2100 with ensemble spread, a seasonal-totals bar chart, and a headline percent-change chart annotated with per-scenario model agreement (N of 70 simulations agreeing on sign of change). **Key finding**: the forced precipitation signal is small (~±5-8% per season) compared to ensemble spread (±15-25% across models), which is itself the result — most Mediterranean-regime precipitation change is noise-dominated rather than signal-dominated. Most robust pattern: **DJF gets wetter, SON gets drier** across all scenarios, with Winter signals consistent under SSP 3-7.0 (46 of 70 models agree on sign).

## Two Trees: Redwood vs Sequoia climate futures

[Two Trees climate futures](two-trees-climate.ipynb) — two NPS units named after the species they protect, two very different climate regimes. Redwood NP sits at 41°N on the Pacific coast (cool, fog-dependent); Sequoia NP sits at 36°N in the Sierra Nevada mid-elevation (cold winters, snowmelt-dependent). Fetches `T_Max` + `T_Min` + `Precip` for both, 1950-2100, all scenarios, uses `compute_t_avg` to derive mean temperature, then produces three plots in sequence: monthly climatology side-by-side (today's contrast), annual trajectories with 10-90 percentile ensemble bands (~1960-2100, using `annual_aggregate` + `smooth`), and a mid-term anomaly headline bar chart. **Key finding**: Sequoia warms ~0.5°C *more* than Redwood under every SSP (elevation-dependent warming; Pacific moderation buffers the coast). Includes a written-up **ensemble-mismatch gotcha** in the precipitation anomaly table — SSP 2-4.5 and SSP 5-8.5 pull from ~33-sim subsets of the 70-sim historical ensemble, producing artifactual ~50% decline numbers; SSP 3-7.0 uses a size-matched 62-sim subset and gives the physically plausible ~10% decline. Worth understanding before trusting any precipitation table computed this way.
