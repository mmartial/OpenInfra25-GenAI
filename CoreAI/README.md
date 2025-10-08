# GenAI -- CoreAI

Those GenAI demos are adapted from [Infotrend's CoreAI Demo Projects](https://github.com/Infotrend-Inc/CoreAI-DemoProjects).

To demonstrate local-to-the-instance's LLMs, the [Ollama](https://github.com/ollama/ollama) container was added to the proposed [compose.yaml](./compose.yaml) file.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
  - [Docker Compose](https://docs.docker.com/compose/)
- a [Tailscale](https://tailscale.com/) account
  - containers will be used in a Tailscale network (`tailnet`) and have no exposed ports
- [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)
  - an NVIDIA GPU with at least 24GB of VRAM
- a [PyTorch](https://pytorch.org/)-enabled container, such as [Infotrend's CoreAI](https://github.com/Infotrend-Inc/CoreAI)

## Infotrend's CoreAI

[Infotrend's CoreAI](https://github.com/Infotrend-Inc/CoreAI) is a tool designed to build Docker images to provide pre-configured environments for Machine Learning (ML) and Computer Vision (CV) projects.

In particular, these containers bundle together support for:

- CUDA / GPU acceleration
- [TensorFlow](https://www.tensorflow.org/)
- [PyTorch](https://pytorch.org/)
- [OpenCV](https://opencv.org/)
- [JupyterLab](https://jupyter.org/)

It supports both CPU- and GPU-based builds (by including or excluding CUDA), allows versioning of components, and includes configuration utilities that enables running as a non-root user.

Capabilities and features:

- **Consistent development environment**: Using `docker` or `podman` with a reproducible container-based software stack with specific versions of CUDA, TensorFlow, PyTorch, OpenCV, and other dependencies packaged. This enhances deployment and re-deployment of projects built with the tool (by using it as a `FROM` in `Dockerfile`s).
- **Fast prototyping/testing**: pre-built containers are "ready to use" --use it `FROM`, start a `/bin/bash` or launch a Jupyter Lab-- immediately without spending time installing and configuring all the ML/CV libraries and their dependencies.
- **Versioned builds**: The project provides multiple tagged builds specifying which components are included — CUDA, TensorFlow, etc. and which versions.
- **Non-root user execution**: Containers are configured to run under a non-root user (the `coreai` user), with flexibility in setting the `UID`/`GID` to align with host system users, simplifying both security and file-permission with mounted volumes.
- **JupyterLab integration**: The container runs JupyterLab (on port `8888` when enabled), so ML engineers can interactively develop and visualize.
- **Custom builds / flexibility**: Ability to build custom images (TensorRT, or building TensorFlow/PyTorch from source) via provided `Dockerfile`s.

## Tailscale

[Tailscale](https://tailscale.com/) is a secure, easy-to-use overlay VPN that creates a private network connecting our devices.
Built on top of the [WireGuard](https://www.wireguard.com/) protocol, it automatically handles NAT traversal, encryption, and authentication, to securely access devices and services as if they were on the same local network.

For a primer, see [How Tailscale works](https://tailscale.com/blog/how-tailscale-works).

For homelab access, see [Tailscale: Subnet Router & Remote Services Access](https://www.gkr.one/blg-20250323-tailscale).

The containers will be started in a Tailscale network and have no ports exposed on the host, only on the Tailscale network (our `tailnet`).

## Installation

After cloning this repository, in the folder where this `README.md` is located, run `cp compose.yaml.example compose.yaml` and edit the `compose.yaml` file to replace two components:

- in the `oi25-coreai-cpo` service, replace `WANTED_UID` and `WANTED_GID` with the expected user ID and group ID (obtained with `id -u` and `id -g`)
- in the `tailscale-oi25-coreai` service, replace `TS_AUTHKEY` with the user's Tailscale auth key

For details on Tailscale auth keys, see [Tailscale's documentation](https://tailscale.com/kb/1282/docker).
If logged into the Tailscale dashboard, go to [https://login.tailscale.com/admin/settings/keys](https://login.tailscale.com/admin/settings/keys) and in the "Auth Keys" section, click on "Generate Auth Key...". [Tailscale-AuthKey](./Tailscale-AuthKey.png) shows an example of the settings that could be used.

After editing the `compose.yaml` file, run `docker compose up -d` to start the containers. It is possible to view the logs of a started service using `docker compose logs -f`.

The containers will take a few minutes to start up. During this process, the `CoreAI` container will mount the local `iti` directory to the `/iti` directory within the container. This will allow access to the Jupyter Notebooks developed for these demos.

Once the containers are up and running, obtain the `100.` IP address of the `tailscale-oi25-coreai` "machine" from the Tailscale dashboard, and access the JupyterLab at `http://<TAILSCALEIP>:8888`.

To turn off the compose stack, run `docker compose down` in the folder where the `compose.yaml` is located.

### Troubleshooting

Note: this should be done with the compose stack turned off.

When running on OpenStack, the GPU device(s) (usually `/dev/nvidia...`) might be owned by groups that do not exist within the container.
This will prevent proper access to the device for inference.
This can be checked using `nvidia-smi`. Although the running user might be able to access the command from a terminal on the OpenStack instance, the device might not be available within the `oi25-coreai-cpo` container. Check by using:

```bash
docker exec -it oi25-coreai-cpo /usr/bin/nvidia-smi
```

If `Failed to initialize NVML: Insufficient Permissions` appears, find the group of the `/dev/nvidia0` device to add the `coreai` and `coareaitoo` users to that group.

```bash
ls -ln /dev/nvidia0
### crw-rw---- 1 root 1002 195, 0 Oct  6 02:25 /dev/nvidia0
### here 1002 is the group
```

With this value, create a new `local` container built to add the users to the required group.
The `compose.yaml` file in the `CoreAI-local` folder must use the same `FROM` as the `infotrend/coreai:tag` tag used in the `compose.yaml` file. The version in the current folder matches the value present in the `compose.yaml.example` file. Adapt the `GPU_GROUP_ID` argument to match the needed group ID (in the above example, `1002`). The built container will be named `coreai:local`.

```bash
cd CoreAI-local
# adapt GPU_GROUP_ID to the value obtained above
docker build --build-arg GPU_GROUP_ID=1002 -t coreai:local .
```

After testing if the new container built does not return the error, modify the `compose.yaml` to use the new container (in the `oi25-coreai-cpo` service definition, comment/uncomment the main and alternate `image:` entry) before restarting the compose stack.

```bash
# Check if the problem is resolved
docker run --gpus all --rm -it coreai:local /usr/bin/nvidia-smi

# If it works, comment/uncomment the image: entry for the oi25-coreai-cpo service to replace the `image` field with `coreai:local`

# Restart the compose stack with the new image
docker compose up -d
```

After restarting the stack, confirm that `nvidia-smi` works as expected:

```bash
docker exec -it oi25-coreai-cpo /usr/bin/nvidia-smi
```
