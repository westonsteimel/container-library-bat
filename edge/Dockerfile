ARG SOURCE
ARG REVISION
ARG CREATED
ARG VERSION
ARG GIT_REF="${VERSION}"

FROM westonsteimel/rust-static-builder:stable as builder

ARG GIT_REF

RUN git clone --depth 1 --branch "${GIT_REF}" https://github.com/sharkdp/bat /build/bat \
    && cd /build/bat \
    && mkdir /build/output \
    && NOCOMPRESS=1 /home/builder/build.sh \
    && cp /build/bat/target/${TARGET}/release/bat /build/output/

FROM alpine:edge as config

RUN addgroup bat \
    && adduser -G bat -s /bin/sh -D bat

FROM scratch

ARG VERSION
ARG SOURCE
ARG REVISION
ARG CREATED

COPY --from=config /etc/passwd /etc/passwd
COPY --from=builder /build/output/bat /usr/local/bin/bat

USER bat

ENV TERM xterm-256color

ENTRYPOINT ["/usr/local/bin/bat"]
CMD ["--help"]

LABEL org.opencontainers.image.title="bat" \
    org.opencontainers.image.description="bat in Docker" \
    org.opencontainers.image.source="${SOURCE}" \
    org.opencontainers.image.revision="${REVISION}" \
    org.opencontainers.image.created="${CREATED}" \
    org.opencontainers.image.version="${VERSION}"

