---
title: Advanced
---

Short tour of the parts of the stack that don't live on the main branch yet. Each section links to the branch where the code actually is.

## Luigi Pipeline

Resumable CLI extraction. Branch: [`luigi-pipeline`](https://github.com/Androo1213/dseNpsDataPipelineNew/tree/luigi-pipeline).

For a run like *"T_Max + T_Min across all 4 scenarios for Yosemite"*, the same work that would take one big `get_climate_data` call in a notebook becomes a small Luigi task graph:

```
ExtractionPipeline (WrapperTask)
  ├── FetchClimateTask(T_Max, Historical)         → JoshuaTree/T_Max_Historical.csv
  ├── FetchClimateTask(T_Max, SSP 2-4.5)          → JoshuaTree/T_Max_SSP_2-4.5.csv
  ├── FetchClimateTask(T_Min, Historical)         → JoshuaTree/T_Min_Historical.csv
  ├── FetchClimateTask(T_Min, SSP 2-4.5)          → JoshuaTree/T_Min_SSP_2-4.5.csv
  └── ComputeTAvgTask(SSP 2-4.5)                  → JoshuaTree/T_Avg_SSP_2-4.5.csv
        ├── requires: T_Max SSP 2-4.5 CSV
        └── requires: T_Min SSP 2-4.5 CSV
```

Each `FetchClimateTask` wraps one call to `get_climate_data()`; `ComputeTAvgTask` is pure local math (no Coiled). The pipeline is invoked from the terminal with fuzzy park search built in — `--park "yosemit"` resolves to Yosemite National Park automatically.

**Where it earns its keep:** mid-run interruptions. Luigi checks whether each task's output CSV already exists before doing work, so if your laptop disconnects after Historical + SSP 2-4.5 finish, re-running the same command picks up at SSP 3-7.0. Same story if you fetched T_Max last week and now want T_Min — it only does the new combos.

**What it doesn't do:** resume *within* a single fetch. A `FetchClimateTask` either produces a complete CSV or it doesn't — if a Coiled worker dies mid-fetch, the whole task reruns. So Luigi is a "big multi-scenario extraction" tool, not a mid-cluster-crash safety net.

## Formal Python Packaging

Pip-installable version of the library. Branch: [`formal-package`](https://github.com/Androo1213/dseNpsDataPipelineNew/tree/formal-package).

Moves the single-file `lib/andrewAdaptLibrary.py` (which requires a `sys.path.insert` trick) to a proper `src/`-layout package called `dse-climate`, with `pyproject.toml` declaring dependencies, optional extras, and build config:

```toml
[project]
name = "dse-climate"
version = "0.1.0"
requires-python = ">=3.10"
dependencies = ["xarray", "numpy", "pandas", "geopandas", "rioxarray",
                "fsspec", "shapely", "matplotlib"]

[project.optional-dependencies]
coiled   = ["coiled", "dask", "distributed"]
climate  = ["climakitae @ https://github.com/cal-adapt/climakitae/archive/refs/tags/1.4.0.zip"]
all      = ["dse-climate[coiled,climate]"]
```

Install directly from GitHub:

```bash
pip install "git+https://github.com/Androo1213/dseNpsDataPipelineNew.git@formal-package#egg=dse-climate[all]"
```

Then `from dse_climate import ParkCatalog, get_climate_data, ...` anywhere — no more `sys.path.insert` at the top of every notebook. The public API (`CatalogExplorer`, `ParkCatalog`, `get_climate_data`, `get_spatial_snapshot`, `annual_aggregate`, `compute_anomalies`, `smooth`, etc.) is explicit in `src/dse_climate/__init__.py`.

## WRF Dynamical Downscaling

LOCA2 is *statistical* downscaling — it takes coarse GCM output and pattern-matches it onto a 3km grid using historical observations. WRF is *dynamical* — it runs a full atmospheric physics simulation at high resolution. Both are accessible through the same library, via the `activity=` kwarg on `get_climate_data()` and `CatalogExplorer()`.

What's different about WRF in the library:

- **Curvilinear grid** — WRF outputs are on a Lambert Conformal projection: `lat` and `lon` are 2D coordinate arrays indexed by `(y, x)` dims, not 1D dimension coordinates. Regular `.sel(lat=..., lon=...)` doesn't work. [`build_wrf_coiled_task()`](https://github.com/Androo1213/dseNpsDataPipelineNew/blob/main/lib/andrewAdaptLibrary.py) handles this via boolean masking against the 2D coordinate arrays.
- **Three resolutions** — `d03` (3km, CA), `d02` (9km, regional), `d01` (45km, continental). Each has different coverage — d01 reaches the eastern US where LOCA2 doesn't, which is why `ParkCatalog.what_is_available()` falls back to live-probing WRF stores for parks outside the LOCA2 box.
- **Different variable IDs** — `t2max` instead of `tasmax`, `prec` instead of `pr` — mapped through `WRF_VARIABLE_MAP`. You still use `"T_Max"`, `"Precip"` etc. as the user-facing names.
- **Smaller ensemble** — 9 GCMs, not 15 (WRF is expensive to run, so fewer groups have released it).

The right task builder (LOCA2 vs WRF) is selected automatically from the `activity` argument — you don't touch curvilinear-grid logic directly.

## New Team Coiled Setup

The tutorials assume you're in the DSE lab's Coiled workspace. For a new team adopting this stack, the setup is:

1. **Coiled account** — sign up at [coiled.io](https://coiled.io), create a workspace for your team. Free tier is enough to start.
2. **Cloud backend** — Coiled supports AWS, GCP, and Azure. We use GCP because Cal-Adapt's Zarr stores are on AWS S3 in `us-west-2`; GCP workers still have fast access, and the lab's account was already on GCP. AWS workers in `us-west-2` would be slightly lower latency if you're starting fresh.
3. **Quotas** — the default per-region quotas on new cloud projects are too tight for a 32- or 45-worker cluster. On GCP specifically, bump:
   - **Instances** → 50+ (default 24)
   - **In-use regional external IPv4 addresses** → 50+ (default 8)
   - **Persistent Disk SSD** → 2,000 GB (default 500)
   - CPUs is usually fine at the default 200
4. **Authenticate** — `coiled login` once per container, browser flow. Credentials persist in `~/.config/coiled/`.
5. **Pick a region** — pass `region=` to `coiled.Cluster(...)`. `us-central1` has been the most reliable for spot capacity; `us-west1` runs out of `n1-highmem-4` instances frequently at peak hours.
6. **Smoke test** — run the [tutorial walkthrough](../tutorials/tutorial-walkthrough.ipynb) with a small cluster (4 workers) first. If that works end-to-end, scale up.

### See it in action

The [Flex notebook](../tutorials/flex.ipynb) is the most concise showcase of what the full stack buys you — it pulls monthly `T_Max` and `T_Min` for every NPS unit in California, all scenarios, 1950–2100, on a 45-worker cluster. About 60 lines of actual code end-to-end, all leaning on library helpers, and the notebook prints its own timing + data-volume diagnostics at the end.
