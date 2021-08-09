ARG VERSION="0.18.2"
ARG REVISION="589df6792fe406f91b00db5e24d35393c6527e6b"

FROM westonsteimel/rust-static-builder:stable as builder

ARG VERSION
ARG REVISION

RUN git clone --branch "v${VERSION}" https://github.com/sharkdp/bat /build/bat \
    && cd /build/bat \
    && git reset --hard "${REVISION}" \
    && mkdir /build/output \
    && NOCOMPRESS=1 /home/builder/build.sh \
    && cp /build/bat/target/${TARGET}/release/bat /build/output/

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
COPY --from=builder /build/output/bat /usr/local/bin/bat

USER bat

ENTRYPOINT ["/usr/local/bin/bat"]
CMD ["--help"]

LABEL org.opencontainers.image.title="bat" \
    org.opencontainers.image.description="bat in Docker" \
    org.opencontainers.image.revision="${REVISION}" \
    org.opencontainers.image.version="${VERSION}"

