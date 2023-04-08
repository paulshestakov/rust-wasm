# syntax=docker/dockerfile:1

FROM --platform=$BUILDPLATFORM rust:1.68.2 AS base
WORKDIR /src
RUN apt-get update && \
 apt-get install -y git clang && \
 rustup target add wasm32-wasi

# This line installs WasmEdge including the AOT compiler
RUN curl -sSf https://raw.githubusercontent.com/WasmEdge/WasmEdge/master/utils/install.sh | bash

FROM base AS build
COPY Cargo.toml .
COPY src ./src 
# Build the Wasm binary
RUN --mount=type=cache,target=/usr/local/cargo/git/db \
    --mount=type=cache,target=/usr/local/cargo/registry/cache \
    --mount=type=cache,target=/usr/local/cargo/registry/index \
    cargo build --target wasm32-wasi --release
# This line builds the AOT Wasm binary
RUN /root/.wasmedge/bin/wasmedgec target/wasm32-wasi/release/order_demo_service.wasm order_demo_service.wasm

FROM scratch
ENTRYPOINT [ "order_demo_service.wasm" ]
COPY --from=build /src/order_demo_service.wasm /order_demo_service.wasm