## 1.页面搭建

首先将背景图`bg`拷贝到`drawable`

![bg](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/bg.png)

然后在`layout`文件下编写页面文件`activity_main.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@drawable/bg"
    android:padding="16dp"
    android:orientation="vertical">
    <LinearLayout
        android:layout_marginTop="130dp"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="姓  名 ："
            android:textSize="18sp" />
        <EditText
            android:id="@+id/et_name"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="请输入姓名"
            android:textSize="16sp" />
    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="10dp">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="电  话 ："
            android:textSize="18sp" />
        <EditText
            android:id="@+id/et_phone"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="请输入手机号码"
            android:textSize="16sp" />
    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
        <Button
            android:id="@+id/btn_add"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginRight="2dp"
            android:layout_weight="1"
            android:background="#B9B9FF"
            android:text="添加"
            android:textSize="18sp" />
        <Button
            android:id="@+id/btn_query"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginRight="2dp"
            android:layout_weight="1"
            android:background="#DCB5FF"
            android:text="查询"
            android:textSize="18sp" />
        <Button
            android:id="@+id/btn_update"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginRight="2dp"
            android:layout_weight="1"
            android:background="#E6CAFF"
            android:text="修改"
            android:textSize="18sp" />
        <Button
            android:id="@+id/btn_delete"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:background="#ACD6FF"
            android:text="删除"
            android:textSize="18sp" />
    </LinearLayout>
    <TextView
        android:id="@+id/tv_show"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="25dp"
        android:textSize="20sp" />
</LinearLayout>
```

![image-20221020093735433](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20221020093735433.png)

## 2.后端代码编写

### 1.首先创建控件和初始化所有控件并绑定单击事件

```java
private EditText mEtName, mEtPhone;
private TextView mTvShow;
private Button mBtnAdd, mBtnQuery, mBtnUpdate, mBtnDelete;
```

```java
mEtName = (EditText) findViewById(R.id.et_name);
mEtPhone = (EditText) findViewById(R.id.et_phone);
mTvShow = (TextView) findViewById(R.id.tv_show);
mBtnAdd = (Button) findViewById(R.id.btn_add);
mBtnQuery = (Button) findViewById(R.id.btn_query);
mBtnUpdate = (Button) findViewById(R.id.btn_update);
mBtnDelete = (Button) findViewById(R.id.btn_delete);
mBtnAdd.setOnClickListener(this);
mBtnQuery.setOnClickListener(this);
mBtnUpdate.setOnClickListener(this);
mBtnDelete.setOnClickListener(this);
```

### 2.SQLite数据库的创建

```java
//1. 创建数据库
class MyHelper extends SQLiteOpenHelper {
    //创建数据库
    public MyHelper(@Nullable Context context) {
        super(context, "itcast.db", null, 1);
    }

    //创建表
    @Override
    public void onCreate(SQLiteDatabase sqLiteDatabase) {
        String str = "create table information(_id integer primary key autoincrement, name varchar(20),  phone varchar(20))";
        sqLiteDatabase.execSQL(str);
    }

    //当数据库版本号发生变化时自动调用的方法
    @Override
    public void onUpgrade(SQLiteDatabase sqLiteDatabase, int i, int i1) {

    }
}
```