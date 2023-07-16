---
title: PointCloud2のcallbackの型に、PCLのPoint型を指定する
date: 2023-07-15 10:00:00 +0900
categories: [ROS1, PCL]
tags: [ros1, pcl]
pin: false
---


## 概要
- sensor_msgs/PointCloud2型のmessegeをCallback関数で受け取る時に、PCLのPoint型を指定することができる
### メリット
- Callback後に点群に対して、PCLを用いた処理を入れる場合、PCLに変換する処理する処理を削減することができる

## 参考
- [ROS WiKi - Subscribing to different point cloud message types](http://wiki.ros.org/pcl/Overview)

## PCLのPoint型をCallbackの引数の型に指定する
- 例えばPublisher側が、pcl::PointCloud<pcl::PointXYZRGB>でPublishしている場合には、次のようなCallback関数でmessasegeを受け取ることができる

``` cpp
// Need to include the pcl ros utilities
#include <pcl_ros/point_cloud.h>

// callback signature, assuming your points are pcl::PointXYZRGB type:
void callback(const pcl::PointCloud<pcl::PointXYZRGB>::ConstPtr&);

// create a templated subscriber
ros::Subscriber sub = nh.subscribe<pcl::PointCloud<pcl::PointXYZRGB> > (topic, queue_size, callback);
```

- callbackにPCLのPointTypeを指定するためには、pcl_rosのpoint_type.hをincludeする必要がある
- sensor_msgs/PoitnCloud2と同様にConstPtrで受け取ることが可能

### PCLのPoint型をPublishするmessageの型に指定する
- publisher側は次のようになる
- 下記のようにPublishする場合、Subscriber側は、messegeをsensor_msg/PointCloud2型でも受け取ることができる

``` cpp
// Need to include the pcl ros utilities
#include "pcl_ros/point_cloud.h"

// you have an object already, eg with pcl::PointXYZRGB points
pcl::PointCloud<pcl::PointXYZRGB> cloud;

// create a templated publisher
ros::Publisher pub = nh.advertise<pcl::PointCloud<pcl::PointXYZRGB> > (topic, queue_size);

// and just publish the object directly
pub.publish(cloud);

```
