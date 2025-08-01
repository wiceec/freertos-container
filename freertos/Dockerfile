ARG BASE_IMAGE="ghcr.io/${REPO_OWNER}/freertos:base-latest"
ARG TOOLCHAIN_ARCH="arm-none-eabi"

FROM ${BASE_IMAGE}

RUN apt-get update && apt-get install -y \
    ${TOOLCHAIN_ARCH} \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*