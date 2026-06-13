FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive \
    PATH=/opt/miniconda3/bin:$PATH

RUN set -eux; \
    apt-get update && \
    apt-get install -y --no-install-recommends \
        wget bzip2 ca-certificates git parallel \
        libopenblas-dev cmake && \
    rm -rf /var/lib/apt/lists/*

RUN set -eux; \
    wget -q https://github.com/conda-forge/miniforge/releases/download/24.9.2-0/Miniforge3-24.9.2-0-Linux-x86_64.sh \
         -O /tmp/miniforge.sh && \
    bash /tmp/miniforge.sh -b -p /opt/miniconda3 && \
    rm /tmp/miniforge.sh && \
    conda clean -afy

RUN conda create -n cnmf_env --yes \
        -c conda-forge -c bioconda \
        python \
        fastcluster matplotlib numpy \
        palettable pandas scipy \
        scikit-learn pyyaml scanpy \
        parallel jupyterlab cnmf harmonypy scikit-misc ipython\
    && conda clean -afy
RUN  /opt/miniconda3/envs/cnmf_env/bin/ipython kernel install --user --name=cnmf
WORKDIR /Works
COPY <<'EOF' /entrypoint.sh
#!/bin/bash
set -e
conda init
source ~/.bashrc

exec "$@"
EOF
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
CMD ["/bin/bash"]
