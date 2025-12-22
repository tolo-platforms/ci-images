# Rust builder for CI - cross-compilation for musl targets
# Pre-installed: Rust, sccache, musl cross-compilers, rustfmt, clippy
# NOTE: This image must run on x86_64 hosts (GitHub-hosted runners or x86_64 self-hosted)

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

# Install musl cross-compilers from GitHub releases
# These are x86_64-hosted toolchains - image must run on x86_64
RUN curl -sL https://github.com/musl-cross/musl-cross/releases/download/20241103/x86_64-unknown-linux-musl.tar.xz | xz -d | tar -xC /opt \
    && curl -sL https://github.com/musl-cross/musl-cross/releases/download/20241103/aarch64-unknown-linux-musl.tar.xz | xz -d | tar -xC /opt

ENV PATH="/opt/x86_64-unknown-linux-musl/bin:/opt/aarch64-unknown-linux-musl/bin:${PATH}"

# Create symlinks for expected linker names (cargo config expects x86_64-linux-musl-gcc)
RUN ln -s /opt/x86_64-unknown-linux-musl/bin/x86_64-unknown-linux-musl-gcc /usr/local/bin/x86_64-linux-musl-gcc \
    && ln -s /opt/aarch64-unknown-linux-musl/bin/aarch64-unknown-linux-musl-gcc /usr/local/bin/aarch64-linux-musl-gcc

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
