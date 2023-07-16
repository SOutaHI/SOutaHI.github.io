---
title: dockerのネットワークモードの違い
date: 2023-07-15 10:00:00 +0900
categories: [Docker, Docker Compose]
tags: [docker, docker compose]
pin: false
---

## 概要

- docker container ではコンテナのネットワークモードを指定することができる
- default（指定しない場合）では、Bridge で開始される
- 指定する場合、bridge, host, none, servise, container の 5 つの内から、いずれかを選択することができる
- bridge、host, none の挙動を確認する

## 参考

- [docker compose network_mode](https://docs.docker.jp/v1.12/compose/compose-file.html#network-mode)
- [【Docker Network 第３章】-net = host オプションを理解する](https://qiita.com/MetricFire/items/b731c84975bd9894748d)

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
    # ネットワークモードの指定
    network_mode: "bridge"
    # コンテナ名称の指定
    container_name: robot1
    # containaerを維持するためのオプション
    # DockerfileのTailのどちらかかがあればよい
    tty: true
```

## network_mode を bridge にして実行

- コンテナを起動する

```bash
docker compose up
```

- docker inspects でコンテナの IP を確認する

```bash
docker inspect robot1
[
    {

        "NetworkSettings": {
            "Gateway": "172.17.0.1",
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                }
            }
        }
    }
]
```

- コンテナの IP は 172.17.0.2 になっている
- この時、ホスト PC（192.168.X.X）から、PING すると疎通していることが確認できる

```bash
ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.100 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.086 ms
64 bytes from 172.17.0.2: icmp_seq=3 ttl=64 time=0.085 ms
^C
--- 172.17.0.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2043ms
rtt min/avg/max/mdev = 0.085/0.090/0.100/0.006 ms
```

## network_mode を host にして実行

- コンテナを起動する

```bash
docker compose up
```

- docker inspects でコンテナの IP を確認する

```bash
docker inspect robot1
[
    {

        "NetworkSettings": {
            "Networks": {
                "host": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "Gateway": "",
                    "IPAddress": "",
                    "IPPrefixLen": 0,
                }
            }
        }

    }
]
```

- コンテナの IP が振られていない
- ネットワーク名前空間をホストと共有している

## network_mode を none にして実行

- コンテナを起動する

```bash
docker compose up
```

- docker inspects でコンテナの IP を確認する

```bash
docker inspect robot1
[
    {

        "NetworkSettings": {
            "Networks": {
                "none": {
                    "Gateway": "",
                    "IPAddress": "",,
                    "MacAddress": "",
                    "DriverOpts": null
                }
            }
        }

    }
]
```

- コンテナの IP が振られていない
- 外部ネットワークインターフェイス存在しない
