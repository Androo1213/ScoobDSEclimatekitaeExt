---
title: Installation
---

## Prerequisites

You need two things installed on your machine:

1. **[Docker Desktop](https://www.docker.com/products/docker-desktop/)** — runs the containerized environment
2. **[VS Code](https://code.visualstudio.com/)** with the **[Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)** extension

That's it. You don't need to install Python, conda, or any packages locally — the container handles all of that.

## Open in Container

1. Clone the repo and open the folder in VS Code:
   ```bash
   git clone https://github.com/Androo1213/dseNpsDataPipelineNew.git
   code dseNpsDataPipelineNew
   ```

2. VS Code will detect the `.devcontainer` folder and show a prompt: **"Reopen in Container"** — click it. Or use `Cmd+Shift+P` and type `Dev Containers: Reopen in Container`.

3. First time takes about 8 minutes (building the conda environment with all the geospatial and climate packages). After that it's instant.

## Select the Kernel

When you open a notebook, click **Select Kernel** in the top right and pick **Python (py-env)**.

If you only see an R kernel or no Python kernel, you're probably not inside the container — go back to step 2.

## Verify Setup

Open `notebooks/python/Tutorial_Coiled_Setup.ipynb` and run the first code cell (the setup check). It should print something like:

```
xarray 2024.x.x
geopandas 1.x.x
coiled installed
andrewAdaptLibrary imported
NPS shapefile found
yay! go to Part 1.
```

If you see errors, make sure you're inside the container and using the py-env kernel.

## Coiled Authentication

The library uses [Coiled](https://coiled.io) to run data fetches on cloud workers. You need a Coiled account (free tier available).

In the VS Code terminal (inside the container):

```bash
coiled login
```

This opens a browser tab where you authenticate. Credentials are saved so you only do this once per container.

## Alternative: Browser (no VS Code)

If you prefer JupyterLab in the browser instead of VS Code:

```bash
./docker/run_docker.sh build      # first time only
./docker/run_docker.sh notebook   # opens http://localhost:8888
```
