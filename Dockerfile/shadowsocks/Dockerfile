#
# Dockerfile for shadowsocks-libev
#

FROM alpine:3.9
LABEL maintainer="taylord0ng <hibase123@gmail.com>"
ENV VERSION v3.2.5

RUN set -ex \
 # Build environment setup
 && apk add --no-cache --virtual .build-deps \
      autoconf \
      automake \
      build-base \
      c-ares-dev \
      libev-dev \
      libtool \
      libsodium-dev \
      linux-headers \
      mbedtls-dev \
      git \
      pcre-dev \
 # Build & install
 && git clone --branch ${VERSION} https://github.com/shadowsocks/shadowsocks-libev \
 && cd shadowsocks-libev/ \
 && git submodule update --init --recursive \
 && ./autogen.sh \
 && ./configure --prefix=/usr --disable-documentation \
 && make install \
 && apk del .build-deps \
 # Runtime dependencies setup
 && apk add --no-cache \
      rng-tools \
      $(scanelf --needed --nobanner /usr/bin/ss-* \
      | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
      | sort -u) \
 && cd / \
 && rm -rf /shadowsocks-libev/

COPY config.json /etc/shadowsocks.json

CMD exec ss-server -c /etc/shadowsocks.json -u -v
