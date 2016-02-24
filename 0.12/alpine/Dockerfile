FROM alpine:latest

MAINTAINER Rui Marinho <rui.marinho@seegno.com> (@ruimarinho)

RUN set -eof pipefail

RUN addgroup bitcoin && adduser -S -G bitcoin bitcoin

ENV GOSU_VERSION "1.7"
ENV GOSU_SHASUM "34049cfc713e8b74b90d6de49690fa601dc040021980812b2f1f691534be8a50  /usr/local/bin/gosu"

RUN apk --no-cache add openssl \
  && wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/${GOSU_VERSION}/gosu-amd64" \
  && echo "${GOSU_SHASUM}" | sha256sum -c \
  && chmod +x /usr/local/bin/gosu

ENV BERKELEYDB_VERSION "db-4.8.30.NC"
ENV BERKELEYDB_PREFIX "/opt/${BERKELEYDB_VERSION}"
ENV BITCOIN_VERSION "0.12.0"
ENV BITCOIN_PREFIX "/opt/bitcoin-${BITCOIN_VERSION}"
ENV BITCOIN_SHASUM "92b1b6e1f49e74c914ff3dd43e97bb1859bcd3239310b5fe54db326aba1fd0a5  v0.12.0.tar.gz"
ENV BITCOIN_DATA "/home/bitcoin/.bitcoin"
ENV PATH "${BITCOIN_PREFIX}/bin:$PATH"

RUN apk --no-cache --virtual build-dependendencies add autoconf \
      automake \
      boost-dev \
      build-base \
      chrpath \
      libevent-dev \
      libtool \
      linux-headers \
      openssl-dev \
      protobuf-dev \
      zeromq-dev \
  && mkdir -p /tmp/build \
  && wget -O /tmp/build/${BERKELEYDB_VERSION}.tar.gz "http://download.oracle.com/berkeley-db/${BERKELEYDB_VERSION}.tar.gz" \
  && tar -xzf "/tmp/build/${BERKELEYDB_VERSION}.tar.gz" -C /tmp/build/ \
  && sed s/__atomic_compare_exchange/__atomic_compare_exchange_db/g -i "/tmp/build/${BERKELEYDB_VERSION}/dbinc/atomic.h" \
  && mkdir -p "${BERKELEYDB_PREFIX}" \
  && cd "/tmp/build/${BERKELEYDB_VERSION}/build_unix" \
  && ../dist/configure --enable-cxx --disable-shared --with-pic --prefix="${BERKELEYDB_PREFIX}" \
  && make install \
  && wget -O "/tmp/build/v${BITCOIN_VERSION}.tar.gz" "https://github.com/bitcoin/bitcoin/archive/v${BITCOIN_VERSION}.tar.gz" \
  && cd /tmp/build \
  && echo "${BITCOIN_SHASUM}" | sha256sum -c \
  && tar -xzf v${BITCOIN_VERSION}.tar.gz \
  && cd "/tmp/build/bitcoin-${BITCOIN_VERSION}" \
  && ./autogen.sh \
  && ./configure LDFLAGS="-L${BERKELEYDB_PREFIX}/lib/" CPPFLAGS="-I${BERKELEYDB_PREFIX}/include/" \
    --prefix="${BITCOIN_PREFIX}" \
    --mandir=/usr/share/man \
    --disable-tests \
    --disable-bench \
    --disable-ccache \
    --with-gui=no \
    --with-utils \
    --with-libs \
    --with-daemon \
  && make install \
  && cd / \
  && strip "${BITCOIN_PREFIX}/bin/bitcoin-cli" "${BITCOIN_PREFIX}/bin/bitcoind" "${BITCOIN_PREFIX}/bin/bitcoin-tx" "${BITCOIN_PREFIX}/lib/libbitcoinconsensus.a" "${BITCOIN_PREFIX}/lib/libbitcoinconsensus.so.0.0.0" \
  && rm -rf "/tmp/build" "${BERKELEYDB_PREFIX}/docs" \
  && apk --no-cache --purge del build-dependendencies \
  && apk --no-cache add boost \
    boost-program_options \
    libevent \
    libzmq

VOLUME ["/home/bitcoin/.bitcoin"]

COPY docker-entrypoint.sh /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]

EXPOSE 8332 8333 18332 18333 18444

CMD ["bitcoind"]
