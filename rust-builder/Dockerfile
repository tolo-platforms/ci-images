# Rust builder for CI - cross-compilation for musl targets
# Pre-installed: Rust, sccache, musl cross-compilers, rustfmt, clippy

FROM rust:1.83-bookworm

ENV DEBIAN_FRONTEND=noninteractive

# Install build dependencies and cross-compilation toolchains
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    curl \
    git \
    pkg-config \
    libssl-dev \
    musl-tools \
    && rm -rf /var/lib/apt/lists/*

# Install musl cross-compilers
RUN curl -sL https://musl.cc/x86_64-linux-musl-cross.tgz | tar -xzC /opt \
    && curl -sL https://musl.cc/aarch64-linux-musl-cross.tgz | tar -xzC /opt

ENV PATH="/opt/x86_64-linux-musl-cross/bin:/opt/aarch64-linux-musl-cross/bin:${PATH}"

# Install sccache for build caching
RUN curl -sL https://github.com/mozilla/sccache/releases/download/v0.8.2/sccache-v0.8.2-x86_64-unknown-linux-musl.tar.gz \
    | tar -xzf - --strip-components=1 -C /usr/local/bin sccache-v0.8.2-x86_64-unknown-linux-musl/sccache \
    && chmod +x /usr/local/bin/sccache

# Install Rust components and musl targets
RUN rustup component add rustfmt clippy \
    && rustup target add x86_64-unknown-linux-musl aarch64-unknown-linux-musl

WORKDIR /workspace
