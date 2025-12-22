# Nix builder for CI container image builds
# Pre-installed Nix with flakes enabled for faster container builds

FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

# Install required packages for Nix and GitHub Actions
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    xz-utils \
    ca-certificates \
    git \
    sudo \
    && rm -rf /var/lib/apt/lists/*

# Create nixbld group (required by Nix installer even in single-user mode)
RUN groupadd -g 30000 nixbld

# Configure Nix for flakes and container-friendly settings
RUN mkdir -p /etc/nix && \
    echo "experimental-features = nix-command flakes" > /etc/nix/nix.conf && \
    echo "sandbox = false" >> /etc/nix/nix.conf && \
    echo "filter-syscalls = false" >> /etc/nix/nix.conf && \
    echo "build-users-group =" >> /etc/nix/nix.conf

# Install Nix (single-user mode for containers)
RUN curl -L https://nixos.org/nix/install | sh -s -- --no-daemon

ENV PATH="/root/.nix-profile/bin:/nix/var/nix/profiles/default/bin:${PATH}"

# Source nix profile and pre-cache nixpkgs channel for faster first builds
RUN . /root/.nix-profile/etc/profile.d/nix.sh && \
    nix-channel --add https://nixos.org/channels/nixos-25.05 nixpkgs && \
    nix-channel --update

WORKDIR /workspace
