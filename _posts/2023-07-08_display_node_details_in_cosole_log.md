---
title: ROS1でConsole Logを表示する際に、Node名も合わせて表示する
date: 2023-07-08 10:00:00 +0900
categories: [ros1]
tags: [ros1]
pin: false
---

## 概要

- 複数の Node を立てる Launch を実行した際に、Console に表示される Log をそれぞれの Node で区別する
- log の内容を記載する際に、Node 名も手打せずに、Node 名を表示する

## 参考

- [Include node name in console log output: ROS ANSWERS](https://answers.ros.org/question/243374/include-node-name-in-console-log-output/)
- [Console Output Formatting](http://wiki.ros.org/rosconsole#Console_Output_Formatting)

##　使用方法

- launch に環境変数として追加する

``` xml
<launch>
  <!-- このenvで指定している -->
  <env name="ROSCONSOLE_FORMAT" value="[${severity}] [${time}] [${node}]: ${message}"/>
  <node pkg="mypackage" type="mynode" name="mynode" output="screen"/>
</launch>

```

- noetic の場合には、time もしくは walltime の format も指定することができる

``` xml
<launch>
  <!-- このenvで指定している -->
  <env name="ROSCONSOLE_FORMAT" value="[${severity}] [${time:format %Y-%m-%d %H:%M:%S}] [${node}]: ${message}"/>
  <node pkg="mypackage" type="mynode" name="mynode" output="screen"/>
</launch>

```
