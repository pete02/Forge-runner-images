FROM nixos/nix:latest

# Only generic settings here. No hostnames, keys or tokens.
RUN mkdir -p /etc/nix \
    && printf '%s\n' \
      'experimental-features = nix-command flakes' \
      'connect-timeout = 3' \
      'fallback = true' \
      >> /etc/nix/nix.conf

RUN mkdir -p /tmp/ci-tools-flake /opt \
    && printf '%s\n' \
      '{' \
      '  inputs.nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";' \
      '  outputs = { self, nixpkgs }: {' \
      '    packages.x86_64-linux.default = let' \
      '      pkgs = import nixpkgs { system = "x86_64-linux"; };' \
      '    in pkgs.buildEnv {' \
      '      name = "ci-tools";' \
      '      paths = [ pkgs.nodejs pkgs.git pkgs.attic-client ];' \
      '    };' \
      '  };' \
      '}' > /tmp/ci-tools-flake/flake.nix \
    && nix build /tmp/ci-tools-flake#default \
      --out-link /opt/ci-tools \
    && test -x /opt/ci-tools/bin/node \
    && test -x /opt/ci-tools/bin/git \
    && test -x /opt/ci-tools/bin/attic

ENV PATH="/opt/ci-tools/bin:${PATH}"

WORKDIR /workspace

ENTRYPOINT []
CMD ["/bin/sh"]