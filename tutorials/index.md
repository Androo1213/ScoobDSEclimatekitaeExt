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

## Flex — All California Parks, All Precip

[Flex: all CA parks, all precip](flex-all-ca-precip.ipynb) — the whole stack in ~50 lines: geometry-filter every NPS unit in California off the mega shapefile, probe each with `_inside_loca2`, spin a 45-worker cluster, fetch monthly Precip 1950–2100 across all 4 scenarios in parallel via a ThreadPoolExecutor, aggregate into one frame, plot parks ranked by historical annual precip. End-to-end cloud wall-clock ~7 min vs ~13 hours on a laptop; ~2 TB of LOCA2 chunks chomped through in the cloud
