# GenAI -- LLM

This repository contains the configuration files for the OpenInfra25-GenAI project.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/)
- a [Tailscale](https://tailscale.com/) account -- we will start the containers in a Tailscale network and NOT have any exposed ports
- an NVIDIA GPU with at least 24GB of VRAM
- [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)
- a [PyTorch](https://pytorch.org/)-enabled container, such as [Infotrend's CoreAI](https://github.com/Infotrend-Inc/CoreAI)'s repository

## Infotrend's CoreAI

[Infotrend's CoreAI](https://github.com/Infotrend-Inc/CoreAI) is a project designed to build Docker images to provide pre-configured environments for machine learning, computer vision, and natural language processing work.

In particular, these containers bundle together support for:
- CUDA / GPU acceleration
- [TensorFlow](https://www.tensorflow.org/)
- [PyTorch](https://pytorch.org/)
- [OpenCV](https://opencv.org/)
- [JupyterLab](https://jupyter.org/)

It supports both CPU- and GPU-based builds (by including or excluding CUDA), allows versioning of components, and includes configuration utilities to run as a non-root user.  ￼

Capabilities and features:
- **Consistent development environment**: By using `docker`, we get a reproducible stack with the exact versions of CUDA, TensorFlow, PyTorch, OpenCV, and other dependencies packaged. This avoids “it works on my machine” issues.
- **Fast prototyping / testing**: Because the environment is ready to go, we can begin working (e.g. launching Jupyter Lab) immediately without spending time installing and configuring all the ML/CV/NLP libraries and their dependencies.
- **Versioned builds**: The project provides multiple tagged builds (e.g. specifying which components are included — CUDA, TensorFlow, etc. — and which versions) so we can pick a container that exactly fits our needs.
- **Non-root user execution**: Containers are configured to run under a non-root user (the `coreai` user), with flexibility in setting the `UID`/`GID` to align with host system users. This is better for security and avoiding file-permission headaches with mounted volumes.
- **JupyterLab integration**: The container runs JupyterLab (by default on port `8888`), so we can interactively develop, run notebooks, visualize, etc.
- **Custom builds / flexibility**: We can build custom images (for example, including TensorRT, or building TF/PyTorch from source) via provided` Dockerfile`s or a `Makefile` system.

The GenAI demos we will perform are adapted from [Infotrend's CoreAI Demo Projects](https://github.com/Infotrend-Inc/CoreAI-DemoProjects).

In particular, to support local-to-the-system's LLMs, we have added the [Ollama](https://github.com/ollama/ollama) container to the [compose.yaml](./compose.yaml) file.

## Tailscale

[Tailscale](https://tailscale.com/) is a secure, easy-to-use overlay VPN that creates a private network connecting our devices. 
Built on top of the [WireGuard](https://www.wireguard.com/) protocol, it automatically handles NAT traversal, encryption, and authentication, so we can securely access devices and services as if they were on the same local network.

For a primer, see [How Tailscale works](https://tailscale.com/blog/how-tailscale-works). 
For homelab access, see [Tailscale: subnet router & remote services access](https://www.gkr.one/blg-20250323-tailscale).

The containers will be started in a Tailscale network and have no ports exposed on the host, only on the Tailscale network (our `tailnet`).

## Installation

After cloning this repository, in the folder where this `README.md` is located, run `cp compose.yaml.example compose.yaml` and edit the `compose.yaml` file to replace two components:
- in the ` oi25-coreai-cpo` service, replace `WANTED_UID` and `WANTED_GID` with your user ID and group ID (obtain with `id -u` and `id -g`)
- in the `tailscale-oi25-coreai` service, replace `TS_AUTHKEY` with your Tailscale auth key

For details on Tailscale auth keys, see [Tailscale's documentation](https://tailscale.com/kb/1282/docker).
If logged into your Tailscale dashboard, go to [https://login.tailscale.com/admin/settings/keys](https://login.tailscale.com/admin/settings/keys) and in the "Auth Keys" section, click on "Gemerate Auth Key..." button. The [Tailscale-AuthKey](./Tailscale-AuthKey.png) shows an example of the settings that could be used.

After editing the `compose.yaml` file, run `docker compose up -d` to start the containers.

The containers will take a few minutes to start up.  

Once the containers are up and running, obtain the `100.` IP address from your Tailscale dashboard, and access the JupyterLab at `http://localhost:8888`. 
