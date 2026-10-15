
FROM ghcr.io/catthehacker/ubuntu:act-22.04

ENV CARGO_HOME=/root/.cargo
ENV RUSTUP_HOME=/root/.rustup
ENV PATH=/root/.cargo/bin:${PATH}

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        ca-certificates \
        curl \
        git \
        build-essential \
        pkg-config \
        libssl-dev \
        libwayland-dev \
        libxkbcommon-dev \
        libgtk-3-dev \
    && rm -rf /var/lib/apt/lists/*

RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs \
    | sh -s -- -y --profile minimal --default-toolchain stable

RUN rustup component add rustfmt clippy \
    && rustc --version \
    && cargo --version

WORKDIR /workspace
