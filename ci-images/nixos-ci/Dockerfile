FROM nixos/nix:latest

# Only generic settings here. No hostnames, keys or tokens.
RUN mkdir -p /etc/nix \
    && printf '%s\n' \
      'experimental-features = nix-command flakes' \
      'connect-timeout = 3' \
      'fallback = true' \
      >> /etc/nix/nix.conf


ENV NIX_CONFIG="experimental-features = nix-command flakes"


RUN mkdir -p /opt/ci-tools-flake \
    && printf '%s\n' \
      '{' \
      '  inputs.nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";' \
      '  outputs = { self, nixpkgs }: let' \
      '    systems = [ "x86_64-linux" "aarch64-linux" ];' \
      '    forAllSystems = nixpkgs.lib.genAttrs systems;' \
      '  in {' \
      '    packages = forAllSystems (system: let' \
      '      pkgs = nixpkgs.legacyPackages.${system};' \
      '    in {' \
      '      default = pkgs.buildEnv {' \
      '        name = "ci-tools";' \
      '        paths = [ pkgs.nodejs pkgs.git pkgs.attic-client ];' \
      '      };' \
      '    });' \
      '  };' \
      '}' > /opt/ci-tools-flake/flake.nix \
    && nix build /opt/ci-tools-flake#default --out-link /opt/ci-tools \
    && test -x /opt/ci-tools/bin/node

ENV PATH="/opt/ci-tools/bin:${PATH}"

WORKDIR /workspace

ENTRYPOINT []
CMD ["/bin/sh"]