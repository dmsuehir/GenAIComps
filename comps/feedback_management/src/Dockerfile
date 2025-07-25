# Copyright (C) 2024 Intel Corporation
# SPDX-License-Identifier: Apache-2.0

FROM python:3.11-slim

ENV LANG=C.UTF-8

RUN apt-get update -y && apt-get install -y --no-install-recommends --fix-missing \
    build-essential \
    libjemalloc-dev \
    libgl1-mesa-glx

RUN useradd -m -s /bin/bash user && \
    mkdir -p /home/user && \
    chown -R user /home/user/

COPY comps /home/user/comps
COPY requirements.txt /home/user/

ARG uvpip='uv pip install --system --no-cache-dir'
RUN pip install --no-cache-dir --upgrade pip setuptools uv && \
    $uvpip -r /home/user/comps/feedback_management/src/requirements.txt && \
    $uvpip -r /home/user/requirements.txt

ENV PYTHONPATH=$PYTHONPATH:/home/user

USER user

WORKDIR /home/user/comps/feedback_management/src

ENTRYPOINT ["python", "opea_feedback_microservice.py"]
