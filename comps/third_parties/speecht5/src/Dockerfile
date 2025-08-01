# Copyright (C) 2024 Intel Corporation
# SPDX-License-Identifier: Apache-2.0

FROM python:3.11-slim
RUN useradd -m -s /bin/bash user && \
    mkdir -p /home/user && \
    chown -R user /home/user/

# Set environment variables
ENV LANG=en_US.UTF-8
ENV PYTHONPATH=/home/user
ARG ARCH=cpu

# Install system dependencies
RUN apt-get update && apt-get install -y --no-install-recommends --fix-missing \
    curl \
    ffmpeg

COPY --chown=user:user comps /home/user/comps

ARG uvpip='uv pip install --system --no-cache-dir'
RUN pip install --no-cache-dir --upgrade pip setuptools uv && \
    if [ "${ARCH}" = "cpu" ]; then \
        $uvpip torch torchvision --index-url https://download.pytorch.org/whl/cpu ; \
        $uvpip -r /home/user/comps/third_parties/speecht5/src/requirements-cpu.txt ; \
    else \
        $uvpip -r /home/user/comps/third_parties/speecht5/src/requirements-gpu.txt ; \
    fi

ENV PYTHONPATH=$PYTHONPATH:/home/user

USER user

WORKDIR /home/user/comps/third_parties/speecht5/src

# air gapped support
RUN curl -sLJO https://raw.githubusercontent.com/intel/intel-extension-for-transformers/main/intel_extension_for_transformers/neural_chat/assets/speaker_embeddings/spk_embed_default.pt
RUN curl -sLJO https://raw.githubusercontent.com/intel/intel-extension-for-transformers/main/intel_extension_for_transformers/neural_chat/assets/speaker_embeddings/spk_embed_male.pt

ENTRYPOINT ["python", "speecht5_server.py", "--device", "cpu"]
