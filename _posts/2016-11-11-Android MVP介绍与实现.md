---
title: Android MVP介绍与实现
date: 2016-11-11 21:47:50 +08:00
categories:
- jekyll
layout: post
author: Darre
---

## 描述：

MVP模式是什么？MVP是从经典的模式MVC演变而来的，它们的基本思想有想通的地方：Controller/Presenter负责逻辑的处理，Model提供数据，View负责显示。

## MVP和MVC的区别：

为什么会出现MVP模式？因为原有的MVC模式有一些不足。比如在android开发中，activity充当着MVC中Controller的角色，但是在实际开发中处理view的逻辑和角色。当业务界面复杂时我们的activity会显得很庞大。造成了activity既像View又像Controller。于是出现了MVP模式，他新增了一个Presenter角色用于处理数据和界面模型以及逻辑，Activity仅仅用于展示界面和用户交互，这样解决了MVC中角色不清的局面。所以，MVP与MVC的重大区别：在MVP中View并不直接使用Model,它们之间的通信是通过Presenter来进行的，所有的交互都发生在Presenter内部，而在MVC中View会直接从Model中读取数据而不是通过Controller。
在MVC里，View是可以直接访问Model的，从而，View里会包含Model信息，不可避免的还要包括一些业务逻辑。在MVC模型里，更关注的Model不变，而同时有多个对Model不同显示，即View，所以，在MVC模型里，Model不依赖与View，但是View是依赖于Model的，不仅如此，因为有一些业务逻辑在View里实现了，导致要更改View也是比较困难的，至少那些业务逻辑是无法重用的。

## MVP与MVC模型结构如图：
 
![](https://github.com/AriadneThread/ariadnethread.github.io/blob/master/assets/posts/2016-11-11-mvc_mvp.png)

## MVC 模型结构：

1、	Model业务逻辑和实体模型
2、	Controller对应Activity
3、	View视图以及布局文件

## MVP模型结构：

1、	Model：业务逻辑和实体模型
2、	View：用户交互和视图显示，在activity中对应activity
3、	Presenter：负责完成View与Model间的逻辑和交互

综上：MVP模式相当于在MVC模式中又加了一个Presenter用于处理模型和逻辑，将View和Model完全独立开，在android开发中的体现就是activity仅用于显示界面和交互，activity不参与模型结构和逻辑。
谷歌给了我们一个MVP模式实战例子，它是一个类似记事本的app，源码地址：https://github.com/googlesamples/android-architecture
官方案例的框架图如下：
 
![](https://github.com/AriadneThread/ariadnethread.github.io/blob/master/assets/posts/2016-11-11-mvp.png) 
 
## 实战：

下面用MVP模式写一个的简单的登录功能。目录结构如图：

![](https://github.com/AriadneThread/ariadnethread.github.io/blob/master/assets/posts/2016-11-11-mvp_demo.png) 
 
1、	Model层：处理和数据相关的一些简单操作

```
/**
 * Created by hao on 2016/11/2.
 * Model：处理和数据相关的一些简单操作
 */
public class UserMsg {
    private String userName;
    private String passWord;

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public String getPassWord() {
        return passWord;
    }

    public void setPassWord(String passWord) {
        this.passWord = passWord;
    }
```

```
/**
 * Created by hao on 2016/11/2.
 */
public interface RequestInterface {
    void login(String userName,String passWord,RequestListener requestListener);
}
```

```
/**
 * Created by hao on 2016/11/2.
 */
public interface RequestListener {
    <T>void success(T clzz);
    void failed();
}
```

```
/**
 * Created by hao on 2016/11/2.
 *
 */
public class RequestTool<T> implements RequestInterface {
    private UserMsg user;
    public RequestTool(T mClass) {
        this.user = (UserMsg) mClass;
    }

    @Override
    public void login(final String userName, final String passWord, final RequestListener requestListener) {
        new Thread(){
            @Override
            public void run() {
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                if ("darre".equals(userName) && "123456".equals(passWord)){
                    user.setUserName(userName);
                    user.setPassWord(passWord);
                    requestListener.success(user);
                }else{
                    requestListener.failed();
                }
            }
        }.start();
    }
}
```

这里有两个接口：一个是请求方法的接口，一个是请求回调成功失败接口，这里模拟了一下登录请求访问。所以，在这里开启了一个子线程，让它休眠2秒。

2、	View：通过定义一个接口来和Presenter进行交换，这个接口主要是对视图进行操作。

```
/**
 * Created by hao on 2016/11/2.
 */
public interface ViewOperation {
    String getUserName();
    String getPassWord();
    void clearUserName();
    void clearPassWord();
    void showToast(String str);
    void showLoading();
    void hideLoading();
}
```

```
/**
 * 与view相关
 */
public class MainActivity extends AppCompatActivity implements View.OnClickListener,ViewOperation {

    private EditText user_et,pwd_et;
    private Button login_btn,clear_btn;
    private Toast toast;
    private LoginPresenter loginPresenter;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();
        setListener();
        initData();
    }

    private void initView(){
        user_et = (EditText) findViewById(R.id.user_et);
        pwd_et = (EditText) findViewById(R.id.pwd_et);
        login_btn = (Button) findViewById(R.id.login_btn);
        clear_btn = (Button) findViewById(R.id.clear_btn);
    }

    private void setListener(){
        login_btn.setOnClickListener(this);
        clear_btn.setOnClickListener(this);
    }

    private void initData(){
        loginPresenter = new LoginPresenter(this);
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.login_btn:
                loginPresenter.login();
                break;
            case R.id.clear_btn:
                loginPresenter.clear();
                break;
        }
    }

    @Override
    public String getUserName() {
        return user_et.getText().toString();
    }

    @Override
    public String getPassWord() {
        return pwd_et.getText().toString();
    }

    @Override
    public void clearUserName() {
        user_et.setText("");
    }

    @Override
    public void clearPassWord() {
        pwd_et.setText("");
    }

    @Override
    public void showToast(String str) {
        if (toast == null){
            toast = Toast.makeText(this,str,Toast.LENGTH_LONG);
        }else{
            toast.setText(str);
        }
        toast.show();
    }

    @Override
    public void showLoading() {

    }

    @Override
    public void hideLoading() {

    }
}
```

这个时候Activity只负责处理View相关的工作。

3、	Presenter：处理界面逻辑和数据模型

```
/**
 * Created by hao on 2016/11/2.
 */
public interface TaskPresenter {
    void login();
    void clear();
}
```

```
/**
 * Created by hao on 2016/11/2.
 * 处理界面逻辑和数据模型
 */
public class LoginPresenter<T> implements TaskPresenter {

    private ViewOperation viewOperation;
    private RequestTool requestTool;
    public LoginPresenter(ViewOperation viewOperation) {
        this.viewOperation = viewOperation;
        requestTool = new RequestTool(UserMsg.class);
    }

    @Override
    public void login() {
        viewOperation.showLoading();
        requestTool.login(viewOperation.getUserName(), viewOperation.getPassWord(), new RequestListener() {

            @Override
            public <T> void success(T clzz) {
                viewOperation.hideLoading();
                viewOperation.showToast("登录成功");
            }

            @Override
            public void failed() {
                viewOperation.hideLoading();
                viewOperation.showToast("登录失败");
            }
        });
    }

    @Override
    public void clear() {
        viewOperation.clearPassWord();
        viewOperation.clearUserName();
    }
```

Presenter主要是Model和View交互的桥梁，这里有一个登录功能，一个清除功能。它们的交互大致是从View中获取需要的数据，交给Model去执行业务方法，执行结束后需要反馈，最终让View做相应的显示。

## 总结：

MVP模式会使得代码多出一些接口但是它使代码逻辑更加清晰，尤其在处理复杂的界面和逻辑时，我们可以对同一个Activity的每一个业务都抽离成一个Presenter，这样代码即清晰逻辑明确又方便我们扩展。这个也要根据业务需求来走，如果业务比较简单，就没那个必要使用MVP了。

## 参考：

http://gold.xitu.io/entry/576bb0c4816dfa0055c0cf17/promote?utm_source=baidu&utm_medium=keyword&utm_content=android_mvp&utm_campaign=q3_search


