# Recommended Moby Package Versions

## Purpose

This document provides recommended versions for Moby packages to ensure compatibility with Docker API 1.44+ and GitHub Codespaces infrastructure.

## Background

The Moby packages hosted on packages.microsoft.com are used by:
- GitHub Codespaces (devcontainers/features/docker-in-docker)
- Azure IoT Edge
- Other Microsoft container services

These packages were previously outdated and did not support the minimum Docker API version 1.44 required by GitHub Codespaces, causing errors like:
```
Error response from daemon: client version 1.43 is too old. 
Minimum supported API version is 1.44
```

## Docker API Version Mapping

| Docker/Moby Version | Docker API Version |
|---------------------|-------------------|
| 24.x                | 1.43              |
| 25.x                | 1.44              |
| 26.x                | 1.45              |
| 27.x                | 1.46              |
| 28.x                | 1.46              |
| 29.x                | 1.47              |

## Recommended Versions (January 2025)

The following versions are recommended for building Moby packages. These versions are compatible, well-tested, and support Docker API 1.46+:

### Core Components

| Package | Version | Commit Hash | Repository |
|---------|---------|-------------|------------|
| moby-engine | v27.5.1 | `4c9b3b011ae4c30145a7b344c870bdda01b454e2` | https://github.com/moby/moby.git |
| moby-cli | v27.5.1 | `4c9b3b011ae4c30145a7b344c870bdda01b454e2` | https://github.com/moby/moby.git |
| moby-containerd | v2.2.1 | `b4360bae32f7d0571150dab8258809695f684834` | https://github.com/containerd/containerd.git |
| moby-runc | v1.4.0 | `5863f3dadfc1cf877e87c9010acd54e2310cf9d6` | https://github.com/opencontainers/runc.git |
| moby-buildx | v0.20.1 | `245093b99ab74aa2c729a496759afca0704d6470` | https://github.com/docker/buildx.git |
| moby-compose | v2.31.0 | `a8469db83f514a5abe4681c7fee773061f1941c6` | https://github.com/docker/compose.git |

### Supporting Components

| Package | Current Version | Repository |
|---------|-----------------|------------|
| moby-tini | v0.19.0 | https://github.com/krallin/tini.git |
| moby-containerd-shim-systemd | v0.1.0+ | https://github.com/containerd/containerd-shim-systemd-v1.git |

## Version Selection Rationale

### moby-engine v27.5.1
- **API Support**: Docker API 1.46
- **Stability**: Latest patch release in the 27.x LTS series
- **Security**: Includes fixes for CVE-2024-45341 and CVE-2024-45336
- **Compatibility**: Well-tested and widely deployed
- **Alternative**: v29.1.3 (latest, API 1.47+) for bleeding-edge deployments

### containerd v2.2.1
- **Latest Stable**: Most recent stable release
- **Compatibility**: Works seamlessly with Docker Moby 27.x and 28.x
- **Updates**: Includes runc v1.3.4 binary (though we package v1.4.0 separately)
- **Features**: Full support for modern container runtimes and OCI spec v1.3

### runc v1.4.0
- **OCI Spec**: Supports runtime-spec v1.3
- **Security**: Multiple security fixes over v1.3.x series
- **Stability**: First stable release in 1.4.z series
- **Compatibility**: Tested with containerd 2.x and Docker Moby 27.x/28.x

### buildx v0.20.1
- **Latest Patch**: Latest stable release in v0.20.x series
- **Compatibility**: Works with Docker Engine 19.03+, including 27.x and 28.x
- **Features**: Modern BuildKit integration, multi-platform builds

### compose v2.31.0
- **Compose Spec**: Latest implementation of Docker Compose specification
- **Compatibility**: Fully compatible with Docker Engine 27.x and 28.x
- **Features**: Modern service orchestration and stack deployment

## Build Specification Examples

### Example Build Spec for moby-engine v27.5.1 (Ubuntu 22.04 / Jammy)

```json
{
  "arch": "amd64",
  "commit": "4c9b3b011ae4c30145a7b344c870bdda01b454e2",
  "repo": "https://github.com/moby/moby.git",
  "package": "moby-engine",
  "distro": "jammy",
  "tag": "27.5.1",
  "revision": "1"
}
```

### Example Build Spec for moby-containerd v2.2.1 (Ubuntu 22.04 / Jammy)

```json
{
  "arch": "amd64",
  "commit": "b4360bae32f7d0571150dab8258809695f684834",
  "repo": "https://github.com/containerd/containerd.git",
  "package": "moby-containerd",
  "distro": "jammy",
  "tag": "2.2.1",
  "revision": "1"
}
```

### Example Build Spec for moby-runc v1.4.0 (Ubuntu 22.04 / Jammy)

```json
{
  "arch": "amd64",
  "commit": "5863f3dadfc1cf877e87c9010acd54e2310cf9d6",
  "repo": "https://github.com/opencontainers/runc.git",
  "package": "moby-runc",
  "distro": "jammy",
  "tag": "1.4.0",
  "revision": "1"
}
```

### Example Build Spec for moby-buildx v0.20.1 (Ubuntu 22.04 / Jammy)

```json
{
  "arch": "amd64",
  "commit": "245093b99ab74aa2c729a496759afca0704d6470",
  "repo": "https://github.com/docker/buildx.git",
  "package": "moby-buildx",
  "distro": "jammy",
  "tag": "0.20.1",
  "revision": "1"
}
```

### Example Build Spec for moby-compose v2.31.0 (Ubuntu 22.04 / Jammy)

```json
{
  "arch": "amd64",
  "commit": "a8469db83f514a5abe4681c7fee773061f1941c6",
  "repo": "https://github.com/docker/compose.git",
  "package": "moby-compose",
  "distro": "jammy",
  "tag": "2.31.0",
  "revision": "1"
}
```

## Supported Distributions

The following distributions are supported by the packaging system:

| Distribution | Codename | Version | Package Type |
|-------------|----------|---------|--------------|
| Ubuntu 18.04 | bionic | 18.04 | deb |
| Ubuntu 20.04 | focal | 20.04 | deb |
| Ubuntu 22.04 | jammy | 22.04 | deb |
| Ubuntu 24.04 | noble | 24.04 | deb |
| Debian 10 | buster | 10 | deb |
| Debian 11 | bullseye | 11 | deb |
| Debian 12 | bookworm | 12 | deb |
| RHEL 8 | rhel8 | el8 | rpm |
| RHEL 9 | rhel9 | el9 | rpm |
| Azure Linux 2 | mariner2 | cm2 | rpm |
| Windows | windows | - | zip |

## Supported Architectures

- `amd64` (x86_64)
- `arm64` (aarch64)
- `arm/v7` (armhf)

## Building Packages

To build a package with the recommended versions:

1. Create a build spec JSON file with the appropriate values from the tables above
2. Run the packaging tool:
   ```bash
   go run . --build-spec=./your-build-spec.json
   ```

## Testing Recommendations

After building packages with these versions:

1. **Basic Functionality Test**
   - Install packages on target distribution
   - Start Docker daemon
   - Run `docker version` to verify API version
   - Run basic container operations (pull, run, stop, rm)

2. **GitHub Codespaces Compatibility Test**
   - Test with devcontainers/features/docker-in-docker:2 with `moby: true`
   - Verify no API version mismatch errors
   - Test basic Docker operations within the devcontainer

3. **Component Integration Test**
   - Verify containerd is running properly
   - Test runc execution
   - Test buildx multi-platform builds
   - Test compose stack deployment

## Maintenance Schedule

These versions should be reviewed and updated:
- **Security Updates**: Immediately when CVEs are announced
- **Minor Updates**: Monthly check for new patch releases
- **Major Updates**: Quarterly review of new major/minor versions

## References

- [Moby Releases](https://github.com/moby/moby/releases)
- [containerd Releases](https://github.com/containerd/containerd/releases)
- [runc Releases](https://github.com/opencontainers/runc/releases)
- [Docker Buildx Releases](https://github.com/docker/buildx/releases)
- [Docker Compose Releases](https://github.com/docker/compose/releases)
- [Docker Engine API Version History](https://docs.docker.com/engine/api/version-history/)
- [GitHub Codespaces Docker-in-Docker Feature](https://github.com/devcontainers/features/tree/main/src/docker-in-docker)

## Change Log

### January 2025
- Updated minimum versions to support Docker API 1.44+
- moby-engine: 20.10.0 → 27.5.1 (API 1.43 → 1.46)
- moby-containerd: 1.4.0 → 2.2.1
- moby-runc: 1.0.0 → 1.4.0
- moby-buildx: 0.9.0 → 0.20.1
- moby-compose: 2.0.0 → 2.31.0
- Addresses GitHub Codespaces Docker API version mismatch issue
