ARG CADDY_VERSION=2.4.3
FROM caddy:$CADDY_VERSION-builder-alpine AS builder

RUN xcaddy build \
	--with github.com/hairyhenderson/caddy-teapot-module@fbc7370ca8cd66af5573a2c250a8cab55bf6a85a \
	--with github.com/sahoahfoa/caddy-tlsredis@649ef5fc7f955dd7233214b19491c82ab8a38bee \
	--with github.com/caddy-dns/lego-deprecated

FROM caddy:$CADDY_VERSION-alpine

COPY --from=builder /usr/bin/caddy /usr/bin/caddy
