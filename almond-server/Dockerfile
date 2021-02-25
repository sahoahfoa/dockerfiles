ARG ALPINE_VERSION=3.12
FROM alpine:$ALPINE_VERSION 
LABEL maintainer="Scott Howard"

ARG ALMOND_VERSION=1.99.0

# Install all deps in the standard repos
RUN set -ex; \
	apk --update --no-cache upgrade; \
	apk add --no-cache alpine-sdk gettext findutils curl zip unzip nodejs; \
    mkdir /opt/almond;

RUN useradd almond && \
    chown almond:almond /opt/almond
USER almond
RUN set -ex; \
    curl -RL "https://github.com/stanford-oval/almond-server/archive/v${ALMOND_VERSION}.tar.gz" | tar -xvz --strip 1 -C /opt/almond
WORKDIR /opt/almond

RUN npm ci && rm -fr /home/almond/.cache && rm -fr /home/almond/.npm
RUN mkdir /home/almond/.cache

EXPOSE 3000
ENTRYPOINT ["npm", "start"]
