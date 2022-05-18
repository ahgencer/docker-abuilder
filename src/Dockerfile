## Alpine Linux-based Docker builder image
# https://github.com/ahgencer/docker-builder/

## Build stage 1: Prepare build environment
FROM alpine:3.15 AS staging

# Upgrade installed packages
RUN apk -U upgrade

# Install build dependencies
RUN apk add \
        alpine-sdk \
        build-base \
        git

# Copy all files into image
COPY . /

# Configure `buildpkg` and `fetchpkg´
RUN chmod 0755 \
      /usr/local/bin/buildpkg \
      /usr/local/bin/fetchpkg

# Create and configure `builder` user
RUN addgroup builder && \
    adduser -D -G builder -G abuild builder && \
    su -c "abuild-keygen -an" builder && \
    cp /home/builder/.abuild/builder-*.rsa.pub /etc/apk/keys

## Build stage 2: Squash generated image layers
FROM scratch
COPY --from=staging / /

# If the builder image is run on its own, simply drop into a shell
CMD [ "/bin/sh" ]
