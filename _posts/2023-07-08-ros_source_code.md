---
title: ros1のソースコード
date: 2023-07-08 10:00:00 +0900
categories: [ROS1]
tags: [ros1]
pin: false
---

## 概要
- ros１でPublisherやSubscriber、カメラ周りの実装を確認したい時がある
- 各実装をsubmoduleとするリポジトリを作成し、実装を確認したいときに参照する

## リポジトリ
- 現在は、ros_commとros_perceptionを追加している
    - https://github.com/SOutaHI/ros_src
- ディレクトリ構造は次の通り

``` bash
├── ros_comm
│   ├── clients
│   │   ├── roscpp
│   │   └── rospy
│   ├── ros_comm
│   ├── test
│   ├── tools
│   │   ├── rosbag
│   │   ├── rosbag_storage
│   │   ├── rosgraph
│   │   ├── roslaunch
│   │   ├── rosmaster
│   │   ├── rosmsg
│   │   ├── rosnode
│   │   ├── rosout
│   │   ├── rosparam
│   │   ├── rosservice
│   │   ├── rostest
│   │   ├── rostopic
│   │   └── topic_tools
│   └── utilities
│       ├── message_filters
│       ├── roslz4
│       ├── roswtf
│       └── xmlrpcpp
└── ros_perception
    ├── image_common
    │   ├── camera_calibration_parsers
    │   ├── camera_info_manager
    │   ├── image_common
    │   ├── image_transport
    │   └── polled_camera
    ├── image_pipeline
    │   ├── camera_calibration
    │   ├── depth_image_proc
    │   ├── image_pipeline
    │   ├── image_proc
    │   ├── image_publisher
    │   ├── image_rotate
    │   ├── image_view
    │   ├── stereo_image_proc
    │   └── wiki_files
    └── image_transport_plugins
        ├── compressed_image_transport
        ├── image_transport_plugins
        └── theora_image_transport


```