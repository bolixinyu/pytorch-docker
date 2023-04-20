# registry.cn-hongkong.aliyuncs.com/chenyaofo/chatgpt:py310-pt113-cu117-ds
FROM nvidia/cuda:11.7.0-devel-ubuntu22.04

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8 \
    PATH=/opt/conda/bin:$PATH \
    PYTHON_VERSION=3.10

RUN APT_INSTALL="apt-get install -y --no-install-recommends --no-install-suggests" && \
    GIT_CLONE="git clone --depth 10" && \
    rm -rf /etc/apt/sources.list.d/cuda.list \
           /etc/apt/sources.list.d/nvidia-ml.list && \
    rm -f /etc/apt/apt.conf.d/docker-clean && \
    apt-get update && \
    # libaio-dev for building deepspeed
    DEBIAN_FRONTEND=noninteractive $APT_INSTALL ca-certificates git wget libaio-dev cmake && \
    apt-get clean

RUN wget https://github.com/conda-forge/miniforge/releases/latest/download/Mambaforge-Linux-x86_64.sh -O ~/installer.sh && \
    /bin/bash ~/installer.sh -b -p /opt/conda && \
    rm ~/installer.sh && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate dev" >> ~/.bashrc && \
    find /opt/conda/ -follow -type f -name '*.a' -delete && \
    find /opt/conda/ -follow -type f -name '*.js.map' -delete

RUN /opt/conda/bin/mamba create -n dev python=${PYTHON_VERSION} && \
    CONDA_INSTALL="/opt/conda/bin/mamba install -n dev -y" && \
    /opt/conda/bin/mamba clean -afy && \
    echo 'Conda Install Done!'

RUN PIP_INSTALL="/opt/conda/envs/dev/bin/pip install --no-cache-dir" && \
    $PIP_INSTALL torch==1.13.1+cu117 torchvision==0.14.1+cu117 torchaudio==0.13.1 --extra-index-url https://download.pytorch.org/whl/cu117 && \
    $PIP_INSTALL deepspeed && \
    $PIP_INSTALL pyhocon && \
    $PIP_INSTALL git+https://gitee.com/chenyaofo/torch4x@nightly && \
    $PIP_INSTALL git+https://github.com/huggingface/transformers && \
    $PIP_INSTALL sentencepiece datasets protobuf huggingface_hub
