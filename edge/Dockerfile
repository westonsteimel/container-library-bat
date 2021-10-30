ARG VERSION="master"
ARG REVISION="d5f737f402e9298ca49b1451e0e7868565666bfc"

FROM ghcr.io/westonsteimel/rust-static-builder:stable as builder

ARG VERSION
ARG REVISION

RUN git clone --branch "${VERSION}" https://github.com/sharkdp/bat /build/bat \
    && cd /build/bat \
    && git reset --hard "${REVISION}" \
    && NOCOMPRESS=1 /home/builder/build.sh 

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

