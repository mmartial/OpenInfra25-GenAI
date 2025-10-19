### ComfyUI

[https://github.com/comfyanonymous/ComfyUI](https://github.com/comfyanonymous/ComfyUI)

<div style="font-size: 0.7em;">

- 🧩 Modular & Visual — Graph-based interface for building AI image generation workflows (without coding).
- 🔗 Node-Driven Architecture — Everything is built from nodes — each performing a specific task in the pipeline.
- 🧠 Flexible Workflows — Users connect visual nodes (prompts, samplers, models, processors) to create complex pipelines.
- 🧰 Customizable — Supports `custom nodes`, enabling fine-tuned control at every stage.
- 🪄 Pipeline Flow — Prompt ➝ Encode ➝ Model ➝ Noise ➝ Sampler ➝ Decode ➝ Output.

</div>

--

### ComfyUI-Nvidia-Docker

[https://github.com/mmartial/comfyui-nvidia-docker](https://github.com/mmartial/comfyui-nvidia-docker)

<div style="font-size: 0.7em;">

- 🐳 ComfyUI in a container with NVIDIA GPU support, bundling CUDA, drivers, and all dependencies.
- 🧑‍💻 Clean File Permissions — Supports `comfy` user UID/GID mapping to match host users and avoid permission issues on shared volumes.
- 🧭 Easy Management — Integrates ComfyUI-Manager for smooth updates and node handling.
- 🧩 Flexible Configuration — Enables `user scripts`, custom launch args, and isolated data through separate `run` & `basedir` folders.
- 🔐 Security Controls — Provides configurable ComfyUI `security levels` to match your environment.

</div>

--

### Stable Diffusion terminology

<div style="font-size: 0.5em;">

| Component | Role | Analogy |
| --- | --- | --- |
| 🧠 Model | Core generator turning text into images | 🎨 Painter |
| 📖 CLIP | Encodes text into concepts the model understands | 🗣️ Translator |
| 🧩 LoRA | Adds new styles or knowledge to the base model | 🧰 Custom brush |
| 🔄 Sampler | Algorithm that refines noise into the image | 🔄 Painting technique |
| 🌌 Latent | Compressed internal image representation | 📐 Blueprint |
| 🖼️ VAE | Converts latent image to actual pixels | 🖨️ Printer |

</div>

<div style="font-size: 0.5em;">

[https://en.wikipedia.org/wiki/Stable_Diffusion](https://en.wikipedia.org/wiki/Stable_Diffusion)

</div>

--

### Pre-requisite

<div style="font-size: 0.5em;">

[https://github.com/mmartial/OpenInfra25-GenAI/tree/main/ComfyUI](https://github.com/mmartial/OpenInfra25-GenAI/tree/main/ComfyUI)

</div>

- [Docker](https://docs.docker.com/get-docker/)
    - [Docker Compose](https://docs.docker.com/compose/)
- a [Tailscale](https://tailscale.com/) account
    - containers will be used in a Tailscale network (`tailnet`) and have no exposed ports
- [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)
    - an NVIDIA GPU with at least 24GB of VRAM
- a [ComfyUI](https://github.com/comfyanonymous/ComfyUI) environment
    - container: [mmartial/comfyui-nvidia-docker](https://github.com/mmartial/comfyui-nvidia-docker)

--

### Getting started

<div style="font-size: 0.5em;">

[https://github.com/mmartial/OpenInfra25-GenAI/blob/main/ComfyUI/compose.yaml.example](https://github.com/mmartial/OpenInfra25-GenAI/blob/main/ComfyUI/compose.yaml.example)

</div>

<div style="font-size: 0.8em;">

```yaml
services:
  oi25-comfyui-nvidia:
    image: mmartial/comfyui-nvidia-docker:latest
    volumes:
      - ./run:/comfy/mnt
      - ./basedir:/basedir
    environment:
      - WANTED_UID=<WANTED_UID> # TODO: replace this to your user ID `id -u`
      - WANTED_GID=<WANTED_GID> # TODO: replace this to your group ID `id -g`
      - BASE_DIRECTORY=/basedir
      - SECURITY_LEVEL=normal

  tailscale-oi25-comfyui:
    image: tailscale/tailscale:latest
    hostname: tailscale-oi25-comfyui
    environment:
      - TS_AUTHKEY=tskey-auth- # TODO: Add your TS_AUTHKEY here
```

</div>

No ports exposed: access through `tailscale-oi25-comfyui`'s Tailscale IP

--

### Flux.1 "tok man" Low Rank Adaptation (LoRA) 

<div style="font-size: 0.5em;">

[https://www.gkr.one/blg-20240818-flux-lora-training](https://www.gkr.one/blg-20240818-flux-lora-training)

</div>

FLUX.1 LoRA to generate new outputs reflecting the training subject

<div style="font-size: 0.7em;">

- 🪄 Training Details — LoRA trained for 4,000 steps using 25 input images.
- ⚡ Hardware Performance — On an NVIDIA RTX 4090, training completed in ≈140 minutes.
- 💾 Model Output — Final LoRA weight file `flux_dev-tok.safetensors` is ~200 MB.

</div>
<hr>

<div style="font-size: 0.8em;">

- FLUX.1-dev [https://huggingface.co/black-forest-labs/FLUX.1-dev](https://huggingface.co/black-forest-labs/FLUX.1-dev)
- FLUX.1 LoRA training [https://www.gkr.one/blg-20240818-flux-lora-training](https://www.gkr.one/blg-20240818-flux-lora-training)
- ai-toolkit [https://github.com/ostris/ai-toolkit](https://github.com/ostris/ai-toolkit)

</div>

--

![Tokan result](https://github.com/mmartial/OpenInfra25-GenAI/blob/main/ComfyUI/01-LoRA_tokman/tokman_ex01_02.jpg?raw=true)

--

### Flux Kontext

<div style="font-size: 0.5em;">

[https://github.com/mmartial/OpenInfra25-GenAI/tree/main/ComfyUI/02-Flux_Kontext](https://github.com/mmartial/OpenInfra25-GenAI/tree/main/ComfyUI/02-Flux_Kontext)

</div>

<div style="font-size: 0.7em;">

- 🪄 In-Context Image Editing + Generation — Provide an image + text instruction, and FLUX Kontext interprets the scene and applies the requested edit.
- 🧭 Local & Global Edits — Make targeted changes (e.g. change hair color, remove or add objects) or transform entire scenes, styles, or layouts.
- 🧍 Character/Object Consistency — Successive edits preserve identity, style & composition, minimizing visual drift across multiple editing steps.

</div>
<hr>

<div style="font-size: 0.8em;">

- FLUX.1 Kontext [https://bfl.ai/models/flux-kontext](https://bfl.ai/models/flux-kontext)
- ComfyUI Flux Kontext [https://docs.comfy.org/tutorials/flux/flux-1-kontext-dev](https://docs.comfy.org/tutorials/flux/flux-1-kontext-dev)

</div>

--

<div style="font-size: 0.7em;">

1. text removal + re-color black and white source
2. background addition + outfit change
3. outfit change + add the OpenInfra logo on the outfit
4. style change

</div>

![Flux Kontext result](https://github.com/mmartial/OpenInfra25-GenAI/blob/main/ComfyUI/02-Flux_Kontext/fluxkontext-ex01_04.jpg?raw=true)

--

### WAN 2.2 Animate

<div style="font-size: 0.5em;">

[https://github.com/mmartial/OpenInfra25-GenAI/tree/main/ComfyUI/03-WAN2.2_Animate](https://github.com/mmartial/OpenInfra25-GenAI/tree/main/ComfyUI/03-WAN2.2_Animate)

</div>

<div style="font-size: 0.7em;">

- 🤖 Core Tools — WAN 2.2 Animate + Kijai’s `custom node` `ComfyUI-WanAnimatePreprocess` (+ embedded workflow).
- 🧍 Preprocessing Pipeline — Detects people in frames, estimates body / hand / face keypoints, extracts aligned face crops, and formats results for WanAnimate.
- 🎬 Result — Transforms a single character image + reference video into a high-fidelity animated or replacement video.
- ⚠️ GPU — Minimum 32 GB VRAM (even with `fp8` weights).

</div>
<hr>

<div style="font-size: 0.8em;">

- WAN 2.2 Animate [https://huggingface.co/Wan-AI/Wan2.2-Animate-14B](https://huggingface.co/Wan-AI/Wan2.2-Animate-14B)
- Kijai's `ComfyUI-WanAnimatePreprocess` [https://github.com/kijai/ComfyUI-WanAnimatePreprocess](https://github.com/kijai/ComfyUI-WanAnimatePreprocess)
</div>

--

![WAN 2.2 Animate result](https://github.com/mmartial/OpenInfra25-GenAI/blob/main/ComfyUI/03-WAN2.2_Animate/wan2.2animate.jpg?raw=true)
