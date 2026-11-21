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
      '    systems = [ "x86_64-linux" "aarch64-linux" "i686-linux" ];' \
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
    && test -x /opt/ci-tools/bin/node \
    && mkdir -p /usr/local/bin \
    && for b in /opt/ci-tools/bin/*; do ln -sf "$b" "/usr/local/bin/$(basename "$b")"; done

RUN mkdir -p /opt/bootstrap/bin /bin \
    && cp -L "$(nix build github:NixOS/nixpkgs/nixos-unstable#pkgsStatic.busybox --no-link --print-out-paths)/bin/busybox" /opt/bootstrap/bin/busybox \
    && for c in tail sh env cat sleep; do \
         ln -sf /opt/bootstrap/bin/busybox "/opt/bootstrap/bin/$c"; \
         ln -sf /opt/bootstrap/bin/busybox "/bin/$c"; \
       done

ENV PATH="/usr/local/bin:/opt/ci-tools/bin:/opt/bootstrap/bin:${PATH}"

WORKDIR /workspace

ENTRYPOINT []
CMD ["/bin/sh"]