FROM ubuntu:22.04

ARG USERNAME=utakata
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN apt-get update && \
    group add --gid $USER_GID $USERNAME \
    && user add -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# 必要なapkパッケージを導入していく
RUN apk update && \
  apk upgrade --no-cache && \
  apk add --no-cache bash curl npm libc-dev protoc \
  openssl-dev protobuf-dev gcc git g++ make binaryen \
  pkgconfig libressl-dev musl-dev 


# Rust Install 
RUN curl https://sh.rustup.rs -sSf | sh
ENV PATH="/root/.cargo/bin:$PATH"
RUN rustup default nightly

RUN npm install -g sass

# cargo-leptosを入れる
RUN curl --proto '=https' --tlsv1.2 -LsSf https://github.com/leptos-rs/cargo-leptos/releases/download/0.2.5/cargo-leptos-installer.sh | sh

# WASM出力のためのものを入れる
RUN rustup target add wasm32-unknown-unknown

WORKDIR /workspace 
COPY . .
