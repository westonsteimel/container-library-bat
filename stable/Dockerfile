ARG VERSION="0.22.1"
ARG REVISION="e5d95796141a719e208208182a5d3c2090a009c6"

FROM docker.io/rust:alpine as builder

ARG VERSION
ARG REVISION

RUN apk update && apk add --no-cache \
    git \
    ca-certificates \
    build-base \
    musl-utils \
    musl-dev

RUN git clone --branch "v${VERSION}" https://github.com/sharkdp/bat /build/bat \
    && cd /build/bat \
    && git reset --hard "${REVISION}" \
    && CARGO_TARGET_DIR=/build/artifacts/ RUSTFLAGS='-C link-arg=-s' cargo build --release --bins

FROM alpine:edge as config

RUN addgroup bat \
    && adduser -G bat -s /bin/sh -D bat

FROM scratch

ARG VERSION
ARG REVISION
ENV BAT_VERSION="${VERSION}" \
    BAT_REVISION="${REVISION}" \
    TERM="xterm-256color"


COPY --from=config /etc/passwd /etc/passwd
COPY --from=builder /build/artifacts/bat /usr/local/bin/bat

USER bat

ENTRYPOINT ["/usr/local/bin/bat"]
CMD ["--help"]

LABEL org.opencontainers.image.title="bat" \
    org.opencontainers.image.description="bat in Docker" \
    org.opencontainers.image.revision="${REVISION}" \
    org.opencontainers.image.version="${VERSION}"

