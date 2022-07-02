# Alpine Linux builder base image

<p align="center">
  <img alt="Docker Pulls" src="https://img.shields.io/docker/pulls/ahgencer/abuilder?label=Docker%20Pulls">
  <img alt="GitHub Stars" src="https://img.shields.io/github/stars/ahgencer/docker-abuilder?label=GitHub%20Stars">
  <img alt="Issues" src="https://img.shields.io/github/issues/ahgencer/docker-abuilder/open?label=Issues">
  <img alt="License" src="https://img.shields.io/github/license/ahgencer/docker-abuilder?label=License">
</p>

- GitHub: https://github.com/ahgencer/docker-abuilder
- Docker Hub: https://hub.docker.com/r/ahgencer/abuilder
- Wiki: https://github.com/ahgencer/docker-abuilder/wiki

*ahgencer/abuilder* provides a Docker image that contains common build
dependencies for compiling Alpine Linux packages from source.

Interested? [Here's how to get started.](#getting-started)

## What's inside the image?

### Included Packages

These are the `apk` packages and other software provided by the image.

|  Component   | Description                                                                                                                                                |
|:------------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alpine Linux | The base image. Built around [musl](https://www.etalabs.net/compare_libcs.html) libc and [BusyBox](https://www.busybox.net/) for security and small sizes. |
|  alpine-sdk  | Meta package for the Alpine Linux Software Development Kit.                                                                                                |
|  build-base  | Meta package for building Alpine Linux packages.                                                                                                           |
|     git      | [Version Control System](https://en.wikipedia.org/wiki/Version_control) for cloning software repositories.                                                 |

### Scripts

Useful scripts for automating the build process for Alpine Linux packages.

| Component | Description                                                                                           |
|:---------:|:------------------------------------------------------------------------------------------------------|
| buildpkg  | Iterates all directories in `/opt` and runs the script called `build` for each package.               |
| fetchpkg  | Fetches the APKBUILD package information directly from the official Alpine Linux `aports` repository. |

## Getting started

### Building the image

You can build the image yourself using Docker by simply running:

    docker build -t ahgencer/abuilder:<tag> src/

To build the image instead using Podman / Buildah, run:

    podman build --format=docker -t docker.io/ahgencer/abuilder:<tag> src/

You may also optionally pass in the `ALPINE_VERSION` build argument to specify
the version of the base image to use.

### Using the image

The image is meant to be used as an early build stage in a Dockerfile.

```dockerfile
# Don't forget to lock the builder image to a specific major version!
ARG BUILDER_VERSION=1.0
ARG ALPINE_VERSION=latest

## Build stage 1: Patch and build custom `apk` packages
FROM ahgencer/abuilder:$BUILDER_VERSION AS builder

# Copy and build `apk` packages
COPY ./opt /opt
RUN buildpkg

## Build stage 2: Prepare files for the final image
FROM alpine:$ALPINE_VERSION

# Copy built packages and RSA public keys into image
COPY --from=builder --chown=root:root /home/builder/.abuild/builder-*.rsa.pub /etc/apk/keys/
COPY --from=builder /home/builder/packages/opt/**/* /opt/
```

## License

This program is free software published under the
[MIT License](https://github.com/ahgencer/docker-abuilder/blob/master/LICENSE).
Feel free to use it in your own projects!
