---
layout: post
title:  "Jekyll Setup"
date:   2016-08-22 13:47:04 +0800
author: maokaiyin

categories: jekyll
---

### [Requirements](https://jekyllrb.com/docs/installation/)

* Linux, Unix, or Mac OS X

### Ruby Setup

安装[Ruby](https://www.ruby-lang.org/en/documentation/installation/)

或使用Ruby version manager安装

* rbenv (推荐)
* rvm

安装2.0以上Ruby，最新版本为佳。

### Gem Source
修改[gem source](https://ruby.taobao.org/)

### Bundle

	gem install bundle

进入jekyll目录

	bundle install

本地预览
	
	bundle exec jekyll serve


# Docker

上面的步骤看起来很少，但在实际操作的时候，还会有不少出错的地方。那么对于环境打包，`Docker`手到擒来。去[hub](hub.docker.com)看了下果然有`Jekyll`的官方`image`。

安装[Docker](https://www.docker.com/products/overview)

Pull Jekyll image

	docker pull jekyll/jekyll
	
> 建议使用国内的docker镜像源（[daocloud](https://get.daocloud.io/)）

Run in container

	docker run -v ~/PATH_OF_YOUR_BLOG_SOURCE:/blog -p 4000:4000 -i -t jekyll/jekyll /bin/bash

解释下上面的命令：将`~/PATH_OF_YOUR_BLOG_SOURCE`路径共享到`container`的`/blog`, 本地端口`4000`对应`container`的`4000`，后边就是指定`image`，运行`bash`。

进入`container`后，进入目录

	cd /blog

看到自己的blog文件

	bundle install
	
本地预览
	
	bundle exec jekyll serve
	

```
bash-4.3# bundle exec jekyll s
Configuration file: /blog/_config.yml
            Source: /blog
       Destination: /blog/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
                    done in 1.494 seconds.
 Auto-regeneration: enabled for '/blog'
Configuration file: /blog/_config.yml
    Server address: http://0.0.0.0:4000/
  Server running... press ctrl-c to stop.
```

浏览器访问[http://127.0.0.1:4000/](http://127.0.0.1:4000/)预览
