---
title: ros-noetic-desktop-fullの依存パッケージ
date: 2023-07-08 10:00:00 +0900
categories: [ros1]
tags: [ros1]
pin: false
---

## 概要

- ## ros のインストールでは、ros-(devel)-desktop-full が推奨されている
- このパッケージのインストールで、どのような依存パッケージがインストールされるか把握する
- develは、noeticとする

## ros-noetic-desktop-full の依存関係
- apt-cashe で依存を確認する
- ros-noetic-desktop-fullのdependencies
    - image-transportは、commpressed ImageのPublisher、Subscriberのクラスが実装されている
    - ros-control系はros-noetic-urdf-sim-tutorialに含まれている

``` bash
ros-noetic-desktop-full
├──ros-noetic-desktop
│  ├──ros-noetic-angles
│  ├──ros-noetic-common-tutorials
│  ├──ros-noetic-geometry-tutorials
│  ├──ros-noetic-joint-state-publisher-gui
│  ├──ros-noetic-robot
│  ├──ros-noetic-ros-tutorials
│  ├──ros-noetic-roslint
│  ├──ros-noetic-urdf-tutorial
│  ├──ros-noetic-visualization-tutorials
│  └──ros-noetic-viz
├──ros-noetic-perception
│  ├──ros-noetic-image-common
│  ├──ros-noetic-image-pipeline
│  ├──ros-noetic-image-transport-plugins
│  ├──ros-noetic-laser-pipeline
│  ├──ros-noetic-perception-pcl
│  ├──ros-noetic-ros-base
│  └──ros-noetic-vision-opencv
├──ros-noetic-simulators
│  ├──ros-noetic-gazebo-ros-pkgs
│  ├──ros-noetic-robot
│  ├──ros-noetic-rqt-common-plugins
│  ├──ros-noetic-rqt-robot-plugins
│  └──ros-noetic-stage-ros
└──ros-noetic-urdf-sim-tutorial
   ├── ros-noetic-controller-manager
   ├── ros-noetic-diff-drive-controller
   ├── ros-noetic-gazebo-ros
   ├── ros-noetic-gazebo-ros-control
   ├── ros-noetic-joint-state-controller
   ├── ros-noetic-position-controllers
   ├── ros-noetic-robot-state-publisher
   ├── ros-noetic-rqt-robot-steering
   ├── ros-noetic-rviz
   ├── ros-noetic-urdf-tutorial
   └── ros-noetic-xacro
```

