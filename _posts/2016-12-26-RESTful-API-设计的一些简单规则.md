---
title: RESTful API 设计的一些简单规则
date: 2016-12-26 11:16:13
tags: RESTful
---

这里我收集了一些约定（原则），用来帮助更好地使用 RESTful API。

这些原则都是基于个人的有限经验总结出来的，在这儿请求结果为JSON。

### 返回正确的HTTP状态码

一个好的API应该永远为每个请求返回合适的HTTP状态码（status codes），这样每个请求只需通过状态码来判断本次请求是否成功或有效，而不是其他的参数。

### 如果某字段没有值，应该返回null

没有值的`numbers`, `strings`, `booleans`在返回结果中值为`null`，如果出于业务考虑，为某参数不能存在`null`，而需要一个默认参数，也不能在API层设置，应在数据库中设置默认值。

注意：数组不可以为`null`。如果某个参数时一个列表，即使列表数量为0，也应返回空列表。这样前端对列表的处理将会简单很多。

例子：

```
{
    "id": 11111,
    "name": "Albert",
    "age": null,
    "relatives": [], // no relatives
    "address": null
}
```
###  boolean类型的处理

不要使用`0`和`1`，也不要使用`“0”`和`“1”`，也不要尝试其他的方案。使用`true`和`false`。

### 正确设置HEAD信息

使用HTTP相关的`HEAD`信息，例如，请求结果应该有正确的`Content-Type`，像`application/json`。

### 参考 

将RESTful API理念实现最好的莫过于[twitter](https://dev.twitter.com/rest/public)了。




