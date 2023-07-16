---
title: gstreamerで使用可能なrtspのURL Scheme
date: 2023-07-15 10:00:00 +0900
categories: [Gstreamer, RTSP]
tags: [gstreamer, rtsp]
pin: false
---

## 概要

- gstreamer のエレメントにおいて、rtspsrc がある
- rtsp パケットを src とするコマンドであり、受信可能なURL Schemeは複数種類存在する
- 受信可能なプロトコルを紹介する

## 参考
- [Gstreamer/gst-plugin-good](https://github.com/GStreamer/gst-plugins-good/tree/20bbeb5e37666c53c254c7b08470ad8a00d97630)
- [Gstreamer/gst-rtsp-server](https://github.com/GStreamer/gst-rtsp-server)
- [RFC 7826 - Real-Time Streaming Protocol Version 2.0 日本語訳](https://tex2e.github.io/rfc-translater/html/rfc7826.html)
- [gStreamer/Pluginのビルド手順(Ubuntu, Windows)](https://qiita.com/nakakura/items/4b2c1c312236ea96824b)

## 使用可能なrtspのURL Scheme
- gst-plugin-goodのgst/rtsp/gstrtcpsrc.cを参考にする
    - gst-plugin-goodはgstreamerのpluginの中で、公式が使用を推奨するプラグイン
    - コードの品質が良く、ドキュメンテーションも書かれている
    - ライセンスはLGPL
- 上記ファイルの9389行目に、使用可能なrtspのプロトコル種類を返す関数が定義されている
    - [gst_rtspsrc_uri_get_protocols (GType type)](https://github.com/GStreamer/gst-plugins-good/blob/20bbeb5e37666c53c254c7b08470ad8a00d97630/gst/rtsp/gstrtspsrc.c#L9387C1-L9387C30)

- 使用可能なURL Schemeは次の通り
    - rtsp
    - rtspu
    - rtspt
    - rtsph
    - rtsp-sdp
    - rtsps
    - rtspsu
    - rtspst
    - rtspsh

- 上記の中で、RTSPのURL Schemeとして[rtspのRFC](https://tex2e.github.io/rfc-translater/html/rfc7826.html#22-14-1--The-rtsp-URI-Scheme)に定義されているものは、次の3つ
    - rtsp
       - real time streaming protocol
    - rtspu
        - rtsp over UDP
    - rtsps
        - rtsp over TLS

- RFCのURLスキーマを考えると、他も異なるプロトコルで覆っているものと考えられる
    - rtspt
        - rtsp over tcp
    - rtsph
        - rtsp over http
    - rtsp-sdp
        - SDPを付随しているrtsp
    - rtspsu
        - rtsp over TLS over UDP
    - rtspst
        - rtsp over TLS over TCP
    - rtspsh
        - rtsp over TLS over http
