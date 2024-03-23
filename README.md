# Docker with GUI/GPU Support
This is a template devcontainer environment with graphics and GPU support. The GL Vendor-Neutral Dispatch Library (glvnd) is an abstraction layer that sends the openGL commands to the right driver and for proper graphics support, and should be installed withing the container. Specifically, the following packages must be present within the container. Additionally, NVIDIA container runtime needs a few environmental variable to be set. With all that, the `Dockerfile` looks like the following:

```docker
FROM ubuntu:20.04 # Or any other image
RUN apt-get update \
  && apt-get install -y -qq --no-install-recommends \
    libglvnd0 \
    libgl1 \
    libglx0 \
    libegl1 \
    libxext6 \
    libx11-6 \
  && rm -rf /var/lib/apt/lists/*# Env vars for the nvidia-container-runtime.
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES graphics,utility,compute
```

During running the container, the `X`'s socket directory `/tmp/.X11-unix` should be mounted into the container and environmental variable `DISPLAY` must be set to the corresponding value in the host. Furthermore, in case an NVIDIA GPU is to be used, follow through [here](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html) to install and configure the `nvidia-container-toolkit` and enable the corresponding `--gpus all` in `docker run` or `runtime: nvidia` in the docker-compose file. So, the corresponding docker compose file is:
```yaml
version: "3.9"
services:
  docker-with-gui-template:
    build: .
    container_name: docker-with-gui-template-container
    network_mode: host
    # privileged: true
    command: bash
    volumes:
      - /tmp/.X11-unix:/tmp/.X11-unix
    environment:
      - DISPLAY=${DISPLAY}
      - QT_X11_NO_MITSHM=1
    runtime: nvidia
    stdin_open: true
    tty: true
```
**Note:** The `privileged` flag can be set to true for case where we need to directly communicate with the hardwares attached to the host or when we have a preempt-RT kernel and want to run real-time threads within the container. 

With this, run the container by navigating to root path of the `Dockerfile` and `docker-compose.yaml` file and run the following:
```bash
docker compose up
```
Before running any GUI related app, we need to allow connection to `Xserver` on the host computer. For this run the following within the host terminal:
```bash
xhost +local:root
```
**Note** There are security implications to exposing X in this manner. For more detail read [here](http://wiki.ros.org/docker/Tutorials/GUI).

## Running Using the VSCode
A `devcontainer.json` file is provided in this repository. Simply open this repository using the vscode and run `Rebuild and Reopen in container` in the command terminal. Note that the Microsoft Dev Containers extension should be installed prior to this. 

## Sources
[OpenGL and CUDA Applications in Docker](https://medium.com/@benjamin.botto/opengl-and-cuda-applications-in-docker-af0eece000f1) \
[Using GUIs with Docker](http://wiki.ros.org/docker/Tutorials/GUI)


