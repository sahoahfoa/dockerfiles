ARG ALPINE_VERSION=edge
FROM alpine:$ALPINE_VERSION

RUN apk --no-cache add dnsmasq && echo "conf-dir=/etc/dnsmasq.d" >> /etc/dnsmasq.conf
RUN mkdir /tftp/ && chown dnsmasq:dnsmasq /tftp/
COPY tftp.conf /etc/dnsmasq.d/

USER dnsmasq

EXPOSE 69/udp
VOLUME ["/etc/dnsmasq.d"]
VOLUME ["/tftp"]

ENTRYPOINT ["/usr/sbin/dnsmasq"]
CMD ["--user=dnsmasq", "--group=dnsmasq", "-k", "--log-facility=-"]
