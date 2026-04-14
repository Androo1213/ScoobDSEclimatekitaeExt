---
title: Installation
---

## Prereqs (strictly enforced :P)

Downloads wise, you just need docker and then the dev-containers VS Code Extension.

1. **[Docker Desktop](https://www.docker.com/products/docker-desktop/)** — runs the containerised environment
2. **[VS Code](https://code.visualstudio.com/)** with the **[Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)** extension

Don't worry about dependencies or I suppose even Python-- the container will handle all of that. 

## Open in Container

1. Clone the repo and open the folder in VS Code:
   ```bash
   git clone https://github.com/Androo1213/dseNpsDataPipelineNew.git
   code dseNpsDataPipelineNew
   ```

2. VS Code _should_ (ping me if this doesn't work, I've had it be a teeny bit buggy) detect the `.devcontainer` folder and show a prompt: **"Reopen in Container"**. Click it! Sometimes vsCode isn't smart enough to prompt but you can even use `Cmd+Shift+P` and type `Dev Containers: Reopen in Container`.

3. It takes like 10 minutes the first time you build it (building the conda environment with all the geospatial and climate packages). After that it's trivially quick.

## Select the Kernel

When one open a notebook, just need to click **Select Kernel** in the top right and pick **Python (py-env)**.

If don't see this Python kernel, you're probably not inside the container, or there's a problem with it, try step two again.

## Verify Setup

Open `notebooks/python/Tutorial_Coiled_Setup.ipynb` and run the first code cell (the setup check). It should print something like:

```
xarray 2024.x.x
geopandas 1.x.x/
coiled installed
andrewAdaptLibrary imported
NPS shapefile found
yay! go to Part 1.
```

If you see errors, make sure you're inside the container and using the py-env kernel.

## Coiled Authentication

Lib uses [Coiled](https://coiled.io) to run data fetches on cloud workers. You need a Coiled account (free tier available).
Major TODO on this is that this only works with like people in the lab right now, need to probably make a tutorial on how to actually set up the coiled infra so other teams can have this for them, and also think of the best way for the tutorial notebook to work for like 'outside observers'

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
