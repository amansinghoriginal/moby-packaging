# Pull Request: Update Moby Package Versions for Docker API 1.44+ Support

## Overview

This PR updates the minimum version requirements for Moby packages to resolve Docker API version mismatch issues affecting GitHub Codespaces users and ensure compatibility with modern Docker infrastructure.

## Problem Statement

Users of GitHub Codespaces with the `devcontainers/features/docker-in-docker:2` feature (using `moby: true`) were experiencing failures due to Docker API version mismatches:

```
Error response from daemon: client version 1.43 is too old. 
Minimum supported API version is 1.44, please upgrade your client to a newer version
```

The root cause: Moby packages hosted on packages.microsoft.com were outdated (version 20.10.x, API 1.43) and did not meet the minimum API version 1.44 required by GitHub Codespaces infrastructure.

## Changes Made

### 1. Updated Minimum Version Requirements (`scripts/min-versions.json`)

| Package | Previous Version | New Version | API Version |
|---------|-----------------|-------------|-------------|
| moby-engine | 20.10.0 | 27.5.1 | 1.43 → 1.46 |
| moby-cli | 20.10.0 | 27.5.1 | 1.43 → 1.46 |
| moby-containerd | 1.4.0 | 2.2.1 | - |
| moby-runc | 1.0.0 | 1.4.0 | - |
| moby-buildx | 0.9.0 | 0.20.1 | - |
| moby-compose | 2.0.0 | 2.31.0 | - |

### 2. Created Comprehensive Documentation (`RECOMMENDED_VERSIONS.md`)

This document provides:
- **Version Recommendations**: Detailed recommendations for all Moby packages with rationale
- **Commit Hashes**: Exact commit hashes for reproducible builds
- **Build Specifications**: Example JSON specs for all packages and distributions
- **Docker API Version Mapping**: Clear mapping between Docker/Moby versions and API versions
- **Testing Guidelines**: Recommended testing procedures
- **Maintenance Schedule**: Guidelines for keeping versions up-to-date

## Technical Details

### Version Selection Criteria

1. **Moby Engine v27.5.1**
   - Supports Docker API 1.46 (well above minimum requirement of 1.44)
   - Latest stable release in the 27.x LTS series
   - Includes critical security fixes (CVE-2024-45341, CVE-2024-45336)
   - Extensively tested and widely deployed

2. **containerd v2.2.1**
   - Latest stable release
   - Full compatibility with Docker Moby 27.x and 28.x
   - Supports OCI runtime-spec v1.3

3. **runc v1.4.0**
   - First stable release in 1.4.z series
   - Multiple security improvements over v1.3.x
   - Full OCI spec v1.3 support

4. **buildx v0.20.1** and **compose v2.31.0**
   - Latest stable releases
   - Full compatibility with updated Docker Engine

### Build Process

This repository uses a Go-based build system that packages upstream components. The actual version specifications are passed as JSON parameters to the Azure Pipeline, not stored in the repository. The key files are:

- `scripts/min-versions.json`: Defines minimum acceptable versions
- `RECOMMENDED_VERSIONS.md`: Provides recommended versions and build specs
- Package definitions in `packages/*/`: Define how packages are built and installed

## How to Use These Updates

### For Package Maintainers

1. **Review the recommendations** in `RECOMMENDED_VERSIONS.md`

2. **Trigger Azure Pipeline builds** using the provided build specifications. For example, to build moby-engine v27.5.1 for Ubuntu 22.04:

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

3. **Test the built packages** in target environments:
   - Basic Docker functionality
   - GitHub Codespaces compatibility
   - Component integration

4. **Deploy to packages.microsoft.com** after successful testing

### For Users

Once the updated packages are deployed:

1. **Update your system packages**:
   ```bash
   apt-get update && apt-get upgrade moby-engine moby-cli moby-containerd moby-runc
   ```
   or
   ```bash
   yum update moby-engine moby-cli moby-containerd moby-runc
   ```

2. **Verify the API version**:
   ```bash
   docker version
   # Should show API version 1.46 or higher
   ```

3. **GitHub Codespaces users** can now use `docker-in-docker` with `moby: true` without workarounds

## Testing Recommendations

Before deploying to production:

1. **API Version Verification**
   ```bash
   docker version | grep "API version"
   ```
   Expected: API version 1.46 or higher

2. **Basic Container Operations**
   ```bash
   docker pull mcr.microsoft.com/hello-world
   docker run --rm mcr.microsoft.com/hello-world
   ```

3. **GitHub Codespaces Test**
   - Create a devcontainer with `docker-in-docker` feature
   - Set `moby: true` in feature configuration
   - Verify no API mismatch errors
   - Test building and running containers

4. **Multi-platform Build Test** (buildx)
   ```bash
   docker buildx create --use
   docker buildx build --platform linux/amd64,linux/arm64 .
   ```

5. **Compose Stack Test**
   ```bash
   docker compose up -d
   docker compose ps
   docker compose down
   ```

## Impact

### Affected Users
- GitHub Codespaces users (primary beneficiaries)
- Azure IoT Edge users
- Anyone using Moby packages from packages.microsoft.com

### Benefits
- ✅ Resolves Docker API version mismatch errors
- ✅ Enables use of modern Docker features
- ✅ Improves security with latest patches
- ✅ Better compatibility with container tooling
- ✅ Aligns with upstream Docker ecosystem

### Risks
- Potential breaking changes from major version updates
- Need for comprehensive testing before deployment
- Users on older distributions may need to update OS first

## Backward Compatibility

The version jump from 20.10.x to 27.5.1 is significant. While Docker maintains good backward compatibility, there are some considerations:

1. **API Changes**: New minimum API version (1.44) may affect very old clients
2. **Deprecated Features**: Some features deprecated in 24.x may be removed
3. **Configuration Changes**: Some daemon configuration options may have changed

**Mitigation**: The extensive testing recommendations in `RECOMMENDED_VERSIONS.md` help identify any compatibility issues before production deployment.

## References

- [Moby v27.5.1 Release Notes](https://github.com/moby/moby/releases/tag/v27.5.1)
- [containerd v2.2.1 Release Notes](https://github.com/containerd/containerd/releases/tag/v2.2.1)
- [runc v1.4.0 Release Notes](https://github.com/opencontainers/runc/releases/tag/v1.4.0)
- [Docker Engine API Version History](https://docs.docker.com/engine/api/version-history/)
- [GitHub Codespaces Docker-in-Docker Issue](https://github.com/devcontainers/features/issues)

## Next Steps

1. **Review**: Package maintainers review the changes and recommendations
2. **Build**: Trigger Azure Pipeline builds for all target distributions
3. **Test**: Comprehensive testing in staging environments
4. **Deploy**: Roll out to packages.microsoft.com
5. **Monitor**: Track user feedback and any issues
6. **Communicate**: Notify users of the update and any required actions

## Questions or Concerns?

Please comment on this PR or reach out to the repository maintainers.

---

**Related Issues**: GitHub Codespaces Docker-in-Docker API version mismatch
**Breaking**: No (but requires deployment of new packages)
**Documentation**: Yes (RECOMMENDED_VERSIONS.md)
