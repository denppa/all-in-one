# syntax=docker/dockerfile:latest
FROM golang:1.23.3-alpine3.20 AS go

ENV IMAGINARY_HASH=8f36a26c448be8c151a3878404b75fcd1cd3cf0c

RUN set -ex; \
    apk add --no-cache \
        vips-dev \
        vips-magick \
        vips-heif \
        vips-jxl \
        vips-poppler \
        build-base; \
    go install github.com/h2non/imaginary@"$IMAGINARY_HASH";

FROM alpine:3.20.3
RUN set -ex; \
    apk upgrade --no-cache -a; \
    apk add --no-cache \
        tzdata \
        ca-certificates \
        netcat-openbsd \
        vips \
        vips-magick \
        vips-heif \
        vips-jxl \
        vips-poppler \
        ttf-dejavu \
        bash

COPY --from=go /go/bin/imaginary /usr/local/bin/imaginary
COPY --chmod=775 start.sh /start.sh

ENV PORT=9000

USER 65534

# https://github.com/h2non/imaginary#memory-issues
ENV MALLOC_ARENA_MAX=2
ENTRYPOINT ["/start.sh"]

HEALTHCHECK CMD nc -z 127.0.0.1 "$PORT" || exit 1
LABEL com.centurylinklabs.watchtower.enable="false"
