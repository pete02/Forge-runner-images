
FROM nixos/nix:latest

ENV NIX_CONFIG="experimental-features = nix-command flakes"

RUN nix --version \
    && git --version

WORKDIR /workspace

ENTRYPOINT []
CMD ["/bin/sh"]
