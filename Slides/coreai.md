### Infotrend's CoreAI

[https://github.com/Infotrend-Inc/CoreAI](https://github.com/Infotrend-Inc/CoreAI)

Build Docker images for ML/CV projects

- CUDA or CPU builds
- TensorFlow
- PyTorch
- OpenCV
- Jupyter Lab
- Ubuntu based

Run as a non-root `coreai` user. 

**Same `FROM`, multiple applications**

--

### Infotrend's CoreAI -- Demo Projects

<div style="font-size: 0.5em;">

[https://github.com/Infotrend-Inc/CoreAI-DemoProjects](https://github.com/Infotrend-Inc/CoreAI-DemoProjects)

</div>

<div style="font-size: 0.5em;">

| Domain | Project Name | 
| --- | --- | 
| CV | CLIP (Contrastive Language-Image Pre-training) Model Implementation |
| CV | Fashion MNIST Classification | 
| CV | Fast Neural Style Transfer | 
| DS | Home Credit Default Risk Recognition |
| LLM | <div style="color: green">AI Agent with Web Search and LiteLLM</div> | 
| LLM | Fine Tuning LLaMa using QLoRA | 
| LLM (CV) | <div style="color: green">Flux1Schnell Image Generation</div> | 
| LLM (+ CV) | <div style="color: green">Gemma3 LLM + VLM (Image Understanding)</div> | 
| LLM | <div style="color: green">RAG Pipeline</div> | 
| ML | Brain Tumor Segmentation | 
| Multimedia | Video Transcription | [
| NLP | NLP with Disaster Tweets | 

</div>

--

### Pre-requisite

<div style="font-size: 0.5em;">

[https://github.com/mmartial/OpenInfra25-GenAI/tree/main/CoreAI](https://github.com/mmartial/OpenInfra25-GenAI/tree/main/CoreAI)

</div>

- [Docker](https://docs.docker.com/get-docker/)
    - [Docker Compose](https://docs.docker.com/compose/)
- a [Tailscale](https://tailscale.com/) account
    - containers will be used in a Tailscale network (`tailnet`) and have no exposed ports
- [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)
    - an NVIDIA GPU with at least 24GB of VRAM
- a [PyTorch](https://pytorch.org/) enhanced container
    - [Infotrend's CoreAI](https://github.com/Infotrend-Inc/CoreAI)

--

### Getting started

<div style="font-size: 0.5em;">

[https://github.com/mmartial/OpenInfra25-GenAI/blob/main/CoreAI/compose.yaml.example](https://github.com/mmartial/OpenInfra25-GenAI/blob/main/CoreAI/compose.yaml.example)

</div>

<div style="font-size: 0.8em;">

```yaml
services:
  oi25-coreai-cpo:
    image: infotrend/coreai:25b01-cpo-12.6.3_2.6.0_4.11.0
    command: /run_jupyter.sh
    environment:
      - WANTED_UID=<WANTED_UID> # replace this to your user ID `id -u`
      - WANTED_GID=<WANTED_GID> # replace this to your group ID `id -g`

  oi25-coreai-ollama:
    image: ollama/ollama:latest
    command: serve

  tailscale-oi25-coreai:
    image: tailscale/tailscale:latest
    hostname: tailscale-oi25-coreai
    environment:
      - TS_AUTHKEY=tskey-auth- # TODO: Add your TS_AUTHKEY here
```

</div>

No ports exposed: access through `tailscale-oi25-coreai`'s Tailscale IP

--

### Ollama

<div style="font-size: 0.5em;">

[https://github.com/mmartial/OpenInfra25-GenAI/tree/main/CoreAI/iti/01-ollama](https://github.com/mmartial/OpenInfra25-GenAI/tree/main/CoreAI/iti/01-ollama)

</div>

Serve local Large Language Models (LLMs)

<div style="font-size: 0.8em;">

- REST
    [https://github.com/ollama/ollama/blob/main/docs/api.md](https://github.com/ollama/ollama/blob/main/docs/api.md)
- Python's OpenAI API 
    [https://pypi.org/project/openai/](https://pypi.org/project/openai/)
- Python's Ollama's API
    [https://pypi.org/project/ollama/](https://pypi.org/project/ollama/)

</div>
<hr>

<div style="font-size: 0.8em;">

- Ollama [https://github.com/ollama/ollama](https://github.com/ollama/ollama)

</div>

--

### Search Agent

<div style="font-size: 0.5em;">

[https://github.com/mmartial/OpenInfra25-GenAI/tree/main/CoreAI/iti/03-SearchAgent](https://github.com/mmartial/OpenInfra25-GenAI/tree/main/CoreAI/iti/03-SearchAgent)

</div>

tool-using LLM: web search with local LLM

- 🧰 Define tools: web search + fetch/summarize helper
- 🔁 Agent loop: plan → choose tool → execute → observe
- 📥 Retrieve & summarize: gather top hits, extract key snippets
- 🧠 Synthesize: use context to produce a grounded answer

<hr>

<div style="font-size: 0.8em;">

- Perplexity Sonar [https://docs.perplexity.ai/getting-started/models](https://docs.perplexity.ai/getting-started/models)
- Ollama Web Search [https://docs.ollama.com/web-search](https://docs.ollama.com/web-search)

</div>

--

### Retrieval Augmented Generation (RAG)

<div style="font-size: 0.5em;">

[https://github.com/mmartial/OpenInfra25-GenAI/tree/main/CoreAI/iti/02-RAG_Pipeline](https://github.com/mmartial/OpenInfra25-GenAI/tree/main/CoreAI/iti/02-RAG_Pipeline)

</div>

Extract key snippets from documents and use them as context for LLM Q&A

- 📄 Ingest: small document set
- ✂️ Chunk & embed: break text into vectors
- 🧭 Index: store in vector database
- 🤖 LLM Q&A: retrieve top chunks as context
<hr>

<div style="font-size: 0.8em;">

- Ragbits [https://ragbits.deepsense.ai/](https://ragbits.deepsense.ai/)
- Docling [https://docling-project.github.io/docling/](https://docling-project.github.io/docling/)
- Chromadb [https://www.trychroma.com/](https://www.trychroma.com/)

</div>

--

### Gemma 3

<div style="font-size: 0.5em;">

[https://github.com/mmartial/OpenInfra25-GenAI/tree/main/CoreAI/iti/04-Gemma3](https://github.com/mmartial/OpenInfra25-GenAI/tree/main/CoreAI/iti/04-Gemma3)

</div>

Instruction-tuned Vision Language Model (VLM) + LLM

- 🧩 Initialize tokenizer & model `pipeline`
- 💬 Prompting (text): prompt payload (system/user) to answer a question
- 🖼️ Multimodal: image understanding using interpretive analysis prompt
<br>
<div style="font-size: 0.7em;">

> "Describe the image. what is the field of expertise needed, explain the idea behind the meaning of the image?"


</div>

<hr>

<div style="font-size: 0.8em;">

- Gemma-3-4b-it [https://huggingface.co/google/gemma-3-4b-it](https://huggingface.co/google/gemma-3-4b-it)

</div>

--

### FLUX.1\[Schnell\]

<div style="font-size: 0.5em;">

[https://github.com/mmartial/OpenInfra25-GenAI/tree/main/CoreAI/iti/05-Flux1](https://github.com/mmartial/OpenInfra25-GenAI/tree/main/CoreAI/iti/05-Flux1)

</div>

12B text-to-image diffusion model, distilled for 1-4 steps fast inference

- 📦 Install & import libraries (`diffusers`, `transformers`)
- 🌀 Load FLUX.1 \[schnell\] (with `torch.bfloat16` precision)
- 🎨 Run inference: prompts + seeds → generate images

<hr>

<div style="font-size: 0.8em;">

- Flux.1 Schnell [https://huggingface.co/black-forest-labs/FLUX.1-schnell](https://huggingface.co/black-forest-labs/FLUX.1-schnell)

</div>
