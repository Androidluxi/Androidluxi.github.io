# Android隐藏头部状态栏

### 1. onCreate 中通过代码隐藏（MainActivity）

首先贴上官网介绍的方法

[隐藏状态栏  | Android 开发者  | Android Developers](https://developer.android.google.cn/training/system-ui/status?hl=zh-cn#41)

在Android4.1之后，如果Activity继承的是Application，则用官方介绍的办法来隐藏状态栏

    View decorView = getWindow().getDecorView();
    // Hide the status bar.
    int uiOptions = View.SYSTEM_UI_FLAG_FULLSCREEN;
    decorView.setSystemUiVisibility(uiOptions);
    // Remember that you should never show the action bar if the
    // status bar is hidden, so hide that too if necessary.
    ActionBar actionBar = getActionBar();
    actionBar.hide();

存下以下错误：getActionBar的值是null。

![image-20230302101355585](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230302101355585.png)

**如果Activity继承了Appcompat，则必须使用getSupportActionBar()来获取ActionBar**

我实验之后发现这部分代码有部分废弃了。具体作用也不是很明确。

```
View decorView = getWindow().getDecorView();
int uiOptions = View.SYSTEM_UI_FLAG_FULLSCREEN;
decorView.setSystemUiVisibility(uiOptions);
```

1. MainActivity 继承 Activity 时
   如果在 onCreate --> setContentView 方法之后加则会报错。
   onCreate --> setContentView 方法前加入以下代码：

   ```java
   requestWindowFeature(Window.FEATURE_NO_TITLE);
   ```

2. MainActivity 继承 AppCompatActivity 时

   ```java
   ActionBar actionBar = getSupportActionBar();
           if (actionBar!=null) {
               actionBar.hide();
           }
   ```

### 2.布局清单中通过 Activity theme 隐藏（AndroidMainfest）

这种方式，MainActivity 继承 Activity 或 AppCompatActivity 都可用

在 res —> values —> styles —> 中加入以下代码：

```xml
<style name="HideStyle" parent="Theme.AppCompat.Light.NoActionBar"/>
```

然后在需要隐藏标题栏的activity标签声明中，加入以下代码即可；

```xml
android:theme="@style/HideStyle"
```

