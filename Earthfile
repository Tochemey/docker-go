VERSION 0.8

all:
    BUILD \
        --platform=linux/amd64 \
        --platform=linux/arm64 \
        --platform=linux/386 \
        +golang-base

golang-base:
    FROM golang:1.24.3-alpine

    WORKDIR /app
    ARG VERSION=dev

    # install gcc dependencies into alpine for CGO
    RUN apk --no-cache add git ca-certificates gcc musl-dev libc-dev binutils-gold curl openssh

    # install docker tools
    # https://docs.docker.com/engine/install/debian/
    RUN apk add --update --no-cache docker

    # install the go generator plugins
    RUN go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
    RUN go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
    RUN export PATH="$PATH:$(go env GOPATH)/bin"

    # install vektra/mockery
    RUN go install github.com/vektra/mockery/v2@v2.53.2

    # install buf from source
    RUN GO111MODULE=on GOBIN=/usr/local/bin go install github.com/bufbuild/buf/cmd/buf@v1.54.0

    # install oapi to generate swagger
    RUN go install github.com/oapi-codegen/oapi-codegen/v2/cmd/oapi-codegen@latest

    # install the various tools to generate connect-go
    RUN go install github.com/fullstorydev/grpcurl/cmd/grpcurl@latest
    RUN go install connectrpc.com/connect/cmd/protoc-gen-connect-go@latest

    # install linter
    # binary will be $(go env GOPATH)/bin/golangci-lint
    RUN curl -sSfL https://raw.githubusercontent.com/golangci/golangci-lint/master/install.sh | sh -s -- -b $(go env GOPATH)/bin v2.1.6
    RUN ls -la $(which golangci-lint)

    SAVE IMAGE --push tochemey/docker-go:1.24.0-${VERSION}
