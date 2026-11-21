FROM nixos/nix:latest

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
      '        paths = with pkgs; [' \
      '          nodejs git attic-client bash coreutils curl' \
      '          gnugrep gnused findutils gnutar gzip xz cacert' \
      '        ];' \
      '      };' \
      '    });' \
      '  };' \
      '}' > /opt/ci-tools-flake/flake.nix \
    && nix build /opt/ci-tools-flake#default --out-link /opt/ci-tools

# Static busybox for the runner's bootstrap commands (lives outside /nix on purpose)
RUN mkdir -p /opt/bootstrap/bin /bin \
    && cp -L "$(nix build github:NixOS/nixpkgs/nixos-unstable#pkgsStatic.busybox --no-link --print-out-paths)/bin/busybox" /opt/bootstrap/bin/busybox \
    && for c in tail sh env cat sleep; do \
         ln -sf /opt/bootstrap/bin/busybox "/opt/bootstrap/bin/$c"; \
         ln -sf /opt/bootstrap/bin/busybox "/bin/$c"; \
       done

# Expose nix and the CI tools on PATH
RUN mkdir -p /usr/local/bin \
    && for b in /nix/var/nix/profiles/default/bin/* /opt/ci-tools/bin/*; do \
         [ -e "$b" ] && ln -sf "$b" "/usr/local/bin/$(basename "$b")"; \
       done

ENV PATH="/usr/local/bin:/opt/ci-tools/bin:/opt/bootstrap/bin:${PATH}"
ENV SSL_CERT_FILE=/opt/ci-tools/etc/ssl/certs/ca-bundle.crt

# Fail the image build, not the pipeline, if anything is missing
RUN node --version && nix --version && git --version && attic --version \
    && bash --version | head -n1 && curl --version | head -n1

WORKDIR /workspace
ENTRYPOINT []
CMD ["/bin/sh"]