ARG ALPINE_VERSION=3.15
FROM alpine:$ALPINE_VERSION AS builder

ARG AUTH_VERSION=4.6.0

ARG MAKEFLAGS=
ENV MAKEFLAGS ${MAKEFLAGS:--j2}

RUN set -ex; \
	apk --update --no-cache upgrade; \
	apk add  --no-cache ca-certificates curl; \
	apk add  --no-cache --virtual .build-depends \
		file gnupg g++ make \
		boost-dev openssl-dev libsodium-dev lmdb-dev luajit-dev net-snmp-dev protobuf-dev \
		curl-dev mariadb-dev postgresql-dev sqlite-dev geoip-dev yaml-cpp-dev \
		ldb-dev openldap-dev krb5-dev; \
	mkdir -v -m 0700 -p /root/.gnupg; \
	curl -RL -O "https://www.powerdns.com/powerdns-keyblock.asc"; \
	gpg2 --no-options --verbose --keyid-format 0xlong --keyserver-options auto-key-retrieve=true --import *.asc; \
	curl -RL -O "https://downloads.powerdns.com/releases/pdns-${AUTH_VERSION}.tar.bz2{.asc,.sig,}"; \
	gpg2 --no-options --verbose --keyid-format 0xlong --keyserver-options auto-key-retrieve=true --verify *.sig; \
	rm -rf /root/.gnupg *.asc *.sig; \
	tar -xpf "pdns-${AUTH_VERSION}.tar.bz2"; \
	rm -f "pdns-${AUTH_VERSION}.tar.bz2"; \
	cd "pdns-${AUTH_VERSION}"; \
	./configure --sysconfdir=/etc/powerdns --mandir=/usr/share/man \
				--with-libsodium --with-sqlite3 --enable-tools --with-lua=luajit --with-protobuf \
				--enable-lua-records --enable-ixfrdist \
				--with-dynmodules="bind gsqlite3 geoip gmysql gpgsql lmdb lua2 pipe ldap remote"; \
	make $MAKEFLAGS; \
	make install-strip; \
	apk del --purge .build-depends;

FROM alpine:$ALPINE_VERSION
LABEL maintainer="Scott Howard"

RUN set -ex; \
	apk --update --no-cache upgrade; \
	apk add --no-cache ca-certificates curl less mandoc tini libcap sqlite \
		boost-program_options \
		openssl libsodium lmdb luajit net-snmp protobuf mariadb-connector-c libpq sqlite-libs geoip yaml-cpp \
		libldap krb5;

ENV PAGER less

RUN set -ex; \
	addgroup -g 953 -S pdns; \
	adduser -S -D -H -u 953 -G pdns pdns;

COPY --from=builder /usr/local/bin /usr/local/bin/
COPY --from=builder /usr/local/sbin /usr/local/sbin/
COPY --from=builder /usr/local/lib/pdns /usr/local/lib/pdns
COPY --from=builder /usr/share/man/man1 /usr/share/man/man1/
COPY --from=builder /usr/local/share/doc/pdns /usr/local/share/doc/pdns
COPY --from=builder /etc/powerdns /etc/powerdns/

RUN set -ex; \
	mkdir -p /var/run/pdns /var/lib/powerdns /etc/powerdns/pdns.d; \
	sqlite3 /var/lib/powerdns/powerdns.sqlite3 < /usr/local/share/doc/pdns/schema.sqlite3.sql; \
	printf "include-dir=/etc/powerdns/pdns.d\nsetgid=pdns\nsetuid=pdns\n" > /etc/powerdns/pdns.conf; \
	cp /etc/powerdns/pdns.conf-dist /etc/powerdns/pdns.d/auth.conf; \
	chown -R pdns:pdns /var/run/pdns /var/lib/powerdns /etc/powerdns/pdns.d; \
	install -v -d -m 00770 -o pdns -g pdns /var/run/pdns && ls -l /var/run/; \
	setcap "cap_net_bind_service=+eip" /usr/local/sbin/pdns_server; \
	/usr/local/sbin/pdns_server --version || [ $? -eq 99 ];

USER pdns

# DNS ports
EXPOSE 53/udp
EXPOSE 53/tcp

# Webserver/API port
EXPOSE 8081/tcp

VOLUME ["/var/lib/powerdns"]

ENTRYPOINT ["/sbin/tini", "--", "/usr/local/sbin/pdns_server"]
CMD ["--disable-syslog"]
