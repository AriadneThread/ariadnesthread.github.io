---
title: 移动端js倒计时问题
date: 2016-12-30 00:00:00 +08:00
layout: post
author: 张瑞峰
categories: html5
---

再移动端下，当程序再后台运行的时候，js倒计时也就停止执行了，当在打开后台运行的程序的时候，时间已经不准确了
解决办法：一套js倒计时程序一直在执行，然后写个旧市倒计时异步请求服务器的时间，然后去比对两者的时间，如果时间差差2秒以上的话，那么第一套js的时间就取异步请求的时间
代码如下

```这个是前端js执行
   var ountdown=“倒计时时长“；
   var interval=setInterval(getRTime,1000);//显示倒计时
    function getRTime(){
        countdown=countdown-1;
        if(countdown<=0){
            location='/m/pay/payStatus?order_no='+order_no;
        }else{
            var d=Math.floor(countdown/60/60/24);
            var h=Math.floor(countdown/60/60%24);
            var m=Math.floor(countdown/60%60);
            var s=Math.floor(countdown%60);
            if(m<10){
                m="0"+m;
            }
            if(s<10){
                s="0"+s;
            }
            //document.getElementById("c1").innerHTML = countdown+" "+m+":"+s;
            var string="<span>"+m+"</span> : <span>"+s+"</span>";
            document.getElementById("countdown").innerHTML = string;
        }
    }
    
    
    //这个异步请求服务器的时间，计算与上面的时间差
        setInterval(function(){
        $.ajax({
            url: '/m/pay/countdown',
            data:{orderTime:orderTime},
            dataType:'text',
            success: function(data) {
                var d;
                d=data-1;
                if(d<=0){
                    location='/m/pay/payStatus?order_no='+order_no;
                }else{
                    var m=Math.floor(d/60%60);
                    var s=Math.floor(d%60);
                    if(m<10){
                        m="0"+m;
                    }
                    if(s<10){
                        s="0"+s;
                    }
                    // document.getElementById("c2").innerHTML = d+" "+m+":"+s;
                    if((countdown-d)>2){
                        countdown=d;
                        var string="<span>"+m+"</span> : <span>"+s+"</span>";
                        document.getElementById("countdown").innerHTML = string;
                    }
                }
            }
        });
    },1000);
    
```
