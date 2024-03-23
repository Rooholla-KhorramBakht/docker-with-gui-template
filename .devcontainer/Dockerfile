FROM ubuntu:20.04
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && apt-get install -y -qq --no-install-recommends \ 
                                      libglvnd-dev \
                                      libgl1-mesa-dev \
                                      libegl1-mesa-dev \
                                      libgles2-mesa-dev \
                                      libxext6 \
                                      libx11-6 \
                                      freeglut3-dev \
                                      && rm -rf /var/lib/apt/lists/*

# Env vars for the nvidia-container-runtime.
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES graphics,utility,compute