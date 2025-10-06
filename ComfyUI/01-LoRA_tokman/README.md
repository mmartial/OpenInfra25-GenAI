# Low Rank Adaptation (LoRA) - "Tokman"

The content below is refering to the LoRA training blog post: https://www.gkr.one/blg-20240818-flux-lora-training

The blog post outlines a method to train LoRA (Low-Rank Adaptation) modules for the FLUX.1-Dev and FLUX.1-Schnell image generation models using a 24 GB GPU and using ComfyUI for inference.

## Goals

- The aim is to allow users to fine-tune (via LoRA) FLUX.1 models on their own images so they can later generate new images that reflect the style or subject present in the training set.
- The procedure relies on the [ai-toolkit](https://github.com/ostris/ai-toolkit) repository (on GitHub) to carry out the training pipeline.
- The guide supports both the Dev and Schnell versions of FLUX.1, each with its own configuration differences.

## Requirements

- A NVIDIA GPU with ≥ 24 GB VRAM.
- Ubuntu 24.04 (or compatible setup), plus standard tools (git, Python 3.12, pip, venv).
- A Hugging Face token to access FLUX.1 weights and related resources.
- Training images in .jpg or .png format (recommended 1024×1024), each paired with a .txt file containing prompt(s).

## Training notes

When training a LoRA, you usually pick a unique token (like tok, zzzperson, or myStyle123) that doesn’t exist in the model’s vocabulary. This token is injected into the text prompts during training and becomes associated with the subject/style in your dataset.

We will train on 4,000 steps.

On an Nvidia RTX 4090, this training took about 140 minutes on 25 input images.

The final LoRA weight file (`flux_dev-tok.safetensors`) is about 200MB.

## Usage

Grab amd drop the `tokman_basicFlux_workflow-ex1.json` file into onto the ComfyUI WebUI, this will load the workflow.

This workflow does not require any special nodes, but will require a set of weights to be placed in various `basedir/models` folders.

The weights are:
- From https://huggingface.co/black-forest-labs/FLUX.1-dev/tree/main
  - `flux1-dev.safetensors`, to be placed into `basedir/models/diffusion_models` (23GB)
  - `ae.safetensors`, to be placed into `basedir/models/vae` (320MB)
- From https://huggingface.co/comfyanonymous/flux_text_encoders/tree/main
  - `t5xxl_fp16.safetensors`, to be placed into `basedir/models/clip` (9GB)
  - `clip_l.safetensors`, to be placed into `basedir/models/clip` (200MB)
- The generated LoRA weight file (`flux_dev-tok.safetensors`) to be placed into `basedir/models/lora` (200MB)

The workflow is a basic workflow that will generate an image using the LoRA weight file.

You can alter the prompt or the resolution to generate different images, as shown in the `tokman_basicFlux_workflow-ex2.json` file.
