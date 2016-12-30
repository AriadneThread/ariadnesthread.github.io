---
layout: post
title:  "Web移动端Fixed布局的解决方案"
date:   2016-12-30 
author: 金丽新

categories: html5
---

fixed 属性在移动端不支持的情况。分两种：一种不包含input  等唤起软键盘的元素。 一种包含唤起软键盘的元素。解决方法不同。

第一种情况：

 注意：

浮动都是使用css中的position: fixed; 进行控制，但是这种方法只适用于电脑端的，而在手机端却不行，效果就是使用手机浏览器打开网页时显示是底部，但是当向下滚动时，而这个浮动的却不跟着一起，这时大家需要在head间添加以下代码即可解决手机浏览器不支持position: fixed的bug了。

```
<meta name="viewport" content="width=device-width,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no"> 
```


第二种情况查询解决方法。

 fixed 元素和输入框(input 元素)同时存在的情况， fixed 元素在有软键盘唤起的情况下，会出现问题。

原因：软键盘唤起后，页面的 fixed 元素将失效（效果变成了 absolute 定位），所以当页面超过一屏且滚动时，失效的 fixed 元素就会跟随滚动了。

解决思路：

如果使 fixed 元素的父级不出现滚动，而将原 body 滚动的区域域移到 main 内部，而 header 和 footer 的样式不变。

```

        <body>
         <header>
            <!-- 头部 -->
        </header>
        <main>
            <!-- 内容 -->
        </main>
            
        <!-- fixed定位的底部 -->
        <footer>
            <input type="text" placeholder=""/>
            <button class="submit">提交</button>
        </footer>
        </body>

```

```

    main {
        /* main绝对定位，进行内部滚动 */
        position: absolute;
        top: 50px;
        bottom: 50px;
        /* 使之可以滚动 */
        overflow-y: scroll;
        /* 增加该属性，可以增加弹性 */
        -webkit-overflow-scrolling: touch;
    }
    
    main .content {
        height: 1000px;
    }

```

为了防止页面露底，可以在页面拖拽到边缘的时候，通过判断拖拽方向以及是否为边缘来阻止 touchmove 事件，防止页面继续拖拽.

参考：

```

// 防止内容区域滚到底后引起页面整体的滚动
var content = document.querySelector('main');
var startY;

content.addEventListener('touchstart', function (e) {
    startY = e.touches[0].clientY;
});

content.addEventListener('touchmove', function (e) {
    // 高位表示向上滚动
    // 底位表示向下滚动
    // 1容许 0禁止
    var status = '11';
    var ele = this;

    var currentY = e.touches[0].clientY;

    if (ele.scrollTop === 0) {
        // 如果内容小于容器则同时禁止上下滚动
        status = ele.offsetHeight >= ele.scrollHeight ? '00' : '01';
    } else if (ele.scrollTop + ele.offsetHeight >= ele.scrollHeight) {
        // 已经滚到底部了只能向上滚动
        status = '10';
    }

    if (status != '11') {
        // 判断当前的滚动方向
        var direction = currentY - startY > 0 ? '10' : '01';
        // 操作方向和当前允许状态求与运算，运算结果为0，就说明不允许该方向滚动，则禁止默认事件，阻止滚动
        if (!(parseInt(status, 2) & parseInt(direction, 2))) {
            stopEvent(e);
        }
    }
});

```