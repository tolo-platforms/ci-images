# Publisher image for CI container publishing
# Pre-installed skopeo and manifest-tool for faster image publishing

FROM debian:bookworm-slim

ENV DEBIAN_FRONTEND=noninteractive

# Install skopeo and ca-certificates
RUN apt-get update && apt-get install -y --no-install-recommends \
    skopeo \
    ca-certificates \
    curl \
    bash \
    && rm -rf /var/lib/apt/lists/*

# Install manifest-tool for the current architecture
RUN ARCH=$(dpkg --print-architecture) && \
    curl -sL "https://github.com/estesp/manifest-tool/releases/download/v2.1.9/binaries-manifest-tool-2.1.9.tar.gz" \
    | tar -xzf - "manifest-tool-linux-${ARCH}" && \
    mv "manifest-tool-linux-${ARCH}" /usr/local/bin/manifest-tool && \
    chmod +x /usr/local/bin/manifest-tool

WORKDIR /workspace
