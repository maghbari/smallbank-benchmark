# SPDX-License-Identifier: Apache-2.0

ARG GO_VER=1.17.5
ARG ALPINE_VER=3.14

FROM golang:${GO_VER}-alpine${ALPINE_VER} as build

RUN apk add --no-cache \
	bash \
	binutils-gold \
  dumb-init \
	gcc \
	git \
	make \
	musl-dev

ADD . $GOPATH/src/maghbari/smallbank-benchmark/contracts/smallbank
WORKDIR $GOPATH/src/maghbari/smallbank-benchmark/contracts/smallbank

RUN go install ./...

FROM golang:${GO_VER}-alpine${ALPINE_VER}

LABEL org.opencontainers.image.title "Smallbank Go Contract"
LABEL org.opencontainers.image.description "Smallbank Go Contract"
LABEL org.opencontainers.image.source "https://github.com/maghbari/smallbank-benchmark/contracts/smallbank"

COPY --from=build /usr/bin/dumb-init /usr/bin/dumb-init
COPY --from=build /go/bin/smallbank /usr/bin/smallbank

WORKDIR /var/hyperledger/smallbank
ENTRYPOINT ["/usr/bin/dumb-init", "--"]
CMD ["sh", "-c", "exec /usr/bin/smallbank -peer.address=$CORE_PEER_ADDRESS"]
