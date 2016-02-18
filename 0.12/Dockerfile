FROM debian:latest

MAINTAINER Rui Marinho <rui.marinho@seegno.com>

RUN apt-get update -y \
  && apt-get install -y curl \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ENV BITCOIND_VERSION 0.12.0
ENV BITCOIND_TEST_VERSION rc5

RUN curl -SL https://bitcoin.org/laanwj-releases.asc | gpg --import \
  && curl -SLO "https://bitcoin.org/bin/bitcoin-core-${BITCOIND_VERSION}/test/SHA256SUMS.asc" \
  && curl -SLO "https://bitcoin.org/bin/bitcoin-core-${BITCOIND_VERSION}/test/bitcoin-${BITCOIND_VERSION}${BITCOIND_TEST_VERSION}-linux64.tar.gz" \
  && gpg --verify SHA256SUMS.asc \
  && grep " bitcoin-${BITCOIND_VERSION}${BITCOIND_TEST_VERSION}-linux64.tar.gz\$" SHA256SUMS.asc | sha256sum -c - \
  && tar -xzf "bitcoin-${BITCOIND_VERSION}${BITCOIND_TEST_VERSION}-linux64.tar.gz" -C /opt \
  && rm "bitcoin-${BITCOIND_VERSION}${BITCOIND_TEST_VERSION}-linux64.tar.gz" SHA256SUMS.asc

ENV PATH /opt/bitcoin-${BITCOIND_VERSION}/bin:$PATH

VOLUME ["/var/lib/bitcoind"]

EXPOSE 8332 8333 18332 18333 18444

ENTRYPOINT ["bitcoind"]

CMD ["--version"]
