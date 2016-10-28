---
title: JavaScript高级教程(上篇)
date: 2016-10-18 13:30:00 +08:00
categories:
- JavaScript
layout: post
author: The actor
---

**学习要点：**

1. 函数
2. 对象和数组
3. 时间与日期

## 1. 函数学习 Function

函数是**定义一次但却可以调用或执行任意多次的一段JS代码。**

函数有时会有参数，即函数被调用时指定了值的局部变量。函数常常使用这些参数来计算一个返回值，这个值也成为函数调用表达式的值。

### 1.1 函数声明

函数对任何语言来说都是一个核心的概念。通过函数可以封装任意多条语句，而且可以在任何地方、任何时候调用执行。

ECMAScript 中的函数使用 `function` 关键字来声明，后跟一组参数以及函数体。

```javascript
function box() {
    //没有参数的函数 alert('只有函数被调用，我才会被之执行 ');
}
box();

function box(name, age) {
    alert('你的姓名:'+name+',年龄:'+age);
}
box('The actor',23);
```

### 1.3 return返回值

带参和不带参的函数，都没有定义返回值，而是调用后直接执行的。实际上，任何函数都可以通过 return 语句跟后面的要返回的值来实现返回值。

```javascript
//没有参数的函数
function box() {
    return '我被返回了!';
}
alert(box());

//有参数的函数
function box(name, age) {
    return '你的姓名:' + name + ', 年龄:' + age; //通过 return 把函数的最终值返回
}
alert(box('李炎恢', 28)); //调用函数得到返回值，然后外面输出

//我们还可以把函数的返回值赋给一个变量，然后通过变量进行操作。
function box(num1, num2) {
    return num1 * num2;
}
var num = box(10, 5); //函数得到的返回值赋给变量
alert(num);
```

return 语句还有一个功能就是退出当前函数，注意和 **break** 的区别。

PS: break 用在循环和 switch 分支语句里。

```javascript
function box(num) {
    if (num < 5) return num;
    return 100;
}
alert(box(10));
```

### 1.4 arguments 对象

ECMAScript 函数不介意传递进来多少参数，也不会因为参数不统一而错误。实际上，函数体内可以通过 arguments 对象来接收传递进来的参数。

```javascript
function box() {
    return arguments[0] + ' | ' + arguments[1]; //得到每次参数的值
}
alert(box(1, 2, 3, 4, 5, 6)); //传递参数
```

arguments 对象的 length 属性可以得到参数的数量。

```javascript
function box() {
    return arguments.length; //得到 6
}
alert(box(1, 2, 3, 4, 5, 6));
```

我们可以利用 `length` 这个属性，来智能的判断有多少参数，然后把参数进行合理的应用。比如，要实现一个加法运算，将所有传进来的数字累加，而数字的个数又不确定。

```javascript
function box() {
    var sum = 0;
    if (arguments.length == 0) return sum; //如果没有参数，退出

    for(var i = 0; i < arguments.length; i++) {
        sum = sum + arguments[i]; //如果有，就累加
    }

    //返回累加结果
    return sum;
}
alert(box(5, 9, 12));
```

**注意：ECMAScript 中的函数，没有像其他高级语言那种函数重载功能。**

```javascript
function box(num) {
    return num + 100;
}

function box (num) { //会执行这个函数
    return num + 200;
}
alert(box(50)); //返回结果
```

## 2. 对象和数组

什么是对象，其实就是一种类型，即引用类型。而对象的值就是引用类型的实例。

在 ECMAScript 中引用类型是一种数据结构，用于将数据和功能组织在一起。它也常被称做为类，但 ECMAScript 中却没有这种东西。

**虽然 ECMAScript 是一门面向对象的语言，却不具备传统面向对象语言所支持的类和接口等基本结构。**

### 2.1 Object类型

创建 Object 类型有两种。一种是使用 `new` 运算符，一种是字面量表示法。

```javascript
//1. 使用 new 运算符创建
Object var box = new Object();
box.name = 'The actor';
box.age = 23;

//2. new 关键字可以省略
var box = Object();

//3. 使用字面量方式创建
Object var box = {
    name: '李炎恢',
    age: 28
};
//PS: 在使用字面量声明 Object 对象时，不会调用 Object() 构造函数(Firefox 除外)。

//4. 属性字段也可以使用字符串
var box = {
    'name': 'The actor',
    'age': 23
};

//5. 使用字面量及传统赋值方式
var box = {}; //字面量方式声明空的对象
box.name = 'The actor'; //点符号给属性赋值
box.age = 23;

//6. 两种属性输出方式
alert(box.age); //点表示法输出
alert(box['age']); //中括号表示法输出，注意引号

//7. 给对象创建方法
var box = {
    run: function () {
        return '运行';
    }
}
alert(box.run()); //调用方法

//8. 使用delete删除对象属性
delete box.name; //删除属性

//在实际开发过程中，一般我们更加喜欢字面量的声明方式。
//因为字面量清晰、语法代码少，而且还给人一种封装的感觉。
//字面量也是向函数传递大量可选参数的首选方式。
function box(obj) {
    if (obj.name != undefined) alert(obj.name);
    if (obj.age != undefined) alert(obj.age);
}
box({
    name: 'The actor',
    age: 23
});
```

### 2.2 Array类型

ECMAScript 中的 Array 类型和其他语言中的数组有着很大的区别。虽然数组都是有序排列，但：

1. ECMAScript 中的数组每个元素可以保存任何类型。ECMAScript 中数组的大小也是可以调整的。
2. 创建 Array 类型有两种方式：第一种是 new 运算符，第二种是字面量。

```javascript
//1. 使用 new 关键字创建数组
var box = new Array();  
var box = new Array(10);
var box = new Array('The actor', 23, 'iOS工程师', '沧州'); //创建一个数组并分配好元素

//2. 以上三种方法，可以省略 new 关键字。
var box = Array();

//3. 使用字面量方式创建数组
var box = [];
var box = ['李炎恢', 28, '教师', '盐城']; //创建包含元素的数组
var box = [1, 2, ]; //禁止这么做，IE 会识别 3 个元素
var box = [,,,,,];    

//PS: 和 Object 一样，字面量的写法不会调用 Array() 构造函数。(Firefox 除外)。

//4. 使用索引下标来读取数组的值
alert(box[2]);
box[2] = '学生';
box[4] = '计算机编程';

//5. 使用 length 属性获取数组元素量
alert(box.length)
box.length = 10;
box[box.length] = 'JS 技术';

//6. 创建一个稍微复杂一点的数组
var box=[
    {
        name: '任智超',
        age: 23,
        run: function(){
            return 'run了';
        }
    },
    ['马云', '任智超', new Object()],
    '马化腾',
    24 + 25,
    new Array(1, 2, 3)
];
alert(box);
```

**PS: 数组最多可包含 4294967295 个元素，超出即会发生异常。**

### 2.3 对象中的方法

#### 转换方法：

对象或数组都具有 `toLocaleString()`、`toString()` 和 `valueOf()` 方法。其中 toString() 和 valueOf() 无论重写了谁，都会返回相同的值。数组会将每个值进行字符串形式的拼接，以逗号隔开。

```javascript
var box = ['The actor', 23, '计算机编程'];
alert(box); //隐式调用了 toString()
alert(box.toString()); //和 valueOf() 返回一致
alert(box.toLocaleString()); //返回值和上面两种一致
```

默认情况下，数组字符串都会以逗号隔开。如果使用 `join()` 方法，则可以使用不同的分隔符来构建这个字符串。

```javascript
var box = ['The actor', 23, '计算机编程'];
alert(box.join('|')); //The actor|23|计算机编程
```

#### 栈方法：

ECMAScript 数组提供了一种让数组的行为类似于其他数据结构的方法。也就是说，可以让数组像栈一样:

**可以限制插入和删除项的数据结构。栈是一种数据结构（后进先出），也就是说最新添加的元素最早被移除。**

**而栈中元素的插入（或叫推入）和移除（或叫弹出），只发生在一个位置 —— 栈的顶部。**

ECMAScript 为数组专门提供了 `push()` 和 `pop()` 方法。

`push()` 方法可以接收任意数量的参数，把它们逐个添加到数组的末尾，并返回修改后数组的长度。
`pop()` 方法则从数组末尾移除最后一个元素，减少数组的 length 值，然后返回移除的元素。

```javascript
var box = ['The actor', 23, '计算机编程'];
alert(box.push('沧州')); //数组末尾添加一个元素，并且返回长度
alert(box); //查看数组
box.pop(); //移除数组末尾元素，并返回移除的元素
alert(box);  
```

#### 队列方法：

栈方法是后进先出，而列队方法就是先进先出。列队在数组的末端添加元素，从数组的前端移除元素。通过 `push()` 向数组末端添加一个元素，然后通过 `shift()` 方法从数组前端移除一个元素。

```javascript
var box = ['The actor', 23, '计算机编程'];
alert(box.push('沧州')); //数组末尾添加一个元素，并且返回长度
alert(box); //查看数组
box.shift(); //移除数组开头元素，并返回移除的元素
alert(box);
```

ECMAScript 还为数组提供了一个 `unshift()` 方法，它和 `shift()` 方法的功能完全相反。

**unshift() 方法为数组的开头添加一个元素**

```javascript
var box = ['The actor', 23, '计算机编程'];
alert(box.unshift('河北', '沧州')); //数组开头添加两个元素
alert(box); //查看数组
box.pop(); //移除数组末尾元素，并返回移除的元素
alert(box);
```

**PS: IE 浏览器对 unshift() 方法总是返回 undefined 而不是数组的新长度。**

#### 重排序方法：

数组中已经存在两个可以直接用来排序的方法: **reverse() 和 sort()。**

`reverse()`：逆向排序

```javascript
var box = [1, 2, 3, 4, 5];
alert(box.reverse());
alert(box); //源数组也被逆向排序了，说明是引用
```

`sort()`：正序

```javascript
var box = [4, 1, 7, 3, 9, 2];
alert(box.sort());
alert(box);
```

sort 方法的默认排序在数字排序上有些问题，因为数字排序和数字字符串排序的算法是一样的。我们必须修改这一特征，修改的方式就是给 `sort(参数)` 方法传递一个函数参数。这点可以参考手册说明。

```javascript
function compare(value1, value2) { //数字排序的函数参数
   if (value1 < value2) {
      return -1; //小于，返回负数
   } else if (value1 > value2) {
      return 1; //大于，返回正数
   } else {
      return 0;
   }
}
var box = [0, 1, 5, 10, 15]; //验证数字字符串
alert(box.sort(compare)); //传参，数字个数>=2位的时候必须
```

PS: 如果要反向操作，即从大到小排序，正负颠倒即可。当然，如果要逆序用 `reverse()` 更加方便。

#### 操作方法：

ECMAScript 为操作已经包含在数组中的元素提供了很多方法。

`concat()` 方法可以基于当前数组创建一个新数组：

```javascript
var box = ['The actor', 23, '沧州'];  
var box2 = box.concat('计算机编程'); //创建新数组，并添加新元素
alert(box2);
alert(box);
```

`slice()` 方法可以基于当前数组获取指定区域元素并创建一个新数组：

```javascript
var box = ['The actor', 23, '沧州'];
var box2 = box.slice(1); //box.slice(1,3), 2-4 之间的元素  
alert(box2); //23, 沧州
alert(box);
```

`splice()` 主要用途是向数组的中部插入元素：

```javascript
//splice 中的删除功能
var box = ['The actor', 23, '沧州'];
var box2 = box.splice(0,2); //截取前两个元素
alert(box2); //返回截取的元素
alert(box); //当前数组被截取的元素被删除

//splice 中的插入功能
var box = ['The actor', 23, '沧州'];
//没有截取，但插入了两条因为截取长度 length 为 0
var box2 = box.splice(1, 0, '计算机编程', '河北');
alert(box2); //在第 2 个位置插入两条
alert(box); //The actor, 计算机编程, 河北, 23, 沧州

//splice 中的替换功能
var box = ['The actor', 23, '沧州'];
var box2 = box.splice(1, 1, 100); //截取了第 2 条，替换成 100
alert(box2); //输出截取的 23
alert(box); //输出数组
```

## 3. 时间和日期

ECMAScript 提供了 Date 类型来处理时间和日期。Date 类型内置一系列获取和设置日期时间信息的方法。

### 3.1 Date类型

ECMAScript 中的 Date 类型是在早期 Java 中 `java.util.Date` 类基础上构建的。为此，Date 类型使用 UTC (Coordinated Universal Time，国际协调时间[又称世界统一时间]) 1970年1月1日午夜(零时)开始经过的毫秒来保存日期。

**在使用这种数据存储格式的条件下，Date 类型保存的日期能够精确到 1970年1月1日之前或之后的 285616年。**

```javascript
var box = new Date(); //创建一个日期对象
alert(box); //不同浏览器显示不同
```

PS：在调用 Date 构造方法而不传递参数的情况下，新建的对象自动获取当前的时间和日期。

ECMAScript 提供了两个方法：`Date.parse()` 和 `Date.UTC()`。

`Date.parse()` 方法接收一个表示日期的字符串参数，然后尝试根据这个字符串返回相应的毫秒数。

默认通常接收的日期格式如下:

```javascript
//1. '月/日/年'，如 6/13/2011;
//2. '英文月名 日, 年'，如 May 25, 2004;
//3. '英文星期几 英文月名 日 年 时:分:秒 时区'，如 Tue May 25 2004 00:00:00 GMT-070
alert(Date.parse('6/13/2011')); //1307894400000
```

如果 Date.parse() 没有传入或者不是标准的日期格式，那么就会返回 NaN。

```javascript
alert(Date.parse()); //NaN
```

如果想输出指定的日期，那么把 Date.parse() 传入 Date 构造方法里。

```javascript
//Mon Jun 13 2011 00:00:00 GMT+0800
var box = new Date(Date.parse('6/13/2011'));

//直接传入，Date.parse() 后台被调用
var box = new Date('6/13/2011');
```

### 3.2 通用的方法

与其他类型一样，Date 类型也重写了 `toLocaleString()`、`toString()` 和 `valueOf()` 方法，但这些方法返回值与其他类型中的方法不同。

```javascript
var box = new Date(Date.UTC(2011, 11, 5, 15, 13, 16));
alert('toString: ' + box.toString());

//按本地格式输出
alert('toLocaleString: ' + box.toLocaleString());
```

PS: 这两个方法在不同浏览器显示的效果又不一样，但不用担心，这两个方法只是在调试比较有用，在显示时间和日期上没什么价值。`valueOf()` 方法显示毫秒数。

### 3.3 格式化方法

Date 类型还有一些专门用于将日期格式化为字符串的方法。

```javascript
var box = new Date();
alert(box.toDateString()); //以特定的格式显示星期几、月、日和年
alert(box.toTimeString()); //以特定的格式显示时、分、秒和时区
alert(box.toLocaleDateString()); //以特定地区格式显示星期几、月、日和年
alert(box.toLocaleTimeString()); //以特定地区格式显示时、分、秒和时区
alert(box.toUTCString()); //以特定的格式显示完整的 UTC 日期。
```

### 3.4 组件方法

组件方法，是为我们单独获取你想要的各种时间/日期而提供的方法。

需要注意的是，这些方法中，有带 UTC 的，有不带 UTC 的。UTC 日期指的是在没有时区偏差的情况下的日期值。

```javascript
alert(box.getTime());           //获取日期的毫秒数,和 valueOf()返回一致
alert(box.setTime(100));        //以毫秒数设置日期,会改变整个日期
alert(box.getFullYear());       //获取四位年份
alert(box.setFullYear(2012));   //设置四位年份,返回的是毫秒数
alert(box.getMonth());          //获取月份,没指定月份,从 0 开始算起
alert(box.setMonth(11));        //设置月份
alert(box.getDate());           //获取日期
alert(box.setDate(8));          //设置日期,返回毫秒数
alert(box.getDay());            //返回星期几,0 表示星期日,6 表示星期六
alert(box.setDay(2));           //设置星期几
alert(box.getHours());          //返回时  
alert(box.setHours(12));        //设置时
alert(box.getMinutes());        //返回分钟
alert(box.setMinutes(22));      //设置分钟
alert(box.getSeconds());        //返回秒数
alert(box.setSeconds(44));      //设置秒数
alert(box.getMilliseconds());   //返回毫秒数
alert(box.setMilliseconds());   //设置毫秒数
alert(box.getTimezoneOffset()); //返回本地时间和 UTC 时间相差的分钟数
```

PS: 以上方法除了 `getTimezoneOffset()`，其他都具有 UTC 功能，例如 `setDate()` 及 `getDate()` 获取星期几，那么就会有 `setUTCDate()` 及 `getUTCDate()`。表示世界协调时间。

#### 后续更多高级教程更新：

1. 正则表达式
2. Function类型
3. 变量、作用域及内存
4. 基本包装类型
5. js中的内置对象
6. 面向对象与原型
7. 匿名函数和闭包详解

等等等等......
