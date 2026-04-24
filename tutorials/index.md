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

## Flex — every California NPS unit, all temperature data

[Flex](flex.ipynb) — the whole stack composed in ~60 lines of actual code. Geometry-filters every NPS unit whose centroid sits inside a hand-crafted California polygon, drops micro-units too small for the 3 km LOCA2 grid, spins a 45-worker Coiled cluster, fetches monthly `T_Max` and `T_Min` across all 4 scenarios for 1950–2100 in parallel via a `ThreadPoolExecutor`, concatenates + computes `T_Avg`, and plots parks ranked by historical mean `T_Max`. End-to-end cloud wall-clock on the order of ~7 min vs ~hours-to-days on a laptop; full cost + timing diagnostics print at the end of the notebook.
