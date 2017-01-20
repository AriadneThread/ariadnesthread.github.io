---
layout: post
title:  "Windows下React Native环境搭建"
date:   2017-01-20 09:32:50
author: Darre
categories: jekyll
---

## 前期准备：

1、	安装Python环境，并配置环境变量：

下载地址：https://www.python.org/downloads/release/python-2711/ 

2、	安装git（傻瓜式安装）

下载地址：https://git-for-windows.github.io/

3、	配置Android环境

4、	安装NodeJS，这步也是傻瓜式安装。

## 开始设置：

安装完环境后，需要设置npm镜像以加速后面的过程。

我们可以输入以下命令：

npm config set registry https://registry.npm.taobao.org 

npm config set disturl https://npm.taobao.org/dist 

注意：如果不执行上面的命令在创建项目的时候会报错：Command `npm install --save --save-exact react-native` failed.

## 安装React Native

![](https://raw.githubusercontent.com/AriadneThread/ariadnethread.github.io/master/assets/posts/2017-01-20-react-native.png)

用git 命令下载：

git clone https://github.com/facebook/react-native.git

这个时候我们可以找到react native目录

![](https://raw.githubusercontent.com/AriadneThread/ariadnethread.github.io/master/assets/posts/2017-01-20-react_native_save.png)

安装react-native命令行工具：

npm install -g react-native-cli

以上步骤就可以完成整个设置，下面来创建Hello_React_native项目

## 创建项目：

1、	创建自己的项目路径，如：F:\Workspace\ReactNative

2、	Git或CMD命令行上输入：react-native init Hello_React_native来创建项目，这里需要等待一段时间。

执行上面没有问题就可以在设备上运行了。

## 设备上运行：

这个可以参考官方文档：

1、	打开USB调试：

设备连接成功可以输入以下命令查看

adb devices

上面没有问题，如果想看看有没有成功可以在电脑上打开浏览器访问以下地址：http://localhost:8081/index.android.bundle?platform=android

然后在git上运行：react-native run-android命令开始对项目进行编译

刚开始会出现红屏，这是正常的，以下分不同的android版本

## Android 5.0及以上使用adb reverse命令

1、	数据线连接电脑，打开手机USB调试

2、	运行adb reverse tcp:8081 tcp:8081

3、	不需要更多配置，使用Reload JS和其他开发选项了。

## Android 5.0以下同意WIFI连接本地开发服务器

1、	首先确保电脑和手机在同一个WiFi环境下

2、	在设备上运行React Native应用

3、	如果出现红屏别着急，这是正常现象可以执行下面步骤解决

4、	运行adb shell input keyevent 82 可以打开开发者菜单

5、	点击进入Dev Settings

6、	点击Debug server host for device

7、	输入电脑的IP和端口号（8081）

8、	回到开发者菜单选择Reload JS就可以了

最后出现如图：

![](https://raw.githubusercontent.com/AriadneThread/ariadnethread.github.io/master/assets/posts/2017-01-20-react-native-run.png)

说明已经成功运行了。
