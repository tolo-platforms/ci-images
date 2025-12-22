# Rust builder for CI - cross-compilation for musl targets
# Pre-installed: Rust, sccache, Zig (for cross-compilation), rustfmt, clippy

FROM rust:1.83-bookworm

ENV DEBIAN_FRONTEND=noninteractive

# Install build dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    curl \
    git \
    pkg-config \
    libssl-dev \
    xz-utils \
    && rm -rf /var/lib/apt/lists/*

# Install Zig for cross-compilation (works on any host architecture)
RUN ZIG_VERSION="0.13.0" && \
    ARCH=$(uname -m) && \
    curl -sL "https://ziglang.org/download/${ZIG_VERSION}/zig-linux-${ARCH}-${ZIG_VERSION}.tar.xz" \
    | tar -xJ --strip-components=1 -C /usr/local

# Create wrapper scripts for zig cc as cross-compilers
# Filter out --target flags since zig uses different target format
RUN printf '#!/bin/sh\nfor arg; do case "$arg" in --target=*) ;; *) set -- "$@" "$arg";; esac; shift; done\nexec /usr/local/zig cc -target x86_64-linux-musl "$@"\n' > /usr/local/bin/x86_64-linux-musl-gcc && \
    printf '#!/bin/sh\nfor arg; do case "$arg" in --target=*) ;; *) set -- "$@" "$arg";; esac; shift; done\nexec /usr/local/zig cc -target aarch64-linux-musl "$@"\n' > /usr/local/bin/aarch64-linux-musl-gcc && \
    chmod +x /usr/local/bin/x86_64-linux-musl-gcc /usr/local/bin/aarch64-linux-musl-gcc

# Install sccache for build caching (architecture-aware)
RUN ARCH=$(dpkg --print-architecture) && \
    case "$ARCH" in \
      amd64) SCCACHE_ARCH="x86_64-unknown-linux-musl" ;; \
      arm64) SCCACHE_ARCH="aarch64-unknown-linux-musl" ;; \
      *) echo "Unsupported architecture: $ARCH" && exit 1 ;; \
    esac && \
    curl -sL "https://github.com/mozilla/sccache/releases/download/v0.8.2/sccache-v0.8.2-${SCCACHE_ARCH}.tar.gz" \
    | tar -xzf - --strip-components=1 -C /usr/local/bin "sccache-v0.8.2-${SCCACHE_ARCH}/sccache" && \
    chmod +x /usr/local/bin/sccache

# Install Rust components and musl targets
RUN rustup component add rustfmt clippy \
    && rustup target add x86_64-unknown-linux-musl aarch64-unknown-linux-musl

WORKDIR /workspace
