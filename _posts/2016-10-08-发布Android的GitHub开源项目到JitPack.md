---
title: 发布Android的GitHub开源项目到JitPack
date: 2016-10-08 11:09:14 +08:00
categories:
- Android
layout: post
author: wuzhen
---

之前使用 JCenter 发布了一个 GitHub 开源项目，感觉整个流程很麻烦，而且 JCenter 经常连不上，所以最近几个项目都是发布到了 JitPack。

## 什么是JitPack

> Easy to use package repository for Git
>
> Publish your JVM and Android libraries

[JitPack](https://jitpack.io) 是一个类似于 [JCenter](https://bintray.com/bintray/jcenter)  和 [MavenCentral](http://mvnrepository.com/) 的仓库，流程简单，可以非常快捷的发布 GitHub 开源项目，方便使用 Gradle 进行依赖管理。

下面介绍怎么发布 GitHub 上的项目：

### 1. 创建项目

在 [GitHub](https://github.com) 上创建项目：

![GitHub创建项目](/assets/posts/2016-10-08-create_repository.png)

创建完成后会打开项目，因为当前项目是空白的，所以会显示以下的页面：

![GitHub空白项目](/assets/posts/2016-10-08-blank_repository.png)

### 2. 上传项目代码到GitHub

把本地的 Library 项目使用 Git 上传到 GitHub，具体的 Git 操作可以按照项目页面中的提示进行，不再赘述。

### 3. 创建项目的Release版本

需要创建项目的 Release 版本才能上传到 JitPack。

创建的方式有两种：

**1\. 使用 Git 添加标签 tag，并且提交到 GitHub**

```
git tag -a <tagname> -m <msg>
git push --tags
```

操作完后即可在 GitHub 的项目中看到新创建的 Release 版本：

![查看Release版本](/assets/posts/2016-10-08-view_release.png)

![Release版本列表](/assets/posts/2016-10-08-release_version.png)

**2\. 在GitHub项目中创建Release版本**

在 GitHub 的项目中点击 "release" 标签栏：

![点击release标签栏](/assets/posts/2016-10-08-view_release.png)

在页面的右上角点击 "Draft a new release" 按钮，进入到创建 Release 版本的页面：

![点击创建Release版本](/assets/posts/2016-10-08-draft_release.png)

填写必要的信息，即可创建一个新的 Release 版本：

![Release版本列表](/assets/posts/2016-10-08-release_version.png)

### 4. 发布项目到JitPack

进入 [JitPack](https://jitpack.io)，使用 GitHub 账号登录

把项目的地址填写到首页的输入框中，点击 "Look Up" 按钮即可自动进行发布：

![Look up](/assets/posts/2016-10-08-jitpack_lookup.png)

发布成功后显示如下图：

![发布成功](/assets/posts/2016-10-08-lookup_succeed.png)

点击 Log 下的图标可以查看 build 的日志。

点击 "Get it" 可以查看 Gradle 的依赖配置说明，如下图：

![Gradle依赖说明](/assets/posts/2016-10-08-jitpack_dependencies.png)

完成上述步骤后即成功发布了开源的 Library，对比 JCenter 的发布流程简化了不少，使用起来也非常的方便。
