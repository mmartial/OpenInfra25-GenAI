# OpenInfra 2025 Europe: Open-Source GenAI on OpenStack

Configuration, workshop material, and demo assets for the OpenInfra 2025 Europe talk **“Open-Source GenAI on OpenStack”** by Julian Pistorius, Mike Lowe, and Martial Michel.

The demos were built and tested on Indiana University’s Jetstream OpenStack cloud using GPU-backed instances.

## Repository Layout

- `CoreAI/` – Jupyter-based inference workflows powered by Infotrend’s CoreAI containers (LLM chat via Ollama plus Stable Diffusion notebooks).
- `ComfyUI/` – Containerized ComfyUI stacks with ready-to-run workflows for using LoRA fine-tuning, FLUX pipelines, and animated diffusion.

## Prerequisites

- Docker and Docker Compose
- NVIDIA Container Toolkit with access to an NVIDIA GPU providing at least 24 GB of VRAM
- A Tailscale account (demos are exposed exclusively on a Tailscale tailnet)
  - Example Tailscale auth key configuration: `CoreAI/Tailscale-AuthKey.png`

Ensure your workstation or cloud instance can attach to the same tailnet you plan to use for the demos.

## Shared Networking via Tailscale

Both demo stacks rely on a sidecar `tailscale` container instead of host port exposure. 
Generate an auth key in the [Tailscale admin console](https://login.tailscale.com/admin/settings/keys)
The same key can be reused for both Compose files. 
Once the stack is up, retrieve the `100.x.y.z` address of the relevant node from the Tailscale dashboard to reach the services.

## CoreAI Inference Notebooks

The `CoreAI` folder extends the Infotrend CoreAI demo projects with an Ollama container for local LLM inference alongside Stable Diffusion notebooks.

1. Copy the Compose template: `cp compose.yaml.example compose.yaml`.
2. Edit `compose.yaml`:
   - `oi25-coreai-cpo`: replace `WANTED_UID` / `WANTED_GID` with your user and group IDs (`id -u`, `id -g`).
   - `tailscale-oi25-coreai`: set `TS_AUTHKEY` to the generated Tailscale auth key.
3. Launch the stack: `docker compose up -d`.
4. Wait a few minutes while containers install dependencies and mount the `/iti` notebooks directory.
5. Visit JupyterLab at `http://<TAILSCALE_IP>:8888` and explore the notebooks inside `/iti`.

### Customizing GPU Permissions

If `docker exec -it oi25-coreai-cpo /usr/bin/nvidia-smi` fails with *Failed to initialize NVML: Insufficient Permissions*, the GPU device group on the host does not exist in the container. Build a custom image from `CoreAI-local/`:

```bash
cd CoreAI/CoreAI-local
docker build --build-arg GPU_GROUP_ID=<host_gpu_group> -t coreai:local .
docker run --gpus all --rm -it coreai:local /usr/bin/nvidia-smi
```

Once validated, switch the `image:` reference in `compose.yaml` to `coreai:local` and restart the stack.

## ComfyUI Image Generation Workflows

The `ComfyUI` folder packages an NVIDIA-enabled ComfyUI deployment (based on [comfyui-nvidia-docker](https://github.com/mmartial/comfyui-nvidia-docker)) together with curated workflows stored under `01-`, `02-`, and `03-` directories.

1. Copy the Compose template: `cp compose.yaml.example compose.yaml`.
2. Edit `compose.yaml`:
   - `oi25-comfyui-nvidia`: replace `WANTED_UID` / `WANTED_GID`.
   - `tailscale-oi25-comfyui`: set `TS_AUTHKEY`.
3. Create runtime directories: `mkdir basedir run` (inside `ComfyUI/` for example).
4. Start the stack: `docker compose up -d`.
5. Connect to ComfyUI at `http://<TAILSCALE_IP>:8188` to load and execute the bundled workflows.

### Adjusting GPU Access

If `docker exec -it oi25-comfyui-nvidia /usr/bin/nvidia-smi` reports insufficient permissions, build a tailored image from `ComfyUI/Comfy-local/`:

```bash
cd ComfyUI/Comfy-local
docker build --build-arg GPU_GROUP_ID=<host_gpu_group> -t comfy:local .
docker run --gpus all --rm -it comfy:local /usr/bin/nvidia-smi
```

Update the `image:` field in your `compose.yaml` to `comfy:local` and restart the services.

## Turning Stacks On and Off

Use `docker compose logs -f <service>` to monitor start-up. Shut down either stack with `docker compose down` from the corresponding directory when you are done.
