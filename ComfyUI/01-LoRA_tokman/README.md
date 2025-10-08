# Low Rank Adaptation (LoRA) - "Tokman"

The content below is referring to the [FLUX.1 LoRA training](https://www.gkr.one/blg-20240818-flux-lora-training) post which uses the [ai-toolkit](https://github.com/ostris/ai-toolkit) repository.

The post outlines how to train a LoRA (Low-Rank Adaptation) module for the [FLUX.1-Dev](https://huggingface.co/black-forest-labs/FLUX.1-dev) and [FLUX.1-Schnell](https://huggingface.co/black-forest-labs/FLUX.1-schnell) image generation models (those steps require a GPU with at least 24GB of VRAM) and using ComfyUI for inference.

## Goals

- The aim is to allow users to fine-tune (via LoRA) FLUX.1 models on their own images so they can later generate new images that reflect the subject present in the training set.
- The procedure relies on the [ai-toolkit](https://github.com/ostris/ai-toolkit) repository (on GitHub) to carry out the training pipeline.
- The guide supports both the Dev and Schnell versions of FLUX.1, each with its own configuration differences.

## Requirements

- A NVIDIA GPU with 24 GB VRAM (or more).
- Ubuntu 24.04 (or compatible setup), plus standard tools (git, Python 3.12, pip, venv).
- A Hugging Face token to access FLUX.1 weights and related resources.
- Training images in .jpg or .png format (recommended 1024×1024), each paired with a .txt file containing prompt(s).

## Training notes

When training a LoRA, we typically select a unique token (like `tok`, `zzzperson`, `myStyle123`, ...) that doesn’t exist in the model’s vocabulary.
This token is injected into the text prompts during training and becomes associated with the subject/style in the dataset.

Training the LoRA was done on 4,000 steps.
On an Nvidia RTX 4090, this training took about 140 minutes with 25 input images.
The final LoRA weight file (`flux_dev-tok.safetensors`) is about 200MB.

## Usage

Once we have generated our LoRA, we can use the basic worflow present in this folder (`tokman_basicFlux_workflow-ex1.json`).
Grab and drop the file into the ComfyUI WebUI, this will load the workflow.

The workflow is a basic image generation one using the Flux.1-Dev model with a LoRA (here our custom trained model).

This workflow does not require the installation of new custom nodes.
It will require a set of weights to be placed in various `basedir/models` folders.

The weights are:

- From [https://huggingface.co/black-forest-labs/FLUX.1-dev/tree/main](https://huggingface.co/black-forest-labs/FLUX.1-dev/tree/main)
  - `flux1-dev.safetensors`, to be placed into `basedir/models/diffusion_models` (23GB)
  - `ae.safetensors`, to be placed into `basedir/models/vae` (320MB)
- From [https://huggingface.co/comfyanonymous/flux_text_encoders/tree/main](https://huggingface.co/comfyanonymous/flux_text_encoders/tree/main)
  - `t5xxl_fp16.safetensors`, to be placed into `basedir/models/clip` (9GB)
  - `clip_l.safetensors`, to be placed into `basedir/models/clip` (200MB)
- The generated LoRA weight file (`flux_dev-tok.safetensors`) to be placed into `basedir/models/lora` (200MB)

The workflows present in those folders are the same basic ones; we altered the prompt or the resolution to generate different images.

![tokman_ex01_02.jpg](./tokman_ex01_02.jpg
