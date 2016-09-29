---
layout: post
title:  "2. javaScript基本语法进阶"
date:   2016-09-29 11:39:00
author: 任智超
categories: JavaScript
---

## 二. javaScript基本语法进阶

主篇涉及学习点：

1. 运算符
 
2. 流程控制

3. js中的函数


### 5 运算符详解

`ECMA-262` 描述了一组用于操作数据值的运算符 ,包括

```
一元运算符
布尔运算符
算术运算符
关系运算符
三元运算符
位运算符
赋值运算符 
```

ECMAScript 中的运算符适用于很多值，包括字符串、数值、布尔值、对象等。不过，通过上一章我们也了解到，应用于对象时通常会调用对象的 **valueOf()和 toString()方法**，以便取得相应的值。

PS:前面的章节我们讲过 typeof 操作符、new 操作符，也可以称之为 typeof 运算符、new 运算符，是同一个意思。

#### 5.1 什么是表达式

表达式是 ECMAScript 中的一个“短语”，解释器会通过计算把它转换成一个值。最简单的表达式是**字面量或者变量名**。例如:

```
5.96          //数值字面量
'任智超'       //字符串字面量
true          //布尔值字面量
null          //空值字面量 
/JavaScript/  //正则表达式字面量
{x:1, y:2}    //对象字面量、对象表达式
[1,2,3]       //数组字面量、数组表达式
function(n) {return x+y;}   //函数字面量、函数表达式
box           //变量
```

当然，还可以通过合并简单的表达式来创建复杂的表达式。比如:

```
box + 5.96    //加法运算的表达式
typeof(box)   //查看数据类型的表达式
box > 8       //逻辑运算表达式
```
通过上面的叙述，我们得知，**单一的字面量和组合字面量的运算符**都可称为表达式。

#### 5.2 一元运算符

只能操作一个值的运算符叫做一元运算符。

##### 5.2.1 递增++和递减--

```
var box = 100;
++box;     //把 box 累加一个 1,相当于 box = box+1 
--box;     //把 box 累减一个 1,相当于 box = box-1 
box++;     //同上
box--;     //同上
```

##### 5.2.2 前置和后置的区别
在没有赋值操作 ,前置和后置是一样的 。但在赋值操作时 ,如果递增或递减运算符前置 , 那么前置的运算符会先累加或累减再赋值,如果是后置运算符则先赋值再累加或累减。

```
var box = 100;
var age = ++box;     //age 值为 101
var height = box++;  //height 值为 100
```

##### 5.2.3 其他类型应用一元运算符的规则

``` 
var box = '89'; box++;  //90,数值字符串自动转换成数值
var box = 'ab'; box++;  //NaN,字符串包含非数值转成 NaN
var box = false; box++;  //1,false 转成数值是0,累加就是 1
var box = 2.3; box++;     //3.3,直接加 1
```

```
var box = {     //1,不设置 toString 或 valueOf 即为 NaN
     toString : function() { return 1;
}
    box++;
};
```

##### 5.2.4 加运算规则如下: 

```
var box = 100; +box;    //100,对于数值,不会产生任何影响
var box = '89'; +box;   //89,数值字符串转换成数值
var box = 'ab'; +box;   //NaN,字符串包含非数值转成 NaN
var box = false; +box;  //0,布尔值转换成相应数值
var box = 2.3; +box;    //2.3,没有变化

var box = {    //1,不设置 toString 或 valueOf 即为 NaN
    toString : function() { return 1;   
    };
};

+box;
```

##### 5.2.5 减运算规则如下: 

```
var box = 100; -box;      //-100,对于数值,直接变负
var box = '89'; -box;     //-89,数值字符串转换成数值
var box = 'ab'; -box;     //NaN,字符串包含非数值转成 NaN
var box = false; -box;     //0,布尔值转换成相应数值
var box = 2.3; -box;       //-2.3

var box = {    //-1,不设置 toString 或 valueOf 即为 NaN
    toString : function() { return 1;
    }; 
};
-box;
```

加法和减法运算符一般用于算术运算，**也可向上面进行类型转换**。

##### 5.2.6 算数运算符

ECMAScript 定义了 5 个算术运算符:

**加减乘除求模 (取余)**。如果在算术运算的值不是数值，那么后台会先使用 Number() 转型函数将其转换为数值(隐式转换)。

加法、减法、乘法、除法没啥可说的了，但是有些细节方面大家需要注意，这里不做阐述。

重点说下 **求模**，这里只说下JS里特有的保留字面量

```
var box = 100 % NaN;    //NaN
var box = Infinity % Infinity;   //NaN
var box = -Infinity % Infinity;   //NaN
var box = -Infinity % -Infinity;   //NaN

varbox=100 % 对象;   //NaN,如果有 toString()或 valueOf()则返回 10% 返回数的值
```

#### 5.3 关系运算符

用于进行比较的运算符称作为关系运算符:

**小于 (<)、大于(>)、
小于等于(<=)、大于等于(>=)、相等(==)、不等(!=)、全等(恒等)(===)、不全等(不恒等)(!==)**

和其他运算符一样，当关系运算符操作非数值时要遵循一下规则: 

```
1.两个操作数都是数值,则数值比较; 
2.两个操作数都是字符串,则比较两个字符串对应的字符编码值; 
3.两个操作数有一个是数值,则将另一个转换为数值,再进行数值比较; 
4.两个操作数有一个是对象,则先调用 valueOf()方法或 toString()方法,再用结果比较;
```

var box = 3 > 2;    //true
var box = 3 > 22;   //false
var box = '3' > 22;   //false   
var box = '3' > '22';  //true  比较的是数值字符串的第一个字符
var box = 'a' > 'b';  //false
var box = 'a' > 'B';  //true B=66
var box = 1 > 对象;    //false,如果有 toString()或 valueOf() 则返回 1 > 返回数的值

在相等和不等的比较上，如果操作数是非数值，则遵循一下规则: 

```
1.一个操作数是布尔值,则比较之前将其转换为数值, false 转成 0,true 转成 1; 
2.一个操作数是字符串,则比较之前将其转成为数值再比较; 
3.一个操作数是对象,则先调用 valueOf()或 toString()方法后再和返回值比较; 
4.不需要任何转换的情况下, null 和 undefined 是相等的;
5.一个操作数是 NaN,则==返回 false,!=返回 true;并且 NaN 和自身不等;
6.两个操作数都是对象 ,则比较他们是否是同一个对象 ,如果都指向同一个对象 ,则返 回 true,否则返回 false。
7.在全等和全不等的判断上,比如值和类型都相等,才返回 true,否则返回 false。
```

```
var box = 2 == 2;       //true
var box = '2' == 2;     //true,'2'会转成成数值 2
var box = false == 0;   //true,false 转成数值就是 0
var box = 'a' == 'A';   //false,转换后的编码不一样
var box = 2 == {};      //false,执行 toString()或 valueOf()会改变
var box = 2 == NaN;     //false,只要有 NaN,都是 false  
var box = {} == {};    //false,比较的是他们的地址,每个新创建对象的引用不同

var age = {};          
var height = age;
var box = age == height;   //true,引用地址一样,所以相等
var box = '2' === 2        //false,值和类型都必须相等
var box = 2 !== 2          //false,值和类型都相等了
```

特殊值对比表

```
null == undefined    true
'NaN' == NaN   false
5 == NaN     false
NaN == NaN    false
false == 0    true
true == 1    true
true == 2     false
undefined == 0  false
null == 0   false
'100' == 100   true
'100' === 100    false
```

#### 5.4 逻辑运算符
逻辑运算符通常用于布尔值的操作,一般和关系运算符配合使用,有三个逻辑运算符 :
逻辑与(AND)、逻辑或(OR)、逻辑非(NOT)。

1.   && 与
2.   || 或
3.   !  非

没啥可说的，任何语言都有这三个预算符,这里不再多说。

#### 5.5 *位运算符*

在一般的应用中,我们基本上用不到位运算符。

**虽然,它比较基于底层,性能和速度会非常好,而就是因为比较底层,使用的难度也很大。**所以,我们我们如果不做框架级的开发，不用理会它。 位运算符有七种,分别是:  **位非 NOT(~)、位与 AND(&)、位或 OR(|)、位异或 XOR(^)、左移(<<)、有符号右移(>>)、无符号右移(>>>)。**

```
var box = ~25;     //-26 
var box = 25 & 3;  //1 
var box = 25 | 3;  //27 
var box = 25 << 3; //200
var box = 25 >> 2; //6
var box = 25 >>> 2; //6
```

#### 5.6 赋值运算符

以下是特殊的几种表现形式，普通的不再多说。

```
1.乘/赋(*=)
2.除/赋(/=)
3.模/赋(%=)
4.加/赋(+=)
5.减/赋(-=) 
6.左移/赋(<<=) 
7.有符号右移/赋(>>=) 
8.无符号有移/赋(>>>=)
```

#### 5.7 其它运算符

##### 5.7.1 字符串运算符

字符串运算符只有一个，即: "+"。它的作用是将两个字符串相加。 

规则：至少一个操作数是字符串即可。

```
var box = '100' + '100';    //100100   本质是拼接
var box = '100' + 100;      //100100
var box = 100 + 100;        //200
```
 
##### 5.7.2 逗号运算符

逗号运算符可以在一条语句中执行多个操作。 

```
varbox=100,age=20,height=178;   //多个变量声明
var box = (1,2,3,4,5);       //5,变量声明,将最后一个值赋给变量,不常用
var box = [1,2,3,4,5];        //[1,2,3,4,5],数组的字面量声明
var box = {                  [object Object],对象的字面量声明 js里的表现形式是 json数据
    1:2,
    3:4, 
    5:6
  };
}
```

##### 5.7.3 三元条件运算符

三元条件运算符其实就是后面将要学到的 if 语句的简写形式。

var box = 5 > 4 ? '对' : '错';   //对,5>4 返回 true 则把'对'赋值给 box,反之。
相当于:
var box = '';     //初始化变量

if (5 > 4) {      //判断表达式返回值
    box = '对'; 
}else{
    box = '错';

九.运算符优先级
//对,5>4 返回 true 则把'对'赋值给 box,反之。

#### 5.8 运算符优先级

没啥可说的，任何语言都一样

### 6 流程控制语句

任何语言都有这个模块，没啥可说的，这里主要把关键字列出来

```
1.语句的定义
2.if 语句
3.switch 语句 
4.do...while 语句 
5.while 语句
6.for 语句
7.for...in 语句
8.break 和 continue 语句 
9.with 语句
```

语句的种类：

循环语句

```
      for       for(;;;){}
      
      for in    for(x in x){}
      
      while     while() {};
      
      do.. while  do{} while();
      
      switch语句   switch()  {case 1: ...  break; ...}
```
      
控制结构

```
      继续执行子句    continue;
      中断之行       break;
      函数返回       return;
      异常触发       throw;
      异常捕获和处理  try {}  catch() {}   finally{}
```

其它

```   
      空语句    ;
      with语句   with()   {}   
```


with语句介绍及使用

with 语句的作用是将代码的作用域设置到一个特定的对象中。


```
var box = {
    'name' : '李炎恢',
    'age' : 28,
    'height' : 178 
    };
    
var n = box.name; 
var a = box.age; 
var h = box.height;

可以将上面的三段赋值操作改写成:
with (box) {         //省略了 box 对象名
      var n = name;
      var a = age;
      var h = height; 
}

```

### 7 函数介绍及实战

函数是定义一次但却可以调用或执行任意多次的一段 JS 代码。

函数有时会有参数,即 函数被调用时指定了值的局部变量。

函数常常使用这些参数来计算一个返回值 ,这个值也成 为函数调用表达式的值。

#### 7.1 函数的声明

函数对任何语言来说都是一个核心的概念 。**通过函数可以封装任意多条语句 ,而且可以在任何地方、任何时候调用执行。** 

ECMAScript 中的函数使用 `function` 关键字来声明,后跟一组参数以及函数体。

```
function box() { //没有参数的函数 
         alert('只有函数被调用,我才会被之执行 ');
} 

box();   //调用函数


function box(name, age) { 
         alert('你的姓名:'+name+',年龄:'+age);
} 

box('任智超',23);  
```

#### 7.2 return返回值

带参和不带参的函数 ,都没有定义返回值 ,而是调用后直接执行的 。实际上,**任何函数 都可以通过 return 语句跟后面的要返回的值来实现返回值。**

```
function box() {    //没有参数的函数
      return '我被返回了!';   //通过 return 把函数的最终值返回
} 

alert(box());   //调用函数会得到返回值 ,然后外面输出
```


```
function box(name, age) {    //有参数的函数
     return '你的姓名:'+name+',年龄:'+age;   //通过 return 把函数的最终值返回
}
alert(box('任智超', 23)); //调用函数得到返回值,然后外面输出
```

我们还可以把函数的返回值赋给一个变量,然后通过变量进行操作。

```
function box(num1, num2) { 
      return num1 * num2;
}
var num = box(10, 5);    //函数得到的返回值赋给变量 alert(num);
```

return 语句还有一个功能就是退出当前函数 ,注意和 break 的区别。PS: **break 用在循环 和 switch 分支语句里。**

```
function box(num) {
      if (num < 5) return num;     //满足条件,就返回 num
      return 100;                  //返回之后,就不执行下面的语句了
} 

alert(box(10));
```

#### 7.3 arguments对象
ECMAScript函数不介意传递进来多少参数,也不会因为参数不统一而错误。实际上, **函数体内可以通过 arguments对象来接收传递进来的参数。**

```
function box() {
      return arguments[0]+' | '+arguments[1];   //得到每次参数的值
}

alert(box(1,2,3,4,5,6));   //传递参数
```

arguments 对象的 length 属性可以得到参数的数量。 

```
function box() {
       return arguments.length; //得到 6 
}

alert(box(1,2,3,4,5,6));
```

我们可以利用 length 这个属性,来智能的判断有多少参数 ,然后把参数进行合理的应用 。 

比如,要实现一个加法运算,将所有传进来的数字累加,而数字的个数又不确定。

```
function box() { 

   var sum = 0;

   if (arguments.length == 0) return sum;    //如果没有参数,退出 
   
   for(var i = 0;i < arguments.length; i++) {
             sum = sum + arguments[i];    //如果有,就累加
   }

   return sum;
}

 alert(box(5,9,12))   
```

**ECMAScript中的函数,没有像其他高级语言那种函数重载功能。**

```
function box(num) {
     return num + 100; 
}

function box (num) {       //会执行这个函数
     return num + 200;      
} 

alert(box(50));            //返回结果
```

**后续会更新js更多高级的使用，如面向对象、数组、时间与日期、表达式、函数类型等等......**
