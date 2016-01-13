# seegno/bitcoind

A bitcoind docker image.

[![seegno/bitcoind][docker-pulls-image]][docker-hub-url]
[![seegno/bitcoind][docker-stars-image]][docker-hub-url]
[![seegno/bitcoind][docker-size-image]][docker-hub-url]
[![seegno/bitcoind][docker-layers-image]][docker-hub-url]

## What is bitcoind?

*from [bitcoinwiki](https://en.bitcoin.it/wiki/Bitcoind)*

bitcoind is a program that implements the Bitcoin protocol for remote procedure call (RPC) use. It is also the second Bitcoin client in the network's history.

## Usage

### How to use this image

Create a `Dockerfile` in the root of application directory:

```Dockerfile
FROM seegno/bitcoind:latest
```

Then simply run:

```sh
$ docker build -t bitcoind
$ docker run --rm -it bitcoind
```

This image behaves like a binary, so you can pass any arguments to the bitcoind command to start it as needed:

```sh
$ docker run --rm -it bitcoin \
  -datadir=/var/lib/bitcoind \
  -printtoconsole \
  -regtest=1 \
  -rest \
  -rpcallowip=172.17.0.0/16 \
  -rpcpassword=bar \
  -rpcuser=foo \
  -server
```

You can also mount a directory it in a volume under `/var/lib/bitcoind` in case you want to access it on the host:

```
$ docker run -v ${PWD}/data:/var/lib/bitcoind -it --rm bitcoin \
  -datadir=/var/lib/bitcoind \
  -printtoconsole \
  -regtest=1 \
  -rest \
  -rpcallowip=172.17.0.0/16 \
  -rpcpassword=bar \
  -rpcuser=foo \
  -server
```

## Image Variants

The `seegno/bitcoind` image comes in a single flavor:

### `seegno/bitcoind:latest`

Tag that points to the latest bitcoind release available.

### `seegno/bitcoind:<version>`

Based on a slim Debian image, it targets a specific version branch of bitcoind (e.g. 0.11.x).

## Supported docker versions

This image is officially supported on docker version 1.7.1, with support for older versions provided on a best-effort basis.

## License

[License information](https://github.com/bitcoin/bitcoin/blob/master/COPYING) for the software contained in this image.

[License information](https://github.com/seegno/docker-bitcoind/blob/master/LICENSE) for the `seegno/bitcoind` docker project.

[docker-hub-url]: https://hub.docker.com/r/seegno/bitcoind
[docker-layers-image]: https://img.shields.io/imagelayers/layers/seegno/bitcoind/latest.svg
[docker-pulls-image]: https://img.shields.io/docker/pulls/seegno/bitcoind.svg
[docker-size-image]: https://img.shields.io/imagelayers/image-size/seegno/bitcoind/latest.svg
[docker-stars-image]: https://img.shields.io/docker/stars/seegno/bitcoind.svg
