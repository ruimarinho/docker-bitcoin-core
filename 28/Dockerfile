FROM debian:trixie-slim

ARG UID=101
ARG GID=101

LABEL maintainer.0="João Fonseca (@joaopaulofonseca)" \
  maintainer.1="Pedro Branco (@pedrobranco)" \
  maintainer.2="Rui Marinho (@ruimarinho)"

RUN groupadd --gid ${GID} bitcoin \
  && useradd --create-home --no-log-init -u ${UID} -g ${GID} bitcoin \
  && apt-get update -y \
  && apt-get install -y curl gnupg gosu \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ARG TARGETPLATFORM
ENV BITCOIN_VERSION=28.0
ENV BITCOIN_DATA=/home/bitcoin/.bitcoin
ENV PATH=/opt/bitcoin-${BITCOIN_VERSION}/bin:$PATH

RUN set -ex \
  && if [ "${TARGETPLATFORM}" = "linux/amd64" ]; then export TARGETPLATFORM=x86_64-linux-gnu; fi \
  && if [ "${TARGETPLATFORM}" = "linux/arm64" ]; then export TARGETPLATFORM=aarch64-linux-gnu; fi \
  && if [ "${TARGETPLATFORM}" = "linux/arm/v7" ]; then export TARGETPLATFORM=arm-linux-gnueabihf; fi \
  && curl -fsSL https://github.com/bitcoin-core/guix.sigs/archive/refs/heads/main.tar.gz \
  | tar -xz --strip-components=1 guix.sigs-main/builder-keys/ \
  && gpg --import builder-keys/*.gpg \
  && rm -rf builder-keys \
  && curl -SLO https://bitcoincore.org/bin/bitcoin-core-${BITCOIN_VERSION}/bitcoin-${BITCOIN_VERSION}-${TARGETPLATFORM}.tar.gz \
  && curl -SLO https://bitcoincore.org/bin/bitcoin-core-${BITCOIN_VERSION}/SHA256SUMS \
  && curl -SLO https://bitcoincore.org/bin/bitcoin-core-${BITCOIN_VERSION}/SHA256SUMS.asc \
  && { gpg --batch --status-fd 1 --verify SHA256SUMS.asc SHA256SUMS > SHA256SUMS.status || true; } \
  && if grep -q '^\[GNUPG:\] BADSIG ' SHA256SUMS.status; then echo 'SHA256SUMS has a bad signature' >&2; exit 1; fi \
  && GOOD_SIGNATURES=$(grep -cE '^\[GNUPG:\] (GOODSIG|EXPKEYSIG) ' SHA256SUMS.status || true) \
  && echo "SHA256SUMS has ${GOOD_SIGNATURES} good signatures, at least 5 required" \
  && [ "${GOOD_SIGNATURES}" -ge 5 ] \
  && grep " bitcoin-${BITCOIN_VERSION}-${TARGETPLATFORM}.tar.gz" SHA256SUMS | sha256sum -c - \
  && tar -xzf *.tar.gz -C /opt \
  && rm *.tar.gz *.asc SHA256SUMS.status \
  && rm -rf /opt/bitcoin-${BITCOIN_VERSION}/bin/bitcoin-qt

COPY --chmod=755 docker-entrypoint.sh /entrypoint.sh

VOLUME ["/home/bitcoin/.bitcoin"]

EXPOSE 8332 8333 18332 18333 18443 18444 38333 38332

ENTRYPOINT ["/entrypoint.sh"]

RUN bitcoind -version | grep "Bitcoin Core version v${BITCOIN_VERSION}"

CMD ["bitcoind"]
