FROM debian:stable-slim

RUN apt-get update && apt-get install -y \
    build-essential \
    gcc-arm-none-eabi \
    cmake \
    python3 \
    python3-pip \
    git \
    ninja-build \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*