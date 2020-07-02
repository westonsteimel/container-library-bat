ARG GIT_TAG="v0.15.4"
ARG BAT_VERSION="${GIT_TAG}"

FROM westonsteimel/rust-static-builder:stable as builder

ARG GIT_TAG

RUN git clone --depth 1 --branch "${GIT_TAG}" https://github.com/sharkdp/bat /build/bat \
    && cd /build/bat \
    && NOCOMPRESS=1 /home/builder/build.sh

FROM alpine:latest as config

RUN addgroup bat \
    && adduser -G bat -s /bin/sh -D bat

FROM scratch

ARG BAT_VERSION

COPY --from=config /etc/passwd /etc/passwd
COPY --from=builder /build/bat/target/x86_64-unknown-linux-musl/release/bat /usr/local/bin/bat

USER bat

ENV TERM xterm-256color

ENTRYPOINT ["/usr/local/bin/bat"]
CMD ["--help"]

LABEL org.opencontainers.image.title="bat" \
    org.opencontainers.image.description="bat in Docker" \
    org.opencontainers.image.version="${BAT_VERSION}" \
    version="${BAT_VERSION}"

