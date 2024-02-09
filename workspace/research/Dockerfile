# Get started with a build env with Rust nightly
FROM rust:alpine3.18 AS builder
WORKDIR /build

RUN apk update && \
    apk upgrade --no-cache && \
    apk add --no-cache bash curl libc-dev binaryen pkgconfig libressl-dev musl-dev 

RUN rustup default nightly 
RUN rustup target add wasm32-unknown-unknown
RUN npm install -g sass
RUN cargo install --locked cargo-leptos
# Add the WASM target
RUN rustup target add wasm32-unknown-unknown

COPY . .
RUN cargo leptos build --release

FROM alpine:3.18 AS runner
WORKDIR /research

ENV USER "utakata"
ENV CHOWNUSER "master:${USER}"

RUN addgroup -S ${USER} && \
    adduser -S master -G ${USER} && \
    chown -R ${CHOWNUSER} research

COPY --chown=${CHOWNUSER} --from=builder /build/target/${USER}/release/research ./${USER}/research 
COPY --chown=${CHOWNUSER} --from=builder /build/target/front/wasm32-unknown-unknown/release/research.wasm ./front/research.wasm
COPY --chown=${CHOWNUSER} --from=builder /build/target/site ./site

USER master

ENV LEPTOS_OUTPUT_NAME "research"
ENV LEPTOS_SITE_ROOT "/research/site"
ENV LEPTOS_ENV "PROD"
ENV LEPTOS_SITE_ADDR "0.0.0.0:3000"

EXPOSE 3000

CMD ["./${USER}/research "]
