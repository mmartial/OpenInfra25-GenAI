FROM infotrend/coreai:25b01-cpo-12.6.3_2.6.0_4.11.0

ARG GPU_GROUP_ID=1002
RUN sudo groupadd gpudev -g ${GPU_GROUP_ID}
RUN sudo usermod -a -G gpudev coreaitoo
RUN sudo usermod -a -G gpudev coreai

EXPOSE 8888

USER coreaitoo
WORKDIR /iti

ENTRYPOINT ["/entrypoint.sh"]
