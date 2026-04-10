---
title: DSEclimatekitaeExt
subtitle: Extension pipeline & accompying library to make pulling and plottind data from Cal-Adapt faster, easier and highkey more inuitive.
---

Uses  Cal-Adapt's [climakitae](https://github.com/cal-adapt/climakitae), which does do a good job of abstracting a lot of the fetching, processiing, and visualize downscaled CMIP6 climate data for most of the western US (with best resolutions supported only for california and very adjacent spaces).

## Features

- **Distributed data fetching** — Sends work to [Coiled](https://coiled.io) cloud workers that sit next to the data on S3. Workers open Zarr stores, subset by time and space, convert units, and return results. You don't download raw data to your laptop.

- **Powerful helper fucntions** - Instead of owning wokr across the whole stack, some helper functions only 

- **Fuzzy park search** — `ParkCatalog` searches across all 437 NPS units. Type "yosemite" or even "grand canion" and it finds the right park. No need to know exact names.



- **Full scenario coverage** — Historical Climate (1950-2014) plus three future scenarios (SSP 2-4.5, SSP 3-7.0, SSP 5-8.5) across 15 CMIP6 global climate models at 3km monthly resolution (LOCA2 statistical downscaling).

- **Spatial heatmaps** — 2x2 comparison plots showing how temperature or precipitation patterns change across scenarios within park boundaries. Anomaly plots show the difference from the historical baseline.

- **CSV export** — Clean CSVs with consistent column names (`simulation`, `time`, `scenario`, `T_Max`, etc.) that read straight into R with `read.csv()` or `readr::read_csv()`.

## Get Started

New here? Start with the [installation guide](getting-started/installation.md) to get the container running, then open the Tutorial notebook.

- [Installation](getting-started/installation.md) — Set up Docker + VS Code
- [Getting Started Overview](getting-started/index.md) — What to do first

## Tutorials

- Tutorial Walkthrough
- ParkCatalog Demo
- Full Data Extraction
- Spatial Comparison

## Getting Started with External Data

*Coming soon*

## Project Structure

*Coming soon*

## Advanced

- Pipeline (Luigi)
- Formal Python packaging
- WRF dynamical downscaling

## Links

- [GitHub Repository](https://github.com/Androo1213/dseNpsDataPipelineNew)
- [Cal-Adapt Analytics Engine](https://cal-adapt.org)
- [climakitae](https://github.com/cal-adapt/climakitae)
- [Coiled](https://coiled.io)
- [LOCA2 Downscaling](https://loca.ucsd.edu)
