FROM ubuntu:18.04 as build

RUN apt-get update && apt-get install -y --no-install-recommends \
  ca-certificates \
  g++ \
  gcc \
  git \
  libc6-dev \
  make \
  wget \
  librocksdb-dev \
  && rm -rf /var/lib/apt/lists/*

RUN set -eux; \
  url="https://dl.google.com/go/go1.12.6.linux-amd64.tar.gz"; \
  wget -O go.tgz "$url"; \
  tar -C /usr/local -xzf go.tgz; \
  rm go.tgz; \
  export PATH="/usr/local/go/bin:$PATH"; \
  go version

ENV GOPATH /go
ENV PATH $GOPATH/bin:/usr/local/go/bin:$PATH
RUN go get -u google.golang.org/grpc

ENV GO111MODULE=on

COPY Makefile go.mod go.sum /go/src/github.com/atomix/dragonboat-raft-replica/
COPY vendor/ /go/src/github.com/atomix/dragonboat-raft-replica/vendor/
COPY pkg/ /go/src/github.com/atomix/dragonboat-raft-replica/pkg/
COPY cmd/ /go/src/github.com/atomix/dragonboat-raft-replica/cmd/

RUN cd /go/src/github.com/atomix/dragonboat-raft-replica && make

FROM ubuntu:18.04

RUN apt-get update && apt-get install -y --no-install-recommends \
  librocksdb-dev \
  && rm -rf /var/lib/apt/lists/*

COPY --from=build /go/src/github.com/atomix/dragonboat-raft-replica/build/_output/dragonboat-raft-replica /usr/local/bin/dragonboat-raft-replica

ENTRYPOINT ["dragonboat-raft-replica"]
