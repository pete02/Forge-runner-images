
FROM ghcr.io/catthehacker/ubuntu:act-22.04

ARG GO_VERSION=1.25.3
ARG GOLANGCI_LINT_VERSION=1.64.8

ENV GOPATH=/root/go
ENV PATH=/usr/local/go/bin:/root/go/bin:${PATH}

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        ca-certificates \
        curl \
        git \
        build-essential \
        pkg-config \
    && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL \
        "https://go.dev/dl/go${GO_VERSION}.linux-amd64.tar.gz" \
        -o /tmp/go.tar.gz \
    && tar -C /usr/local -xzf /tmp/go.tar.gz \
    && rm /tmp/go.tar.gz

RUN curl -fsSL \
        "https://raw.githubusercontent.com/golangci/golangci-lint/v${GOLANGCI_LINT_VERSION}/install.sh" \
    | sh -s -- -b /usr/local/bin "v${GOLANGCI_LINT_VERSION}"

RUN go version \
    && golangci-lint --version

WORKDIR /workspace
