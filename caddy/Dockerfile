ARG CADDY_VERSION=2.3.0
FROM caddy:$CADDY_VERSION-builder-alpine AS builder

RUN xcaddy build \
	--with github.com/hairyhenderson/caddy-teapot-module@v0.0.3-0 \
	--with github.com/gamalan/caddy-tlsredis@0.2.7 \
	--with github.com/caddy-dns/lego-deprecated

FROM caddy:$CADDY_VERSION-alpine

COPY --from=builder /usr/bin/caddy /usr/bin/caddy
