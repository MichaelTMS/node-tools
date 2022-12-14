#Download base image Debian 11 and Golang 1.19 
FROM golang:1.19-bullseye AS builder

ARG IMAGE_TAG
ARG GIT_REPOSITORY

# Set Working Directory is /app
WORKDIR /app

# Node project build
RUN apt update && apt install clang pkg-config libssl-dev build-essential ncdu -y
RUN git clone --depth 1 --branch $IMAGE_TAG $GIT_REPOSITORY . && make build

# Node Image create
FROM debian:bullseye-slim

ARG IMAGE_TAG
ARG BUILD_DATE
LABEL image-tag=$IMAGE_TAG
LABEL build-date=$BUILD_DATE

#Required variables set by default
ENV KEY="haqq_impulse"
ENV CHAINID="haqq_54211-3"
ENV MONIKER="HaqqImpulse"
ENV KEYRING="test"
ENV KEYALGO="eth_secp256k1"
ENV LOGLEVEL="info"
ENV CONFIG_PATH="/root/.haqqd"

RUN apt-get update && apt-get upgrade -y \
    && apt-get install -y tzdata ca-certificates curl wget jq \
    && apt-get -y purge && apt-get -y clean \
    && apt-get -y autoremove && rm -rf /var/lib/apt/lists/*

COPY --from=builder /app/build/haqqd /usr/bin/haqqd
COPY ./run_node.sh . 

RUN chmod +x ./run_node.sh

EXPOSE 8545 8546 9090 9091 26656 26657

ENTRYPOINT [ "/bin/bash", "/run_node.sh" ]
