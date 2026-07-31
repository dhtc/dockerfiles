# Use the official vllm image for gpu with Volta、Turing、Ampere、Ada Lovelace、Hopper、Blackwell architecture (7.0 <= Compute Capability <= 12.1)
# The default base image uses vLLM 0.21.0 with CUDA 13.0. For CUDA 12.9 environments, switch to the commented cu129 image below.
# Compute Capability version query (https://developer.nvidia.com/cuda-gpus)
# support x86_64 architecture and ARM(AArch64) architecture
# FROM vllm/vllm-openai:v0.21.0
FROM vllm/vllm-openai:v0.21.0-cu129

# Install libgl for opencv support & Noto fonts for Chinese characters
RUN apt-get update && \
    apt-get install -y \
        fonts-noto-core \
        fonts-noto-cjk \
        fontconfig \
        libgl1 && \
    fc-cache -fv && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install mineru from local source instead of the upstream PyPI release, so that
# any local change under ./mineru/ (e.g. models_download_utils.py mirror support)
# is actually used inside the image.
COPY . /tmp/MinerU
WORKDIR /tmp/MinerU
RUN python3 -m pip config set global.index-url https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple && \
    python3 -m pip install -U pip --break-system-packages && \
    python3 -m pip install '.[core]' --break-system-packages && \
    python3 -m pip cache purge && \
    rm -rf /tmp/MinerU

# huggingface_hub (used by hf_snapshot_download inside MinerU) and the auto-detect
# probe in mineru.utils.models_download_utils both honor HF_ENDPOINT, so the model
# download below will go through the mirror instead of huggingface.co.
ENV HF_ENDPOINT=https://hf-mirror.com

# Download models and update the configuration file
RUN /bin/bash -c "mineru-models-download -s huggingface -m all"

# Set the entry point to activate the virtual environment and run the command line tool
ENTRYPOINT ["/bin/bash", "-c", "export MINERU_MODEL_SOURCE=local && exec \"$@\"", "--"]
