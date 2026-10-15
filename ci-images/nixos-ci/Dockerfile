FROM nixos/nix:latest

RUN mkdir -p /etc/nix \
    && printf 'experimental-features = nix-command flakes\n' >> /etc/nix/nix.conf

RUN printf '%s\n' \
    '{' \
    '  inputs.nixpkgs.url = "nixpkgs/nixos-unstable";' \
    '  outputs = { self, nixpkgs }: {' \
    '    packages.x86_64-linux.default = let' \
    '      pkgs = import nixpkgs { system = "x86_64-linux"; };' \
    '    in pkgs.buildEnv {' \
    '      name = "ci-tools";' \
    '      paths = [ pkgs.nodejs pkgs.git ];' \
    '    };' \
    '  };' \
    '}' > /tmp/flake.nix \
    && nix build --file /tmp/flake.nix \
       --out-link /opt/ci-tools

ENV PATH="/opt/ci-tools/bin:${PATH}"

WORKDIR /workspace

ENTRYPOINT []
CMD ["/bin/sh"]