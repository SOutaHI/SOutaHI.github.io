---
title: コンテナの名称を指定する
date: 2023-07-15 10:00:00 +0900
categories: [Docker]
tags: [docker]
pin: false
---

## 概要

- コンテナを run する際に、コンテナの名称を指定する
- docker compose において、Serveice のキーがコンテナ名となる

## 参考

- [Docker compose container-name](https://docs.docker.jp/v1.12/compose/compose-file.html#container-name)

## 検証

### セットアップ

```bash
mkdir -p workspace/docker/ && cd workspace/docker
touch Dockerfile
touch compose.yaml
```

### Dockerfile

- image は Debian(11, Bullseye)を使用する

```bash

FROM debian:11
RUN apt-get update
# containerを維持するためのコマンド
CMD tail -f /dev/null
```

### compose.yaml

```bash
services:
  robot1:
    build:
      context: .
      dockerfile: Dockerfile
    # コンテナ名称の指定
    container_name: robot1
    # containaerを維持するためのオプション
    # DockerfileのTailのどちらかかがあればよい
    tty: true
```

## 実行

```bash
docker ps
CONTAINER ID   IMAGE                      COMMAND                  CREATED          STATUS             PORTS                                       NAMES
4010a1229e85   docker-robot1              "/bin/sh -c 'tail -f…"   10 seconds ago   Up 10 seconds                                                  robot1
```
