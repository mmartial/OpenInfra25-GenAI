FROM mmartial/comfyui-nvidia-docker:latest

ARG GPU_GROUP_ID=1002
RUN sudo groupadd gpudev -g ${GPU_GROUP_ID}
RUN sudo usermod -a -G gpudev comfytoo
RUN sudo usermod -a -G gpudev comfy

EXPOSE 8188

USER comfytoo

ENTRYPOINT [ "/comfyui-nvidia_init.bash" ]
