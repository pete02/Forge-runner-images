FROM ghcr.io/catthehacker/ubuntu:act-22.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        ca-certificates \
        curl \
        docker.io \
        git \
    && rm -rf /var/lib/apt/lists/*

RUN docker --version && docker compose version

WORKDIR /workspace