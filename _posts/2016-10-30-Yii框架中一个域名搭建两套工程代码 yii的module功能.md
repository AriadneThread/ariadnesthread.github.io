---
title: Yii框架中一个域名搭建两套工程代码 yii的module功能
date: 2016-10-30 22:55:00 +08:00
categories:
- php
- yii框架
layout: post
author: 张瑞峰
---

Yii框架中一个域名搭建两套工程代码，代码结构如下：

![]({{ site.baseurl }}/assets/post/yii-php/2016-10-31-yii-1.png)

一般的域名是走正常的yii框架代码，如果这个域名下面还有一套工程。比如m.komovie.cn/m，这时候走的就是红色框里面的代码，具体配置如下

![]({{ site.baseurl }}/assets/post/yii-php/2016-10-31-yii-2.png)

配置还是走yii默认的配置文件protected/config/product.php，上图中红色框代表路径，黑色框是新添加的内容

![]({{ site.baseurl }}/assets/post/yii-php/2016-10-31-yii-3.png)

上图中黑色框里面的命名要和上上图中黑色框里面的m要一致，在MModel中可以自定义配置 layouts的路径，当前的配置走的是 modules/m/view/layout.php     错误页面也可以自定义到module/m里面的view，但是发现有时候会报502错误，暂时没有找到原因，干脆还是把错误页面放在了 protected/view/error/error.php,如下图调用就行

![]({{ site.baseurl }}/assets/post/yii-php/2016-10-31-yii-4.png)

错误页面代码如下

![]({{ site.baseurl }}/assets/post/yii-php/2016-10-31-yii-5.png)

到此，已经配置成功，访问m.komovie.cn走的是yii默认的框架代码，访问m.komovie.cn/m/访问的是刚才配置的module/m里面的代码了，不过有时候某些路径还是回报502，需要在yii的配置文件的配置protected/config/product.php，代码如下

...

        'urlManager'=>array(
            'urlFormat'=>'path',
            'rules'=>array(
                'm'=>'m/index/index',
                'm/<controller:index>/<action:view>'=>'m/index/view',
                'm/<controller:index>/<action:view>/<aid:\d+>'=>'m/index/view',
            ),
        ),

...



