
FROM nixos/nix:latest

ENV NIX_CONFIG="experimental-features = nix-command flakes"

RUN nix --extra-experimental-features 'nix-command flakes' profile install \
    nixpkgs#nodejs \
    nixpkgs#git
    
RUN nix --version \
    && git --version

WORKDIR /workspace

ENTRYPOINT []
CMD ["/bin/sh"]
