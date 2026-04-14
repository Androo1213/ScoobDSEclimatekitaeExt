# DSEclimatekitaeExt

Documentation site for the DSE Cal-Adapt climate pipeline — wraps Cal-Adapt's [climakitae](https://github.com/cal-adapt/climakitae) with helpers for fuzzy park search, Coiled-based fetching, spatial plotting, and CSV export.

**Live site:** https://androo1213.github.io/ScoobDSEclimatekitaeExt/

## What's in here

- `index.md` — landing page (features, links)
- `getting-started/` — installation guide
- `tutorials/` — tutorial notebook (executable via Binder on the live site)
- `advanced/` — advanced topics (Luigi, packaging, WRF)
- `environment.yml` — Binder environment spec (used when the rocket icon launches a kernel)
- `myst.yml` — MyST site config + Thebe/Binder integration

## Pipeline code

The climate pipeline this site documents lives in a separate repo:
https://github.com/Androo1213/dseNpsDataPipelineNew

## Local preview

```bash
pip install mystmd
myst start
```

Opens http://localhost:3000 with hot reload.

## Deploy

GitHub Actions handles deployment automatically on push to `main`. See `.github/workflows/deploy.yml`.
