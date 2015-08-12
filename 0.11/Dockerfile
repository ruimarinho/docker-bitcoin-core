FROM debian:latest

MAINTAINER Rui Marinho <rui.marinho@seegno.com>

RUN apt-get update -y \
  && apt-get install -y curl \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys \
  0xBE38D3A8 \
  0x2346C9A6 \
  0x36C2E964 \
  0x1DAAC974 \
  0x7ADCA079 \
  0xB0413BFA \
  0xDAB591E7 \
  0x21F4889F \
  0x98832223

ENV BITCOIND_VERSION 0.11.0

RUN curl -SLO "https://bitcoin.org/bin/bitcoin-core-${BITCOIND_VERSION}/bitcoin-${BITCOIND_VERSION}-linux64.tar.gz" \
  && curl -SLO "https://bitcoin.org/bin/bitcoin-core-${BITCOIND_VERSION}/SHA256SUMS.asc" \
  && gpg --verify SHA256SUMS.asc \
  && grep " bitcoin-${BITCOIND_VERSION}-linux64.tar.gz\$" SHA256SUMS.asc | sha256sum -c - \
  && tar -xzf "bitcoin-${BITCOIND_VERSION}-linux64.tar.gz" -C /opt \
  && rm "bitcoin-${BITCOIND_VERSION}-linux64.tar.gz" SHA256SUMS.asc

ENV PATH /opt/bitcoin-${BITCOIND_VERSION}/bin:$PATH

VOLUME ["/var/lib/bitcoind"]

EXPOSE 8332 8333 18332 18333 18444

ENTRYPOINT ["bitcoind"]

CMD ["--version"]
