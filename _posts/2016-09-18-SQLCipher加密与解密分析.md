---
title: SQLCipher加密与解密分析
date: 2016-09-18 23:47:50 +08:00
categories:
- Android
layout: post
author: Darre
---

## 前言

我们知道Android系统有一个内嵌的SQLite数据库，它经常被用来存储一些Server端的数据，其中也会包含一些敏感的信息，为了保证信息的安全性，我们需要对数据进行加密，通常的做法是直接通过MD5对数据进行加密。但是数据量一旦过大开销就消耗过大，性能上就需要得到提升。这里推荐使用SQLCipher来解决数据库加密问题。它是一个第三方加密开源库。

SQLCiper是一个在SQLite基础之上进行扩展的开源加密库，它占用面积小，性能高，因此比较适合嵌入式应用中。SQLCipher主要对数据库文件进行加密。

**Features：**

> Fast performance with as little as 5-15% overhead for encryption on many operations
>
> 100% of data in the database file is encrypted
>
> Good security practices (CBC mode, key derivation)
>
> Zero-configuration and application level cryptography
>
> Algorithms provided by the peer reviewed OpenSSL crypto library.
>
> Configurable crypto providers

上面是官方的解释，简单的总结一下：

1. 加密性能高，开销小，只要5%-15%的开销用于加密
2. 完全做到数据库的100%加密
3. 采用良好的加密方式（CBC加密模式）
4. 使用方便，做到应用级别加密
5. 采用OpenSSL加密库提供的算法

下面开始介绍SQLCipher加密

## SQLCipher加密篇

SQLCipher使用很简单，上面说道它是在SQLite进行扩展的开源加密库，所以用法和SQLite是是一样的，只是需要注意import导入不同的包而已。

1、 首先进入[https://github.com/sqlcipher](https://github.com/sqlcipher)下载sqlcipher-android-tests，官方下载地址：[https://github.com/sqlcipher/sqlcipher-android-tests](https://github.com/sqlcipher/sqlcipher-android-tests)。

2、 下载后的项目导入，并将libs下面的文件移植到你的项目中

![sqlcipher-android-tests]({{ site.baseurl }}/assets/posts/2016-09-18-sqlcipher-android-tests.png)

3、 这里我用AndroidStudio新建一个工程

![Android Studio项目]({{ site.baseurl }}/assets/posts/2016-09-18-android_studio.png)

4、 创建自己的数据库

```java
package com.kokozu.sqlitecipherdemo;

import android.content.Context;
import net.sqlcipher.database.SQLiteDatabase;
import net.sqlcipher.database.SQLiteOpenHelper;

/**
 * Created by hao on 2016/9/13.
 */
public class SqliteDataBaseHelper extends SQLiteOpenHelper {

    //创建Test数据库表的sql语句
    public static final String CREATE_TABLE =
            "create table Test(userName text, password integer)";

    public SqliteDataBaseHelper(Context context, String name,
            SQLiteDatabase.CursorFactory factory, int version) {
        super(context, name, factory, version);
    }

    @Override
    public void onCreate(SQLiteDatabase sqLiteDatabase) {
        //创建数据表
        sqLiteDatabase.execSQL(CREATE_TABLE);
    }

    @Override
    public void onUpgrade(SQLiteDatabase sqLiteDatabase, int i, int i1) {
    }
}
```

上面的代码相信大家很熟悉了，就不多解释和SQLite的用法是一样的，注意的是导入的包名

那么我们如何对数据库进行加密呢？

`db = sqlHelper.getWritableDatabase("123456");`这一句就是添加加密秘钥，参数是字符串，如果为""表示不加密，但是不能有逗号，否则就无法加密。需要注意的是：这里我使用SqliteDataBaseHelper继承SQLiteOpenHelper,如果想使用多个类似继承SqliteDataBaseHelper的工具类，那么如果加密秘钥相同可以写入相同的数据库，如果不同则不能写入相同的数据库，也就是说一个数据库只能设置一个加密秘钥不能设置多个不同的秘钥，所以如果使用多个类似的SqliteDataBaseHelper类，应该分开在不同的数据库，或者合并这些类。

完整代码如下：

```java
package com.kokozu.sqlitecipherdemo;

import android.app.Activity;
import android.content.ContentValues;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;

import com.getkeepsafe.relinker.ReLinker;

import net.sqlcipher.Cursor;
import net.sqlcipher.database.SQLiteDatabase;

public class MainActivity extends Activity implements View.OnClickListener{

    private Button btn_insert;
    private Button btn_query;
    private SQLiteDatabase db;
    private SqliteDataBaseHelper sqlHelper;
    private static final String TAG = "MainActivity";

    private ReLinker.Logger logcatLogger = new ReLinker.Logger() {

        @Override
        public void log(String message) {
            Log.d("ReLinker", message);
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initData();
        initView();
    }

    private void initView(){
        btn_insert = (Button) findViewById(R.id.btn_insert);
        btn_query = (Button) findViewById(R.id.btn_query);
        btn_insert.setOnClickListener(this);
        btn_query.setOnClickListener(this);
    }

    private void initData(){
        try {
            SQLiteDatabase.loadLibs(this);
        }catch (UnsatisfiedLinkError e){
            ReLinker.log(logcatLogger)
                    .force()
                    .recursively().loadLibrary(this,"sqlcipher");
        }

        sqlHelper = new SqliteDataBaseHelper(this,"test.db",null,1);

        db = sqlHelper.getWritableDatabase("123456");
    }

    @Override
    public void onClick(View view) {
        switch (view.getId()){
            case R.id.btn_insert:
                ContentValues values = new ContentValues();
                values.put("userName","张三");
                values.put("password",123456);
                db.insert("Test",null,values);
                break;
            case R.id.btn_query:
                Cursor cursor = db.query("Test",null,null,null,null,null,null);
                if (cursor != null){
                    while (cursor.moveToNext()){
                        String userName = cursor.getString(
                                cursor.getColumnIndex("userName"));
                        int password = cursor.getInt(
                                cursor.getColumnIndex("password"));
                        Log.e(TAG,"userName="+userName+";password="+password);
                    }
                }
                cursor.close();
                break;
        }
    }
}
```

这里稍微解释一下ReLinker，有时候我们使用第三方提供的动态库so文件，在APK打包时候会报UnsatisfiedLinkError错误，而ReLinker就是减少UnsatisfiedLinkError的错误率。

5、 将上面生成的test.db文件导出用SQLite Expert Personal 3 工具打开，如图：

![Encrypt]({{ site.baseurl }}/assets/posts/2016-09-18-encrypt.png)

如果你的手机有root权限也可以使用Root Explorer查看，也是显示加密错误。
提示:test.db在data/data/packagename/databases目录下
既然可以加密那么也能够解密，下面讲讲SQLCipher如何解密

## SQLCipher解密篇

上面讲了SQLCipher如何加密数据库文件，下面讲如果对加密后的数据库文件进行解密。同样需要我们在官网下载：[https://github.com/sqlcipher/sqlcipher](https://github.com/sqlcipher/sqlcipher)；这里下载的是sqlcipher的源码，源码下载下来后我们需要对它进行编译。

### 1. 安装环境

Linux系统，这里我用CentOS 6.5，安装GCC编译器，sqlite数据库，以及上面提到的OpenSSL如果你的虚拟机上都已经装过了请跳过此步骤。那么怎么看有没有安装呢？通过rpm命令例如：`rpm -qa|grep -l opensssl`来查看openssl是否安装。

下面我们开始讲解如何安装：

1）安装GCC编译器，这里通过yum源进行安装：

```
yum install gcc gcc-c++；
```

2）安装SQLite数据库：

下载地址：[http://www.sqlite.org/download.html](http://www.sqlite.org/download.html)

```
tar zxvf sqlite-autoconf-3140200.tar.gz #解压
./configure #编译
make && make install #安装
```

3）安装OpenSSL：

```
yum -y install openssl-devel
```

### 2. 编译SQLCipher

将下载好的 sqlcipher-maskter.zip 复制到虚拟机上进行解压

```
unzip -q sqlcipher-master.zip
```

![unzip sqlcipher-master.zip]({{ site.baseurl }}/assets/posts/2016-09-18-ls.png)

进入sqlcipher-maskter文件内：`cd sqlcipher-maskter`，参考官方说明进行编译：

![Compiling]({{ site.baseurl }}/assets/posts/2016-09-18-compiling.png)

Crypto就是openssl提供的秘钥库，第一个采用静态链接，第二个采用动态链接，我使用的是第二种方式进行编译。

### 3. 解密数据库文件

将加密的数据库复制到sqlcipher-maskter目录下，使用sqlcipher解密数据库文件

![解密]({{ site.baseurl }}/assets/posts/2016-09-18-解密.png)

`PRAGMA Key='123456'`输入加密的密码，通过PBKDF2键推导获取数据库的加密秘钥
将test2.db复制到桌面用上面的SQLite Expert Personal 3工具打开，可以看到解密成功了。

![解密后打开]({{ site.baseurl }}/assets/posts/2016-09-18-解密后打开.png)

## 总结

在做加密解密的时候犯了两个低级错误，加密时创建项目引入so库的时候一直报UnsatisfiedLinkError错误，怎么也解决不了，后来发现是我的jniLibs目录不小心放错了。第二个问题就是解密的时候，在执行sqlcipher编译的时候总是失败，后来发现没有crypto，于是我安装了openssl-devel这样才编译通过，这个是没有仔细看官方说明导致的，以后要避免。
