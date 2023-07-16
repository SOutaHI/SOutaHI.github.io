---
title: systemdでdeamonを設定する
date: 2023-07-15 10:00:00 +0900
categories: [Linux, Systemd]
tags: [linux, systemd]
pin: false
---

## 概要

- 常駐させたいプロセスがあり、毎回手作業で実行すると、実行することを忘れてしまうことがある
- 上記を防ぐために、プロセスを deamon 化する

## 環境

- OS: ubuntu 20.04
- CPU: intel(R) Core(TM) i9-11900H
- RAM: DDR4 32GB

## 参考

- [systemd を利用してプロセスをデーモン化する](https://cameong.hatenablog.com/entry/2016/10/18/121400)
- [shellとかコマンドとかをサービス化(デーモン化)するやつ](https://qiita.com/min06s/items/cca78fb9796406e6dc80)
- [systemdでデーモン化を簡単に行う](https://qiita.com/NNNiNiNNN/items/7f4c26495b6fda156183)
- [Ubuntuで起動時に自動でShellScriptを実行する方法](https://qiita.com/MAI_onishi/items/74edc40a667dd2dc633e)

## echoコマンドをdeamon化する
### serviceの作成
- echo_hogehoge.seviceを/etc/systemd/systemの直下におく

``` bash
cd /etc/systemd/system
sudo vim echo_hogehoge.sh
```

- echo_hogehoge.shの内容
    - シェバンを入れないと、Serviceの起動に失敗する

``` bash
#!/bin/sh
ping localhost > ~/test.log

```

- shell scriptの権限を変更する
    - 実行権限が無いと、servriceから実行できない
``` bash
chmod +x echo_hogehoge.sh
```

- 次の内容にする

``` bash
[Unit]
Description=echo hogehoge
After=syslog.target network.target

[Service]
User=user名
Type=simple
# スクリプトファイルはfull pathで指定する
ExecStart=/home/user名/script/echo_hogehoge.sh
Restart=always


[Install]
WantedBy=multi-user.target
```

### serviceの起動
- 作成したServiceを有効化する

``` bash
sudo systemctl enable echo_hogehoge.service
sudo systemctl start echo_hogehoge.service

```
### serviceの状態確認
- statusコマンドで確認する

``` bash
systemctl status echo_hogehoge.service
```
