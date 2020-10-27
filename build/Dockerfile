FROM debian:buster-slim as binary

ARG TARGETARCH
ARG UPSTREAM_VERSION
ENV DOWNLOAD_URL https://github.com/prysmaticlabs/prysm/releases/download

RUN DEBIAN_FRONTEND=noninteractive \
    apt update && apt install --assume-yes --no-install-recommends wget ca-certificates
RUN wget -q $DOWNLOAD_URL/$UPSTREAM_VERSION/validator-$UPSTREAM_VERSION-linux-${TARGETARCH:-amd64} -O /usr/local/bin/validator && \
    chmod +x  /usr/local/bin/validator

FROM debian:buster-slim

#Copy binaries from build stage
COPY --from=binary /usr/local/bin/validator /usr/local/bin/validator

RUN apt update && \
    apt install -y ca-certificates file && \
    rm -rf /var/lib/apt/lists/*

ADD ssl /ssl/

# Verify binary is in correct arch
RUN /usr/local/bin/validator --version

RUN validator --version

ENTRYPOINT [ "sh", "-c", "exec validator \
    --medalla \
    --datadir=/data \
    --log-file=/data/validator.log \
    --rpc-host 0.0.0.0 \
    --grpc-gateway-host 0.0.0.0 \
    --monitoring-host 0.0.0.0 \
    --beacon-rpc-provider=$BEACON_RPC_PROVIDER \
    --wallet-dir=$AAAAAAAAA \
    --graffiti=$GRAFFITI \
    --web \
    --accept-terms-of-use \
    $EXTRA_OPTS" \
]
