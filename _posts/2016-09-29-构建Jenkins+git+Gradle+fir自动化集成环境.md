---
title: 构建Jenkins+Git+Gradle+fir自动化集成环境
date: 2016-09-29 13:34:50 +08:00
categories:
- Android
- Jenkins
layout: post
author: Darre
---

## 背景

随着业务的需要，版本迭代更新越来越频繁，而最麻烦的是手动打包给测试人员，让他们进行测试，这样既消耗时间，又影响效率。于是根据这一痛点，开始搭建一个自动化集成环境。首先简单介绍一下集成环境的构成：

Jenkins：是一个开源软件项目，旨在提供一个开放易用的软件平台。它是基于Java开发的一种持续集成工具，用于监控持续重复的工作。（来源百度百科）

Git：是一款免费、开源的分布式版本管理系统，用于管理任何大小的项目

Gradle：自动化构建工具

Fir：是一个第三方免费应用内测托管平台

## 前期准备

1\. 搭建Jenkins环境：

 （1）下载Jenkins.war包。地址：[https://jenkins.io/index.html](https://jenkins.io/index.html)

 （2）在存放的目录下例如：`F:\soft\jenkins` 执行 `java -jar jenkins.war`（要先安装Java环境）

 （3）打开浏览器运行http://localhost:8080，即可打开Jenkins页面

2\. Jenkins环境搭建好后安装插件：Git plugin，Gradle plugin，Android Lint Plugin，fir plugin，插件下载：

 （1）在管理页面左侧找到“系统管理” -> “管理插件” -> “可选插件”选中要安装的插件后点击“直接安装”直到安装完成；

 （2）在“管理插件”中的“已安装”中可以查看是否已经安装

## 搭建集成环境

1\. 进入Jenkins，点击左侧“系统管理”

![系统管理]({{ site.baseurl }}/assets/posts/2016-09-29-系统管理.jpg)

2\. 进入“系统管理”，点击右侧栏 “Global Tool Configuration”

![Global Tool Configuration]({{ site.baseurl }}/assets/posts/2016-09-29-global-tool-configuration.jpg)

3\. 进入 “Global Tool Configuration” 配置JDK、Git、Gradle

![配置]({{ site.baseurl }}/assets/posts/2016-09-29-配置.jpg)

4\. 回到面板点击“新建”项目

![新建项目]({{ site.baseurl }}/assets/posts/2016-09-29-新建项目.jpg)

5\. 点击“配置”，配置项目的源码

![新建项目配置]({{ site.baseurl }}/assets/posts/2016-09-29-新建项目配置.jpg)

点击 Credentials 后面的 “Add” 填写 Git 的用户名密码

6\. 构建触发器选中 “Poll SCM”

![poll SCM]({{ site.baseurl }}/assets/posts/2016-09-29-poll-SCM.jpg)

7\. 点击“增加构建步骤”选择“Invoke Gradle Script”安装Gradle plugin后才会有

![构建步骤]({{ site.baseurl }}/assets/posts/2016-09-29-构建步骤.jpg)

8\. 点击“增加构建后操作步骤”选择“public android lint results”、“Archive the artifacts”

![lint]({{ site.baseurl }}/assets/posts/2016-09-29-lint.jpg)

9\. 下载fir plugin插件：[fir-plugin-0629.hpi](http://7xju1s.com1.z0.glb.clouddn.com/fir-plugin-0629.hpi)

10\. 安装fir plugin插件：点击“系统管理” -> “插件管理” -> “高级”在上传插件一栏“选择文件”点击“上传”按钮。

11\. 回构建的项目中，点击“配置” -> “增加构建后操作步骤” -> “Upload to fir.im”

![Upload to fir.im]({{ site.baseurl }}/assets/posts/2016-09-29-fir.jpg)

具体步骤参照：[http://blog.fir.im/jenkins](http://blog.fir.im/jenkins/)

12\. 配置完成后点击“Apply”,“保存”，返回到项目首页点击“立即构建”即可。

构建成功后日志显示如下：

![Log]({{ site.baseurl }}/assets/posts/2016-09-29-log.jpg)

13\. 打开 [fir.im](http://fir.im)，进入到我的应用，即可看到上传结果

## 参考

1. [亲自实践Jenkins+Git+Gradle自动化构建Android应用](http://blog.csdn.net/xiaoyaosheng86/article/details/50855719)
2. [Windows下搭建基于Jenkins+Git+Gradle的Android持续集成](http://www.mobibrw.com/2016/3695)
