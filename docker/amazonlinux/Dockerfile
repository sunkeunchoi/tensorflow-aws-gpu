FROM amazonlinux

RUN yum update -y && \
    yum install -y sudo xz util-linux module-init-tools 

#./NVIDIA-Linux-x86_64-${GPU_DRIVER_VERSION}.run -s --no-kernel-module && \
#export GPU_DRIVER_VERSION=375.26  
ENV GPU_DRIVER_VERSION 375.26 
RUN cd /tmp && \
    curl -LO "http://us.download.nvidia.com/XFree86/Linux-x86_64/${GPU_DRIVER_VERSION}/NVIDIA-Linux-x86_64-${GPU_DRIVER_VERSION}.run" && \
    chmod +x ./NVIDIA-Linux-x86_64-${GPU_DRIVER_VERSION}.run && \
    ./NVIDIA-Linux-x86_64-${GPU_DRIVER_VERSION}.run -s && \
    rm -rf /tmp/*
#export NV_DOCKER_VERSION=1.0.1 
ENV NV_DOCKER_VERSION 1.0.1
RUN cd /tmp && \
    curl -LO "https://github.com/NVIDIA/nvidia-docker/releases/download/v${NV_DOCKER_VERSION}/nvidia-docker_${NV_DOCKER_VERSION}_amd64.tar.xz" && \
    tar --strip-components=1 -C /usr/bin -xvf /tmp/nvidia-docker*.tar.xz && \
    rm /tmp/nvidia-docker*.tar.xz

# Run nvidia-docker-plugin
RUN nohup nvidia-docker-plugin > /tmp/nvidia-docker.log
RUN nvidia-docker run --rm nvidia/cuda nvidia-smi
