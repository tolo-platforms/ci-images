# CI Images

Shared container images for CI/CD pipelines across Tolo platform repositories.

## Images

| Image | Description | Usage |
|-------|-------------|-------|
| `rust-builder` | Rust toolchain with sccache, musl cross-compilers | Build Rust binaries for linux/amd64 and linux/arm64 |
| `nix-builder` | Nix with flakes enabled | Build container images using `dockerTools.buildLayeredImage` |
| `publisher` | skopeo + manifest-tool | Publish container images to registries |

## Usage

```yaml
jobs:
  build:
    runs-on: self-hosted
    container:
      image: ghcr.io/tolo-platforms/ci-images/rust-builder:latest
```

## Building

Images are automatically built and pushed to GHCR on push to main.

To build locally:
```bash
docker build -t rust-builder ./rust-builder
docker build -t nix-builder ./nix-builder
docker build -t publisher ./publisher
```
