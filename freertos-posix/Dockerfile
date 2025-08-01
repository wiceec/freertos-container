ARG BASE_IMAGE="ghcr.io/${REPO_OWNER}/freertos:base-latest"

FROM ${BASE_IMAGE}

RUN apt-get update && apt-get install -y \
    gcc-multilib \
    make \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*