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

## Flex — every California NPS unit, every main variable, all scenarios

[Flex](flex.ipynb) — pure scale-and-speed showcase. Geometry-filter every NPS unit in California off the mega layer, drop micro-units, spin a 45-worker Coiled cluster (with a proper `wait_for_workers` warmup plus per-park retry), fetch monthly `T_Max` + `T_Min` + `Precip` 1950-2100 across all four scenarios for every park in parallel, concat + compute `T_Avg`, then compute three real climatological aggregates per (park × SSP): **ΔT_Avg (°C)**, **ΔPrecip (%)**, and **year of emergence** (Hawkins & Sutton-style — first year the 10-yr-smoothed MMEM annual T_Avg exceeds the historical ceiling). No plots; the point is the summary table fills in during a coffee break. Baseline: ~36 hours on a MacBook to fetch one variable for one park; this notebook does 15 parks × 3 variables.
