## Gemma3

Original code: [https://github.com/Infotrend-Inc/CoreAI-DemoProjects/tree/main/Gemma3](https://github.com/Infotrend-Inc/CoreAI-DemoProjects/tree/main/Gemma3)

## About

This notebook uses Google’s Gemma 3 open models locally (using its instruction-tuned checkpoint: `gemma-3-4b-it`) to show Large Language Model (LLM) and Vision Language Model (VLM) capabilities. without using a service such as Ollama to `serve` the model.

High-level steps:

1. Install/load runtime packages.  ￼
2. Initialize tokenizer and model
3. Prompting (text): a small chat message (system/user) to answer a question.
4. Multimodal (image): perform image understanding on a provided image.
