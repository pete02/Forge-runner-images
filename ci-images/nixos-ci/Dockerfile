
FROM nixos/nix:latest

ENV NIX_CONFIG="experimental-features = nix-command flakes"

RUN nix-env -iA \
    nixpkgs.nodejs_20 \
    nixpkgs.git

RUN nix --version \
    && git --version

WORKDIR /workspace

ENTRYPOINT []
CMD ["/bin/sh"]
