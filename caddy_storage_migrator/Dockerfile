FROM golang:latest as builder

ENV CGO_ENABLED=0
RUN go get -ldflags="-extldflags=-static" github.com/masipcat/caddy-storage-migrator/cmd/migrator

FROM scratch
LABEL maintainer="Scott Howard"

COPY --from=builder /go/bin/migrator /

ENTRYPOINT ["/migrator"]
