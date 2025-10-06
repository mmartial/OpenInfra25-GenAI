# GenAI -- Image Generation

This repository contains the configuration files for the OpenInfra25-GenAI project.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/)
- a [Tailscale](https://tailscale.com/) account -- we will start the containers in a Tailscale network and NOT have any exposed ports
- an NVIDIA GPU with at least 24GB of VRAM
- [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)
- a [ComfyUI](https://github.com/comfyanonymous/ComfyUI)-enabled container, such as [comfyui-nvidia-docker](https://github.com/mmartial/comfyui-nvidia-docker)

## ComfyUI

[ComfyUI](https://github.com/comfyanonymous/ComfyUI) is a powerful, modular, and highly customizable graph-based user interface for building and running AI image generation workflows — especially with models like Stable Diffusion. Instead of writing code, users can visually connect different nodes (for prompts, samplers, models, image processors, etc.) to design complex pipelines with ease.

It’s popular for its flexibility, support for custom nodes and extensions, and ability to fine-tune every step of the image generation process — making it ideal for artists, researchers, and developers who want more control over AI-driven image creation.

### ComfyUI-Nvidia-Docker

GitHub: https://github.com/mmartial/comfyui-nvidia-docker

- Docker container setup for running ComfyUI using NVIDIA GPUs, bundling the necessary CUDA, drivers, and dependencies in a containerized environment.
- Supports configurable UID/GID mapping (i.e. dropping privileges to a user inside the container that matches your host user) so that file permissions for shared volumes behave cleanly.
- Integrates ComfyUI-Manager for easier updates and node management, and provides flexibility like running user scripts, customizing launch arguments, isolating user data (via separate “run” and “basedir” folders), and controlling security levels.

## Tailscale

[Tailscale](https://tailscale.com/) is a secure, easy-to-use overlay VPN that creates a private network connecting our devices. 
Built on top of the [WireGuard](https://www.wireguard.com/) protocol, it automatically handles NAT traversal, encryption, and authentication, so we can securely access devices and services as if they were on the same local network.

For a primer, see [How Tailscale works](https://tailscale.com/blog/how-tailscale-works). 
For homelab access, see [Tailscale: subnet router & remote services access](https://www.gkr.one/blg-20250323-tailscale).

The containers will be started in a Tailscale network and have no ports exposed on the host, only on the Tailscale network (our `tailnet`).

## Installation

After cloning this repository, in the folder where this `README.md` is located, run `cp compose.yaml.example compose.yaml` and edit the `compose.yaml` file to replace two components:
- in the ` oi25-comfyui-nvidia` service, replace `WANTED_UID` and `WANTED_GID` with your user ID and group ID (obtain with `id -u` and `id -g`)
- in the `tailscale-oi25-comfyui` service, replace `TS_AUTHKEY` with your Tailscale auth key

For details on Tailscale auth keys, see [Tailscale's documentation](https://tailscale.com/kb/1282/docker).
If logged into your Tailscale dashboard, go to [https://login.tailscale.com/admin/settings/keys](https://login.tailscale.com/admin/settings/keys) and in the "Auth Keys" section, click on "Gemerate Auth Key..." button. The [Tailscale-AuthKey](../CoreAI/Tailscale-AuthKey.png) shows an example of the settings that could be used.

After editing the `compose.yaml` file, create the required folders `mkdir basedir run` then run `docker compose up -d` to start the containers.

You can follow the deployment process using `docker compose logs -f`. The container will verify access to the NVIDIA GPU and install the necessary dependencies, starting with PyTorch and ending with ComfyUI.

Once the containers are up and running, obtain the `100.` IP address from your Tailscale dashboard, and access the WebUI at `http://<Tailscale_IP>:8188`. 

## Troubleshooting

Check if you can run `nvidia-smi` from outside the container but not within the `oi25-comfyui-nvidia` container:
- Try to run within the container:
```bash
docker exec -it oi25-comfyui-nvidia /usr/bin/nvidia-smi
```
- If you see `Failed to initialize NVML: Insufficient Permissions`, find the group of the `/dev/nvidia0` device and add the user to that group:
```bash
ls -l /dev/nvidia0
### crw-rw---- 1 root 1002 195, 0 Oct  6 02:25 /dev/nvidia0
### here 1002 is the group
```
- With this value, go into the `Comfy-local` folder and we will build a local version of `comfyui-nvidia-docker` using the same `FROM` as the version used in the `compose.yaml` file.
```bash
cd Comfy-local
# adapt GPU_GROUP_ID to the value obtained above
docker build --build-arg GPU_GROUP_ID=1002 -t comfy:local .
```
- Test:
```bash
docker run --gpus all --rm -it comfy:local /usr/bin/nvidia-smi
```
- If it works, first `cd` into the folder where the `compose.yaml` is located, then take down the containers:
```bash
docker compose down
```
- Edit the `compose,yaml` file to replace the `image` field of the `oi25-comfyui-nvidia` service with `comfy:local`.
- Then restart them with the new image:
```bash
docker compose up -d
```
- After restarting the container, we can confirm that `nvidia-smi` works within the container:
```bash
docker exec -it oi25-comfyui-nvidia /usr/bin/nvidia-smi
```
