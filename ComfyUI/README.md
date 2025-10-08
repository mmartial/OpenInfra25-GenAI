# GenAI -- ComfyUI

Those GenAI demos are adapted from existing workflows available as part of a ComfyUI installation, or from installed custom nodes.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
  - [Docker Compose](https://docs.docker.com/compose/)
- a [Tailscale](https://tailscale.com/) account
  - containers will be used in a Tailscale network (`tailnet`) and have no exposed ports
- [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)
  - an NVIDIA GPU with at least 24GB of VRAM
- [ComfyUI](https://github.com/comfyanonymous/ComfyUI), here a containerized version: [comfyui-nvidia-docker](https://github.com/mmartial/comfyui-nvidia-docker)

## ComfyUI

[ComfyUI](https://github.com/comfyanonymous/ComfyUI) is a powerful, modular, and highly customizable graph-based user interface for building and running AI image generation workflows — especially with models like Stable Diffusion. Instead of writing code, users can visually connect different nodes (for prompts, samplers, models, image processors, etc.) to design complex pipelines with ease.

It’s popular for its flexibility, support for custom nodes and extensions, and ability to fine-tune every step of the image generation process — making it ideal for artists, researchers, and developers who want more control over AI-driven image creation.

In ComfyUI, everything revolves around nodes — modular building blocks that each perform a specific task in the AI image generation pipeline. By connecting these nodes visually, a graph is formed, and the data flows from one to another, creating a flexible, customizable workflow. Nodes are linked in a pipeline: "Prompt ➝ Encode ➝ Model ➝ Noise ➝ Sampler ➝ Decode ➝ Output".

### Stable Diffusion terminology

| Component | Role | Analogy |
| --- | --- | --- |
| 🧠 Model | Core generator turning text into images | 🎨 Painter |
| 📖 CLIP | Encodes text into concepts the model understands | 🗣️ Translator |
| 🧩 LoRA | Adds new styles or knowledge to the base model | 🧰 Custom brush |
| 🔄 Sampler | Algorithm that refines noise into the image | 🔄 Painting technique |
| 🌌 Latent | Compressed internal image representation | 📐 Blueprint |
| 🖼️ VAE | Converts latent image to actual pixels | 🖨️ Printer |

- 🧠 Model — the “brain” of image generation.
  The main neural network (e.g., stable-diffusion-v1-5, SDXL, FLUX, etc.) trained to turn text into images.
  It learns the relationship between words and images and is responsible for generating the image structure, composition, and details.

- 📖 CLIP — the “translator” of text to concepts.
  A text encoder that converts prompts into a vector representation the diffusion model understands.
  It doesn’t generate the image but tells the model what concepts and styles to focus on.

- 🧩 LoRA — the “add-on” for new styles or subjects.
  Short for Low-Rank Adaptation, a lightweight fine-tuning file that modifies a base model without retraining it.
  Adds new styles, characters, objects, or aesthetics on top of an existing model.
  Example: A LoRA trained on Van Gogh paintings makes any image adopt that style when activated.

- 🔄 Sampler — the “rendering process”.
  The algorithm that iteratively refines random noise into a final image based on our prompt and model.
  Different samplers change image speed, detail, smoothness, and style.

- 🌌 Latent — the “hidden image space”.
  The compressed, abstract representation of the image inside the model’s hidden layers.
  Stable Diffusion doesn’t generate pixels directly — it works in latent space (a smaller, efficient representation), then converts it into a visible image later.

- 🖼️ VAE — the “decoder” that turns abstract data into pixels.
  The Variational Autoencoder — a neural network that encodes images into latent space and decodes them back into real pixel space.
  At the end of the generation process, the VAE translates the latent image into a visible PNG/JPG.

### ComfyUI-Nvidia-Docker

GitHub: [https://github.com/mmartial/comfyui-nvidia-docker](https://github.com/mmartial/comfyui-nvidia-docker)

Docker container setup for running ComfyUI using NVIDIA GPUs, bundling the necessary CUDA, drivers, and dependencies in a containerized environment.
The container supports configurable UID/GID mapping (i.e. dropping privileges to a user inside the container that matches our host user) so that file permissions for shared volumes behave cleanly.
It also integrates ComfyUI-Manager for easier updates and node management, and provides flexibility like running user scripts, customizing launch arguments, isolating user data (via separate “run” and “basedir” folders), and controlling security levels.

## Tailscale

[Tailscale](https://tailscale.com/) is a secure, easy-to-use overlay VPN that creates a private network connecting our devices.
Built on top of the [WireGuard](https://www.wireguard.com/) protocol, it automatically handles NAT traversal, encryption, and authentication, to securely access devices and services as if they were on the same local network.

For a primer, see [How Tailscale works](https://tailscale.com/blog/how-tailscale-works).

For homelab access, see [Tailscale: Subnet Router & Remote Services Access](https://www.gkr.one/blg-20250323-tailscale).

The containers will be started in a Tailscale network and have no ports exposed on the host, only on the Tailscale network (our `tailnet`).

## Installation

After cloning this repository, in the folder where this `README.md` is located, run `cp compose.yaml.example compose.yaml` and edit the `compose.yaml` file to replace two components:

- in the `oi25-comfyui-nvidia` service, replace `WANTED_UID` and `WANTED_GID` with the expected user ID and group ID (obtain with `id -u` and `id -g`).
- in the `tailscale-oi25-comfyui` service, replace `TS_AUTHKEY` with the user's Tailscale auth key.

For details on Tailscale auth keys, see [Tailscale's documentation](https://tailscale.com/kb/1282/docker).
If logged into the Tailscale dashboard, go to [https://login.tailscale.com/admin/settings/keys](https://login.tailscale.com/admin/settings/keys) and in the "Auth Keys" section, click on "Generate Auth Key...". [Tailscale-AuthKey](../CoreAI/Tailscale-AuthKey.png) shows an example of the settings that could be used.

After editing the `compose.yaml` file, create the required folders `mkdir basedir run` then run `docker compose up -d` to start the compose stack.
It is possible to view the logs of a started service using `docker compose logs -f`.

The containers will take a few minutes to start up. During this process, the container will verify access to the NVIDIA GPU and install the necessary dependencies, starting with PyTorch and ending with ComfyUI. This will allow the use of the workflows present in the various sub-folders.

Once the containers are up and running, obtain the `100.` IP address of the `tailscale-oi25-comfyui` "machine" from the Tailscale dashboard, and access the JupyterLab at `http://<TAILSCALEIP>:8188`.

To turn off the compose stack, run `docker compose down` in the folder where the `compose.yaml` is located.

## Troubleshooting

Note: this should be done with the compose stack turned off.

When running on OpenStack, the GPU device(s) (usually `/dev/nvidia...`) might be owned by groups that do not exist within the container.
This will prevent proper access to the device for inference.
This can be checked using `nvidia-smi`. Although the running user might be able to access the command from a terminal on the OpenStack instance, the device might not be available within the `oi25-comfyui-nvidia` container. Check by using:

```bash
docker exec -it oi25-comfyui-nvidia /usr/bin/nvidia-smi
```

If `Failed to initialize NVML: Insufficient Permissions` appears, find the group of the `/dev/nvidia0` device to add the `coreai` and `coareaitoo` users to that group.

```bash
ls -ln /dev/nvidia0
### crw-rw---- 1 root 1002 195, 0 Oct  6 02:25 /dev/nvidia0
### here 1002 is the group
```

With this value, create a new `local` container built to add the users to the required group.
The `compose.yaml` file in the `Comfy-local` folder must use the same `FROM` as the `mmartial/comfyui-nvidia-docker:tag` tag used in the `compose.yaml` file. The version in the current folder matches the value present in the `compose.yaml.example` file. Adapt the `GPU_GROUP_ID` argument to match the needed group ID (in the above example, `1002`). The built container will be named `comfy:local`.

```bash
cd Comfy-local
# adapt GPU_GROUP_ID to the value obtained above
docker build --build-arg GPU_GROUP_ID=1002 -t comfy:local .
```

After testing if the new container built does not return the error, modify the `compose.yaml` to use the new container (in the `oi25-comfyui-nvidia` service definition, comment/uncomment the main and alternate `image:` entry) before restarting the compose stack.

```bash
# Check if the problem is resolved
docker run --gpus all --rm -it comfy:local /usr/bin/nvidia-smi

# If it works, comment/uncomment the image: entry for the oi25-comfyui-nvidia to replace the `image` field with `comfy:local`

# Restart the compose stack with the new image
docker compose up -d
```

After restarting the stack, confirm that `nvidia-smi` works as expected:

```bash
docker exec -it oi25-comfyui-nvidia /usr/bin/nvidia-smi
```
