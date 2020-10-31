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

ENTRYPOINT [ "sh", "-c", "exec validator \
    --medalla \
    --datadir=/root/.eth2 \
    --log-file=/root/.eth2/validator.log \
    --rpc-host 0.0.0.0 \
    --grpc-gateway-host 0.0.0.0 \
    --monitoring-host 0.0.0.0 \
    --beacon-rpc-provider=$BEACON_RPC_PROVIDER \
    --beacon-rpc-gateway-provider=$BEACON_RPC_GATEWAY_PROVIDER \
    --wallet-dir=/root/.eth2validators \
    --wallet-password-file=/root/.eth2wallets/wallet-password.txt \
    --graffiti=$GRAFFITI \
    --web \
    --grpc-gateway-host=0.0.0.0 \
    --grpc-gateway-port=80 \
    # Comma separated list of domains from which to accept cross origin requests (browser enforced). This flag has no effect if not used with --grpc-gateway-port. (default: "http://localhost:4200")
    --grpc-gateway-corsdomain=http://prysm-medalla-beacon-chain.dappnode:3500 \
    # A comma separated list of key value pairs to pass as gRPC headers for all gRPC calls. Example: --grpc-headers=key=value
    # --grpc-headers value
    --accept-terms-of-use \
    $EXTRA_OPTS" \
]
