## 4.1 Activity生命周期

### 4.1.1 生命周期状态

**启动**状态
`Activity` 的启动状态很短暂，一般情况下，当 `Activity` 启动之后便会进入运行状态。

**运行**状态
`Activity` 在此状态时处于界面最前端，它是可见、有焦点的，可以与用户进行交互，如点击界面中的按钮和在界面上输入信息等。

**暂停**状态
在某些情况下， `ActiVity` 对用户采说仍然可见，但它无法状取集点，用户对它操作没有响应，此时它就处于暂停状态。

**停止**状态
当 `Activity` 完全不可见时，它就处于停止状态。

**销毁**状态
当 `Activity` 处于销毁状态时，将被清理出内存。

### 4.1.2 生命周期方法

(1) onCreate():	Activity创建时调用，通常做一些初始化设置。

(2) onStart():	Activity即将可见时调用。

(3) onResume():	Activity获取焦点时调用。

(4) onPause():	当前Activity被其他Activity覆盖或屏幕锁屏时调用。

(5) onStop():	Activity对用户不可见时调用。

(6) onDestroy():	Activity销毁时调用。

(7) onRestart():	Activity从停止状态到再次启动时调用。



横竖屏切换会调用 `onDestroy()` 销毁 `Activity`，重新调用 `onCreate()` 方法重建Activity。

通过 `configChanges` 属性进行设置就不会清除输入的信息

```xml
<activity android:name=".MainActivity"
		  android:configChanges="orientationIkeyboardHidden">
```

当`configChanges`属性设置完成之后，打开程序时同样会调用`onCreate()`,`onStar()`、`onResume()`方法，但是当进行横竖屏切换时不会再执行其他的生命周期方法。

如果希望某一个界面一直处于竖屏或者横屏状态，不随手机的晃动而改变，可以在清单文件中通过设置`Activity`的`screenOrientation`属性完成，示例代码如下：

```xml
竖屏：android:screenOrientation="portrait"
横屏：android:screenOrientation="landscape"
```



## 4.2 Activity的创建、配置、开启和关闭

### 4.2.1 创建 Activity

在包上右击选择

![image-20221004104302195](https://lsky.hhdxw.top/imghub/img/image-20221004104302195.png)

一个页面对应一个布局文件 xml

![image-20221004104505572](https://lsky.hhdxw.top/imghub/img/image-20221004104505572.png)

### 4.2.2 配置Activity

清单文件`AndroidManifest.xml`中会创建对应的 xml ，表示程序页面首选项

```xml
<activity
    android:name=".MyActivity"
    android:exported="false" />
```

### 4.2.3 开启和关闭Activity

(1)在 `activity_main.xml` 中添加按钮组件

```xml
<Button
    android:id="@+id/btn"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="单击跳转MyActivity" />
```

(2)在 `MainActivity` 中为按钮添加单击事件，让其跳转到MyActivity页面

```java
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        findViewById(R.id.btn).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
//                跳转到MyActivty
                Intent intent= new Intent(MainActivity.this,MyActivity.class);
                startActivity(intent);
            }
        });
    }
```

(3)在 `activity_my.xml` 中添加按钮组件

```xml
<Button
    android:id="@+id/btn"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="关闭当前页面" />
```

(4)在 `MyActivity` 中为按钮添加单击事件，让其关闭当前页面

```java
public class MyActivity extends AppCompatActivity implements View.OnClickListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_my);
        findViewById(R.id.btn).setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
//        关闭当前页面
        finish();
    }
}
```

## 4.3 Intent与IntentFilter

首先创建三个页面 A 、B、C 

在`A`页面中填写两个`Button`按钮，分别通过`显示`、`隐示`跳转至其他页面

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".A">

    <Button
        android:id="@+id/b"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="显示跳转页面B" />

    <Button
        android:id="@+id/c"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="隐示跳转页面C" />

</LinearLayout>
```

![image-20221006123245336](https://lsky.hhdxw.top/imghub/img/image-20221006123245336.png)

```java
public class A extends AppCompatActivity implements View.OnClickListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_a);
        findViewById(R.id.b).setOnClickListener(this);
        findViewById(R.id.c).setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        switch (view.getId()) {
            case R.id.b:
//                显示跳转到B
                Intent intent = new Intent(A.this, B.class);
                startActivity(intent);
                break;

            case R.id.c:
//                隐式跳转到C
                Intent intent1 = new Intent();
                intent1.setAction("a");
                startActivity(intent1);
                break;
        }
    }
}
```

更改app入口页面

```xml
<application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.MyApplication"
        tools:targetApi="31">
        <activity
            android:name=".C"
            android:exported="false" >
            <intent-filter>
                <action android:name="a" />

                <category android:name="android.intent.category.DEFAULT" />

            </intent-filter>
        </activity>
        <activity
            android:name=".B"
            android:exported="false" />

        <activity
            android:name=".A"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>
```

![image-20221006123256251](https://lsky.hhdxw.top/imghub/img/image-20221006123256251.png)

## 4.4 Activity之间的跳转

### 4.4.2 Activity之间的数据传递

在首页创建两个按钮

```xml
<Button
    android:id="@+id/btn1"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="使用 putExtra 传递数据" />

<Button
    android:id="@+id/btn2"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="使用 Bundle 传递数据" />
```

方法

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {
    Button btn1, btn2;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        btn1 = findViewById(R.id.btn1);
        btn1.setOnClickListener(this);
        btn2 = findViewById(R.id.btn2);
        btn2.setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        switch (view.getId()) {
            case R.id.btn1:
//                使用 putExtra 传递数据
                Intent intent = new Intent(MainActivity.this, FirstActivity.class);
                intent.putExtra("name", "王小明");
                intent.putExtra("age", 12);
                intent.putExtra("isPassed", true);
                startActivity(intent);
                break;
            case R.id.btn2:
//                使用 Bundle 传递数据
                Intent intent1 = new Intent(MainActivity.this, SecondActivity.class);

//                创建 Bundle 对象
                Bundle bundle = new Bundle();
                bundle.putString("name","王小明");
                bundle.putInt("age",12);
                intent1.putExtras(bundle);
                startActivity(intent1);
                break;
        }

    }
}
```

另外两个页面

```xml
    <Button
        android:id="@+id/btn3"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="接收 putExtra 传递的数据" />

    <TextView
        android:id="@+id/text1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
```

```xml
    <Button
        android:id="@+id/btn4"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="接收 Bundle 传递的数据" />

    <TextView
        android:id="@+id/text2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
```

接收方法

```java
public class FirstActivity extends AppCompatActivity {
    Button btn3;
    TextView text1;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_first);
        text1=findViewById(R.id.text1);
        btn3=findViewById(R.id.btn3);
        btn3.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
//                接收数据并显示在text1
                Intent intent=getIntent();
                String name = intent.getStringExtra("name");
                int age = intent.getIntExtra("age",0);
                boolean isPassed = intent.getBooleanExtra("isPassed",true);
                text1.setText(name+":"+age+":"+isPassed);
            }
        });
    }
}
```

```java
public class SecondActivity extends AppCompatActivity {
    Button btn4;
    TextView text2;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second);
        text2 = findViewById(R.id.text2);
        btn4 = findViewById(R.id.btn4);
        btn4.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
//                接收数据并显示在text2
                Bundle bundle = getIntent().getExtras();
                String name = bundle.getString("name");
                int age = bundle.getInt("age");
                text2.setText(name + ":" + age);
            }
        });

    }
}
```

![image-20221011112153331](https://lsky.hhdxw.top/imghub/img/image-20221011112153331.png)

### 4.4.2 Activity之间的数据回传

