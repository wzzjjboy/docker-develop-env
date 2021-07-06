FROM golang:alpine AS builder

LABEL stage=gobuilder

ENV CGO_ENABLED 0
ENV GOOS linux
ENV GOPROXY https://goproxy.cn,direct

WORKDIR /build/zero


RUN export GOPATH=`go env GOPATH`
RUN go get -v github.com/tal-tech/go-stash@v0.0.5
RUN cd $GOPATH/pkg/mod/github.com/tal-tech/go-stash@v0.0.5/ && go mod download
RUN cd $GOPATH/pkg/mod/github.com/tal-tech/go-stash@v0.0.5/stash && go build -ldflags="-s -w" -o /app/stash stash.go
COPY ./config.yaml /app

FROM alpine

RUN apk update --no-cache && apk add --no-cache ca-certificates tzdata
ENV TZ Asia/Shanghai

WORKDIR /app
COPY --from=builder /app /app


CMD ["/app/stash", "-f", "/app/config.yaml"]
