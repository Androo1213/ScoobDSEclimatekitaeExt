---
title: DSEclimatekitaeExt
subtitle: Extension pipeline & accompanying library to make pulling and plotting data from Cal-Adapt faster, easier and highkey more intuitive.
---

Uses  Cal-Adapt's [climakitae](https://github.com/cal-adapt/climakitae), which does do a good job of abstracting a lot of the fetching, processing, and visualize downscaled CMIP6 climate data for most of the western US (with best resolutions supported only for california and very adjacent spaces).

## Features

- **Distributed data fetching** — Sends work to [Coiled](https://coiled.io) cloud workers that sit next to the data on S3. Workers open Zarr stores, subset by time and space, convert units, and return results. You don't download raw data to your laptop.

- **Powerful helper functions** - Instead of owning work across the whole stack, some helper functions only request that you know variables, time scales, scenarios and parks you'd like data from. 

- **De(Myst)ification** - Actually don't even know what park you want? Or your wish of variable depends on what resolution and scenarios are available? Helper functions send live probes into CalAdapts zarr stores to confidently tell you what's available for where at resolution and over what time period. 

- **Fuzzy park search** — `ParkCatalog` searches across all 437 NPS units. Type "yosemite" or even "grand canion" and it finds the right park. No need to know exact names.

- **Full scenario coverage** — Historical Climate (1950-2014) plus three future scenarios (SSP 2-4.5, SSP 3-7.0, SSP 5-8.5) across 15 CMIP6 global climate models at 3km monthly resolution (LOCA2 statistical downscaling).

- **Spatial heatmaps** — 2x2 comparison plots showing how temperature or precipitation patterns change across scenarios within park boundaries. Anomaly plots show the difference from the historical baseline.

- **CSV export** — Clean CSVs with consistent column names (`simulation`, `time`, `scenario`, `T_Max`, etc.) that read straight into R with `read.csv()` or `readr::read_csv()`.

## Get Started

Need Setup walkthrough? Easiest to just start with with the [installation guide](getting-started/installation.md) to get the container running, then open the Tutorial notebook.

- [Installation](getting-started/installation.md) — Set up Docker + VS Code
- [Getting Started Overview](getting-started/index.md) — What to do first

## Tutorials

- [Tutorial Walkthrough](tutorials/tutorial-walkthrough.ipynb) — end-to-end setup, Coiled configuration, library deep dive
- [ParkCatalog Demo](tutorials/parkcatalog-demo.ipynb) — fuzzy search + `what_is_available()` live probing
- [Full Data Extraction](tutorials/full-data-extraction.ipynb) — production multi-scenario fetch for two parks, with CSV export
- [Spatial Comparison](tutorials/spatial-comparison.ipynb) — 2×2 heatmap grids + anomaly plots
- [Flex: all CA parks, all precip](tutorials/flex-all-ca-precip.ipynb) — the whole stack in ~50 lines, 45 workers, every NPS unit in California at once

## Library highlights

A few of the more interesting functions you'd reach for first. Full API in [`lib/andrewAdaptLibrary.py`](https://github.com/Androo1213/dseNpsDataPipelineNew/blob/main/lib/andrewAdaptLibrary.py).

- **`ParkCatalog.search("josh tree")`** — fuzzy name match across all 437 NPS units, returns ranked candidates (scores 0-100).
- **`ParkCatalog.what_is_available(unit_name)`** — live probe into Cal-Adapt Zarr stores that reports the exact variables, scenarios, timescales, and model count available *at this park's coordinates*. Hardcoded fast-path for the LOCA2 western-US box; live S3 probes for everything else.
- **`CatalogExplorer.summary()`** — cross-tab of `(variable × scenario)` simulation counts, built live from Cal-Adapt's catalog CSV. Good first sanity check before any fetch.
- **`get_climate_data(..., backend="coiled")`** — one call, dispatches a Dask task per `(variable × scenario)` to the cluster, returns a tidy DataFrame. Switch to `backend="direct_s3"` to stay local and get xarray DataArrays instead.
- **`get_spatial_snapshot()`** + **`plot_spatial_comparison()`** — fetch gridded snapshots (lat × lon preserved) and render a NxM heatmap grid with optional reference panel and anomaly coloring. Powers the [Spatial Comparison tutorial](tutorials/spatial-comparison.ipynb).
- **`annual_aggregate()`, `compute_anomalies()`, `smooth()`** — one-liners for the climatological ops that every study needs: monthly→annual with the right reducer per variable (sum for precip, mean for temp); absolute or percent anomaly vs a baseline; centered rolling mean to pull the forced signal out of interannual noise.
- **`export_to_csv()`** — dumps `get_climate_data` results to R-friendly CSVs with consistent column names and types.

The [Flex notebook](tutorials/flex-all-ca-precip.ipynb) is the most compact place to see most of these composed together.

## Getting Started with External Data

For most users, the library's defaults (LOCA2 3km monthly, 15 GCMs, 4 scenarios) cover the analysis. If you need something the library doesn't expose directly, three options:

- **Use a different timescale.** Pass `timescale="daily"` or `timescale="yearly"` to `get_climate_data` / `CatalogExplorer`. Daily data is ~30× larger than monthly — plan cluster size accordingly.
- **Switch to WRF (dynamical downscaling).** Pass `activity="WRF"` to access the 3km, 9km, or 45km WRF grids. The 45km grid extends nationwide (good for parks outside the LOCA2 western-US box). See the [WRF section on the Advanced page](advanced/index.md#wrf-dynamical-downscaling).
- **Drop down to `fetch_direct_s3()`.** The coiled backend is a convenience wrapper; if you want raw xarray DataArrays to do your own spatial/temporal processing, `fetch_direct_s3(var_key, experiment, time_slice, lat_bounds, lon_bounds)` skips the Dask layer and returns the lazy DataArray directly. Good for custom analyses that don't fit the tidy-DataFrame-per-scenario shape.

For data sources outside Cal-Adapt (ERA5, PRISM, CHIRPS, etc.), the library doesn't currently have adapters — contributions welcome. The `preprocess()` / `spatial_average()` / `annual_aggregate()` helpers are source-agnostic once you have an xarray DataArray, so most of the pipeline transfers.

## Project Structure

```
dseNpsDataPipelineNew/
├── lib/
│   └── andrewAdaptLibrary.py         # the whole library (single file, ~2,000 lines)
├── notebooks/
│   └── python/
│       ├── Tutorial_Coiled_Setup.ipynb
│       ├── ParkCatalog_Demo.ipynb
│       ├── Full_Data_Extraction.ipynb
│       ├── Spatial_Comparison.ipynb
│       └── Flex_All_CA_Precip.ipynb
├── USA_National_Park_Service_Lands_*/   # mega NPS shapefile (437 units)
├── data/csv/                            # output CSVs from runs
├── docker/                              # container setup
└── environment.yml                      # conda env for local / Binder use
```

Two branches worth knowing about on the same repo:

- **`luigi-pipeline`** — adds `pipelines/` with a resumable CLI for multi-scenario extractions. See [Advanced → Luigi Pipeline](advanced/index.md#luigi-pipeline).
- **`formal-package`** — moves `lib/andrewAdaptLibrary.py` into a pip-installable `src/dse_climate/` package with `pyproject.toml`. See [Advanced → Formal Python Packaging](advanced/index.md#formal-python-packaging).

## Advanced

- [Luigi Pipeline](advanced/index.md#luigi-pipeline) — resumable multi-scenario CLI extraction
- [Formal Python Packaging](advanced/index.md#formal-python-packaging) — `pip install`-able version of the library
- [WRF Dynamical Downscaling](advanced/index.md#wrf-dynamical-downscaling) — curvilinear grid, 3 resolutions, nationwide coverage
- [New Team Coiled Setup](advanced/index.md#new-team-coiled-setup) — adopting this stack for your own group

## Links

- [GitHub Repository](https://github.com/Androo1213/dseNpsDataPipelineNew)
- [Cal-Adapt Analytics Engine](https://cal-adapt.org)
- [climakitae](https://github.com/cal-adapt/climakitae)
- [Coiled](https://coiled.io)
- [LOCA2 Downscaling](https://loca.ucsd.edu)
