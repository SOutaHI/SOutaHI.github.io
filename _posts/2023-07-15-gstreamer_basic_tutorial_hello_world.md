---
title: Gstreamer basic tutorialのhello world
date: 2023-07-15 10:00:00 +0900
categories: [Gstreamer]
tags: [gstreamer]
pin: false
---

## 概要

- Gstreamer basic tutorial の hello world を実行する
- パイプラインに繋いでいるエレメントにも注目する

## 参考

- [Welcome to the GStreamer Tutorials!](https://gstreamer.freedesktop.org/documentation/tutorials/index.html?gi-language=c)

## 環境

- OS: ubuntu 20.04
- CPU: intel(R) Core(TM) i9-11900H
- RAM: DDR4 32GB

##　 Basic tutorial 1 : Hello world

- [Basic tutorial 1: Hello world!](https://gstreamer.freedesktop.org/documentation/tutorials/basic/hello-world.html?gi-language=c#basic-tutorial-1-hello-world)

### セットアップ

- gstreamer のパッケージを apt でインストールする
  - [Install GStreamer on Ubuntu or Debian](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c#install-gstreamer-on-ubuntu-or-debian)

```bash
sudo apt-get install -y libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev libgstreamer-plugins-bad1.0-dev gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly gstreamer1.0-libav gstreamer1.0-tools gstreamer1.0-x gstreamer1.0-alsa gstreamer1.0-gl gstreamer1.0-gtk3 gstreamer1.0-qt5 gstreamer1.0-pulseaudio
```

### 実行

- コードを記述するためのファイルを生成する

```bash
mkdir -p ~/workspace/gstreamer/tutorial/basic/
cd ~/workspace/gstreamer/tutorial/basic
touch basic-tutorial-1.c
```

- テキストエディタで、basic-tutorial-1.c を開き、コードを記述する
  - gstreamer の tutorial は、C 言語で記述されているため、C の拡張子のファイルを作成する

```c
#include <gst/gst.h>

#ifdef __APPLE__
#include <TargetConditionals.h>
#endif

int
tutorial_main (int argc, char *argv[])
{
  GstElement *pipeline;
  GstBus *bus;
  GstMessage *msg;

  /* Initialize GStreamer */
  gst_init (&argc, &argv);

  /* Build the pipeline */
  pipeline =
      gst_parse_launch
      ("playbin uri=https://gstreamer.freedesktop.org/data/media/sintel_trailer-480p.webm",
      NULL);

  /* Start playing */
  gst_element_set_state (pipeline, GST_STATE_PLAYING);

  /* Wait until error or EOS */
  bus = gst_element_get_bus (pipeline);
  msg =
      gst_bus_timed_pop_filtered (bus, GST_CLOCK_TIME_NONE,
      GST_MESSAGE_ERROR | GST_MESSAGE_EOS);

  /* See next tutorial for proper error message handling/parsing */
  if (GST_MESSAGE_TYPE (msg) == GST_MESSAGE_ERROR) {
    g_error ("An error occurred! Re-run with the GST_DEBUG=*:WARN environment "
        "variable set for more details.");
  }

  /* Free resources */
  gst_message_unref (msg);
  gst_object_unref (bus);
  gst_element_set_state (pipeline, GST_STATE_NULL);
  gst_object_unref (pipeline);
  return 0;
}

int
main (int argc, char *argv[])
{
#if defined(__APPLE__) && TARGET_OS_MAC && !TARGET_OS_IPHONE
  return gst_macos_main (tutorial_main, argc, argv, NULL);
#else
  return tutorial_main (argc, argv);
#endif
}

```

- コンパイルし、実行する

```bash
gcc basic-tutorial-1.c -o basic-tutorial-1 `pkg-config --cflags --libs gstreamer-1.0`
./basic-tutorial-1.c
```

### Playbin

- [gstreamer documentation playbin](https://gstreamer.freedesktop.org/documentation/playback/playbin.html?gi-language=c#playbin)
- このチュートリアルでは、gstreamer の pipeline に、playbin を使用している
  - playbin に、.webcam 形式の動画を指定し、動画をウィンドウに出力している
- playbin の特徴
  - playbin は Playback plugin の一種である
  - 指定された URL から自動で適切なソースを選択し、メディアを再生する機能を持つ
    - url でも、file パスでも指定することができる
