
---
layout: post
title:  "js与app交互"
date:   2016-11-30 14:55:00
author: 张瑞峰

categories: js
---

```
//app登陆交互代码开始=============================
function connectWebViewJavascriptBridge(callback) {
    if (window.WebViewJavascriptBridge) {
        callback(WebViewJavascriptBridge)
    } else {
        document.addEventListener('WebViewJavascriptBridgeReady', function() {
            callback(WebViewJavascriptBridge)
        }, false)
    }
}
//app登陆交互代码结束=============================
var version=navigator.userAgent.match(/komovie_([^_]+)_[i|a]/);
if(version) {
    var pos = version[1].indexOf(".");
    version = version[1].substr(0, pos + 1) + version[1].substr(pos).replace(/\./g, "");
    if (version >= 5.09) {
        connectWebViewJavascriptBridge(function(bridge) {
            bridge.init(function(message, responseCallback) {
                var data = { 'Javascript Responds':'Wee!' }
                responseCallback(data)
            })

            bridge.callHandler('getSessionFromApp', {login: appLogin}, function() {});//从app进入H5页面   0不调去app的登陆页面   1唤起你的登陆页面
            //发请求后接收session值
            bridge.registerHandler('getAppUserInfo', function(data) {
                var data=JSON.parse(data);
                //alert(data['sessionId']);
                //alert(data['login']);

                if(data['login']=='1'){
                    $.ajax({
                        async: false,
                        url:Domain_name+"/login/appLogin",
                        data:{sessionId:data['sessionId']},
                        dataType:'json',
                        success:function(d){
                            if(d['status']==0){
                                if(returnBackUrl){
                                    location=returnBackUrl;
                                }else{
                                    location=Domain_name+"/";
                                }
                            }else{
                                location=Domain_name+"/login";
                            }
                        }
                    })
                }else{
                    $.ajax({
                        //async: false,
                        url:Domain_name+"/login/appLogin",
                        data:{sessionId:data['sessionId']},
                        dataType:'json',
                        success:function(d){
                            //alert(data.status);
                        }
                    })
                }
            })
            //登陆返回操作
            bridge.registerHandler('returnLogin',function(){
                if(returnBackUrl){
                    location=returnBackUrl;
                }else{
                    location=Domain_name+"/";
                }
            })

            //唤起app发现页面上面的标签栏
            //,{tab:"2", link:"http://m.komovie.cn", name:"周边"}
            if (ip =='106.2.186.10') {
                //bridge.callHandler('getTabs', [{tab:"0", link:"http://m.komovie.cn/app/activityList", name:"热门活动"},{tab:"1", link:"komovie://kotaPager", name:"约电影"},{tab:"2", link:"http://club.komovie.cn/forum.php?mod=guide&view=new&mobile=2", name:"社区"}], function() {});
                //bridge.callHandler('getTabs', [{tab:"3", link:"http://club.komovie.cn/forum.php?mod=guide&view=new&mobile=2", name:"首页"},{tab:"1", link:"komovie://kotaPager", name:"约电影"}], function() {});
                bridge.callHandler('getTabs', [{tab:"0", link:"http://m.komovie.cn/app/activityList", name:"首页"},{tab:"1", link:"komovie://kotaPager", name:"约电影"},{tab:"2", link:"http://m.komovie.cn/app/appActivityList", name:"热门活动"}], function() {});
                //bridge.callHandler('getTabs', [{tab:"0", link:"http://m.komovie.cn/app/activityList", name:"热门活动"},{tab:"1", link:"komovie://kotaPager", name:"约电影"},{tab:"2", link:"http://club.komovie.cn/forum.php?forumlist=1&mobile=2", name:"社区"},{tab:"3", link:"http://club.komovie.cn/forum.php?mod=guide&view=new&mobile=2", name:"首页"}], function() {});
            }else{
                bridge.callHandler('getTabs', [{tab:"0", link:"http://m.komovie.cn/app/activityList", name:"首页"},{tab:"1", link:"komovie://kotaPager", name:"约电影"},{tab:"2", link:"http://m.komovie.cn/app/appActivityList", name:"热门活动"}], function() {});
                //bridge.callHandler('getTabs', [{tab:"0", link:"http://m.komovie.cn/app/activityList", name:"热门活动"},{tab:"1", link:"komovie://kotaPager", name:"约电影"},{tab:"2", link:"http://club.komovie.cn/forum.php?mod=guide&view=new&mobile=2", name:"社区"}], function() {});
            }


            //单击活动跳到详情页面
            if (version <= 5.20) {
                $(".activityDetail").click(function () {
                    var id = $(this).attr("activity_id");
                    if (id == '123456') {
                        bridge.callHandler('recordDetail', {record_id: 1}, function () {});
                    } else {
                        if (navigator.userAgent.match(/komovie_([^_]+)_a/)) {  //安卓处理
                            window.location="http://m.komovie.cn/sub/client?activity_id="+id;
                        }else{   //苹果处理
                            bridge.callHandler('activityDetail', {activity_id: id}, function () {});
                        }

                    }
                });
            }


            $("#sub_117").click(function(){
                bridge.callHandler('recordDetail', {record_id:1}, function() {});
            });
        })
    }
}
```