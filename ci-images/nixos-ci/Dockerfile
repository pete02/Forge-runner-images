FROM nixos/nix:latest

RUN mkdir -p /etc/nix \
    && printf 'experimental-features = nix-command flakes\n' >> /etc/nix/nix.conf

RUN mkdir -p /tmp/ci-tools-flake /opt \
    && printf '%s\n' \
      '{' \
      '  inputs.nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";' \
      '  outputs = { self, nixpkgs }: {' \
      '    packages.x86_64-linux.default = let' \
      '      pkgs = import nixpkgs { system = "x86_64-linux"; };' \
      '    in pkgs.buildEnv {' \
      '      name = "ci-tools";' \
      '      paths = [ pkgs.nodejs pkgs.git pkgs.openssh ];' \
      '    };' \
      '  };' \
      '}' > /tmp/ci-tools-flake/flake.nix \
    && nix build /tmp/ci-tools-flake#default \
      --out-link /opt/ci-tools \
    && test -x /opt/ci-tools/bin/node \
    && test -x /opt/ci-tools/bin/git \
    && test -x /opt/ci-tools/bin/ssh

# Added after the tools build so image builds don't depend on aria being reachable
RUN printf '%s\n' \
      'substituters = http://aria.lan:5000 https://cache.nixos.org' \
      'trusted-public-keys = aria-cache-1:wmPehLsGmw8SlhWM7RvVcFYa0E9+Y97gxSJY2TUkg/c= cache.nixos.org-1:6NCHdD59X431o0gWypbMrAURkbJ16ZPMQFGspcDShjY=' \
      'connect-timeout = 3' \
      'fallback = true' \
      >> /etc/nix/nix.conf

ENV PATH="/opt/ci-tools/bin:${PATH}"

WORKDIR /workspace

ENTRYPOINT []
CMD ["/bin/sh"]