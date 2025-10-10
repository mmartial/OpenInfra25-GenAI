# Flux.1

Original code: https://github.com/Infotrend-Inc/CoreAI-DemoProjects/tree/main/Flux1Schnell

## About

Load and use the [Flux.1 Schnell](https://huggingface.co/black-forest-labs/FLUX.1-schnell) diffusion model — a lightweight, fast variant of the Flux text-to-image family — for image generation and experimentation in a local environment. 

High-level steps:

1. Install and import the required libraries (e.g., diffusers, torch, transformers, etc.).
2. Configure the runtime device (GPU) for acceleration.
3. Load the Flux1 Schnell model with recommended precision (`torch_dtype=torch.bfloat16`)
4. Run inference with specifed prompts and seeds to generate images
