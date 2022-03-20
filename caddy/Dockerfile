ARG CADDY_VERSION=2.4.6
FROM caddy:$CADDY_VERSION-builder-alpine AS builder

RUN xcaddy build \
	--with github.com/hairyhenderson/caddy-teapot-module@9a6dc8897bd91ee8002ea2e0b0f1ab8c2d7dee88 \
	--with github.com/gamalan/caddy-tlsredis@1674b9232faffbe3314175a85cc711027117f395 \
	--with github.com/caddy-dns/lego-deprecated

FROM caddy:$CADDY_VERSION-alpine

COPY --from=builder /usr/bin/caddy /usr/bin/caddy
