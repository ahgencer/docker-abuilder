## Alpine Linux-based Docker builder image
# https://github.com/ahgencer/docker-builder/

## Build stage 1: Prepare build environment
FROM alpine:3.15 AS staging

## Build stage 2: Squash generated image layers
FROM scratch
COPY --from=staging / /

# If the builder image is run on its own, simply drop into a shell
CMD [ "/bin/sh" ]
