## 第十章：Android 事件处理

1.事件分为：键盘事件、触摸事件

2.处理事件的两种方式：

​	1.基于回调机制
​	回调on开头的方法，返回值true,代表事件处理完毕；
​	2.基于监听接口

10.4 手势

案例一：创建并保存手势

1.创建项目并复制布局文件



案例：手势识别

1.创建项目 `AddGesture` 

2.将  `AddGesture` 中的 `mygestur` 放到 `GestureRecognition` 项目下的 `res` --> `raw ` 中

3.布局文件

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical">
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:text="手势"
        android:textColor="@android:color/black"
        android:textSize="20dp" />
    <android.gesture.GestureOverlayView
        android:id="@+id/gestures"
        android:layout_width="fill_parent"
        android:layout_height="0dip"
        android:layout_weight="1.0" />
</LinearLayout>
```

方法2：直接在SD卡里面试别