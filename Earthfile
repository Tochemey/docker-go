VERSION 0.7

all:
    BUILD \
        --platform=linux/amd64 \
        --platform=linux/arm64 \
        --platform=linux/386 \
        +golang-base

golang-base:
    FROM golang:1.20.6-alpine

    WORKDIR /app
    ARG VERSION=dev

    # install gcc dependencies into alpine for CGO
    RUN apk add gcc musl-dev curl git openssh

    # install docker tools
    # https://docs.docker.com/engine/install/debian/
    RUN apk add --update --no-cache docker

    # install the go generator plugins
    RUN go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
    RUN go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
    RUN export PATH="$PATH:$(go env GOPATH)/bin"

    # install vektra/mockery
    # We need to pin this version because of this issue https://github.com/vektra/mockery/issues/442
    RUN go install github.com/vektra/mockery/v2@v2.26.1

    # install buf from source
    RUN GO111MODULE=on GOBIN=/usr/local/bin go install github.com/bufbuild/buf/cmd/buf@v1.24.0

    # install linter
    # binary will be $(go env GOPATH)/bin/golangci-lint
    RUN curl -sSfL https://raw.githubusercontent.com/golangci/golangci-lint/master/install.sh | sh -s -- -b $(go env GOPATH)/bin v1.53.3
    RUN ls -la $(which golangci-lint)

    SAVE IMAGE --push quickflows-io/docker-go:1.20.6-${VERSION}
