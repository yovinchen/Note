# 第三章：Android常见界面控件

## 3.1简单控件

### 1.TextView（显示文本信息）

|         属性名称         |                           功能描述                           |
| :----------------------: | :----------------------------------------------------------: |
|   android:layout_width   |                   设置 TextView控件的宽度                    |
|  android:layout_height   |                   设置 TextView控件的高度                    |
|        androidid         |                 设置 TextView 控件的唯一标识                 |
|    android:background    |                   设置 TextView 控件的背景                   |
|  android:layout_margin   |         设置当前控件与屏幕边界或周围控件、布局的距离         |
|     android:padding      |            设置 TextView控件与该控件中内容的距离             |
|       android:text       |                         设置文本内容                         |
|    android:textColor     |                      设置文字显示的颜色                      |
|     android:textSize     |   设置文字大小，推荐单位为 5p，如android:textSize = "15sp"   |
|     android:gravity      |     设置文本内容的位置，如设置成“center”，文本将居中显示     |
|    android:maxLength     | 设置文本最大长度，超出此长度的文本不显示。如android:maxLength - "10" |
|      android:lines       |            设置文本的行数，超出此行数的文本不显示            |
|     android:maxLines     |          设置文本的最大行数，超出此行数的文本不显示          |
|    android:ellipsize     | 设置当文本超出TextView规定的范围的显示方式。属性值可选为"start”, "middle" 和"end"，分别表示当文本超出TextView规定的范围时，在文本开始、中间或者末尾显示省略号“----” |
|   android:drawableTop    | 在文本的顶部显示图像，该图像资源可以放在 res/drawable 相应分辨率的目录下，通过“@drawable/文件名”调用。类似的属性有 android:drawableBottorm、android:drawableLefi、android:drawableRight |
| android:lineSpacingExtra |                       设置文本的行间距                       |
|    android:textStyle     |  设置文本样式，如bold (租体), italic (斜体), normal (正常)   |

### 2.Button（按钮）

（1）在布局文件中指定 onClick 属性的方式设置点击事件

```xml
<!--    activity_main.xml 文件-->
<Button
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:onClick="click"
    android:text="onClick属性" />
```

```java
//    MainActivity.java 文件
public void click(View view) {
    System.out.println("我是通过onClick属性完成的单击事件");
}
```

（2）使用匿名内部类的方式设置点击事件

```xml
<!--    activity_main.xml 文件-->
<Button
    android:id="@+id/btn_two"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="匿名内部类" />
```

```java
//    MainActivity.java 文件
public class MainActivity extends AppCompatActivity {
    Button btn_two;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        btn_two = findViewById(R.id.btn_two);
        btn_two.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                System.out.println("我是通过匿名内部类完成的单击事件");
            }
        });
    }
}
```

（3）Activity 实现 OnClickListener 接口的方式设置点击事件

```xml
<!--    activity_main.xml 文件-->
<Button
    android:id="@+id/btn_three"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="接口实现" />
```

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {
    Button btn_three;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        btn_three=findViewById(R.id.btn_three);
        btn_three.setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        System.out.println("我是通过实现接口的方式完成的单击事件");
    }
}
```

### 3.EditText（编辑框）

|          属性姓名          |                        功能描述                        |
| :------------------------: | :----------------------------------------------------: |
|        android:hint        |           控件中内容为空时显示的提示文本信息           |
|   android:textColorHint    |        控件中内容为空时显示的提示文本信息的颜色        |
|      android:password      |              输人文本框中的内容显示为“-”               |
|      android:minLines      |                   设置文本的最小行数                   |
| androidiscrollHorizontally | 设置文本信息超出 EdifText 的宽度情况下，是否出现横拉条 |
|      android:editable      |                     设置是否可编辑                     |
|    android:phoneNumber     |            设置输人文本框中的内容只能是数字            |

### 4.Image View（图片）

|       属性名称        |                    功能描述                     |
| :-------------------: | :---------------------------------------------: |
| android:layout_width  |            设置 ImageView 控件的宽度            |
| android:layout_height |            设置 ImageView控件的高度             |
|      android:id       |           设置ImageView控件的唯一标识           |
|  android:background   |            设置 ImageView 控件的背景            |
| android:layout_margin |     设置当前控件与屏幕边界或周围控件的距离      |
|      android:src      |       设置ImageView控件需要显示的图片资源       |
|   android:scaleType   | 将图片资源缩放或移动，以适应ImageView控件的宽高 |
|     android:tint      |             将图片渲染成指定的颜色              |

### 5.RadioButton（单选按钮）

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="请选择你的性别" />
    <RadioGroup
        android:id="@+id/rg"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content">
        <RadioButton
            android:id="@+id/rb_1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="男" />
        <RadioButton
            android:id="@+id/rb_2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="女" />
        <RadioButton
            android:id="@+id/rb_3"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="保密" />
    </RadioGroup>
    <TextView
        android:id="@+id/tv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="此处显示您的性别：" />
</LinearLayout>
```

![image-20220920170834225](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220920170834225.png)

```java
public class MainActivity extends AppCompatActivity {
    RadioGroup rg;
    RadioButton rb1, rb2;
    TextView tv;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        init();
//        为单选按钮组添加单击事件
        rg.setOnCheckedChangeListener(new RadioGroup.OnCheckedChangeListener() {
            @Override
            public void onCheckedChanged(RadioGroup radioGroup, int i) {
//                radioGroup     代表的是单选按钮组
//                i              代表的是单选按钮
                switch (i) {
                    case R.id.rb_1:
                        tv.setText("您的性别是：男");
                        break;
                    case R.id.rb_2:
                        tv.setText("您的性别是：女");
                        break;
                    case R.id.rb_3:
                        tv.setText("您的性别是：未知");
                        break;
                }
            }
        });
    }

    //    初始化所有的控件
    void init() {
        rg = findViewById(R.id.rg);
        rb1 = findViewById(R.id.rb_1);
        rb1 = findViewById(R.id.rb_2);
        rb1 = findViewById(R.id.rb_3);
        tv = findViewById(R.id.tv);
    }
}
```

演示：

![image-20220920170949644](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220920170949644.png)

### 6.CheckBox（复选框）

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity"
    android:orientation="vertical">
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="请选择您的爱好：" />
    <CheckBox
        android:id="@+id/cb_1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="看书"/>
    <CheckBox
        android:id="@+id/cb_2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="看视频"/>
    <CheckBox
        android:id="@+id/cb_3"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="看文档"/>
    <TextView
        android:id="@+id/tv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="您的爱好是：" />
</LinearLayout>
```

![image-20220920171135978](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220920171135978.png)

```java
public class MainActivity extends AppCompatActivity implements CompoundButton.OnCheckedChangeListener {
    CheckBox cb1, cb2, cb3;
    TextView tv;
    String hobbys="您的爱好是：";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        init();
//        为每一个复选框添加一个单击事件
        cb1.setOnCheckedChangeListener(this);
        cb2.setOnCheckedChangeListener(this);
        cb3.setOnCheckedChangeListener(this);
    }

    void init() {
        cb1 = findViewById(R.id.cb_1);
        cb2 = findViewById(R.id.cb_2);
        cb3 = findViewById(R.id.cb_3);
        tv = findViewById(R.id.tv);
    }

    @Override
    public void onCheckedChanged(CompoundButton compoundButton, boolean b) {
//        compoundButton 代表的是状态被改变的复选框
//        b              代表的是状态 1.选中 2.未选中
        String motion = compoundButton.getText().toString();
        if (b == true) {
//            如果复选框被选中，需要将复选框中的文本信息添加到 tv 中
            if (!hobbys.contains(motion)){
                hobbys=hobbys+motion;
                tv.setText(hobbys);
            }
        } else {
//            如果复选框被取消，需要将复选框中的文本信息删除掉
            if(hobbys.contains(motion)){
                hobbys=hobbys.replace(motion,"");
                tv.setText(hobbys);
            }
        }
    }
}
```

演示：

![image-20220920171302035](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220920171302035.png)

### 7.Toast（信息提醒机制）

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <RadioGroup
        android:id="@+id/rg"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content">

        <RadioButton
            android:id="@+id/rb_1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="男" />

        <RadioButton
            android:id="@+id/rb_2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="女" />
    </RadioGroup>
</LinearLayout>
```

![image-20220922091511202](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220922091511202.png)

```java
public class ToastActivity extends AppCompatActivity {

    RadioGroup rg;
    RadioButton rb1,rb2;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
//        页面关联
        setContentView(R.layout.toast);
//        初始化
        init();
//        添加事件
        rg.setOnCheckedChangeListener(new RadioGroup.OnCheckedChangeListener() {
            @Override
            public void onCheckedChanged(RadioGroup radioGroup, int i) {
                if (i==R.id.rb_1){
                    Toast.makeText(ToastActivity.this,"您的性别是男",Toast.LENGTH_LONG).show();
                }else{
                    Toast.makeText(ToastActivity.this,"您的性别是女",Toast.LENGTH_LONG).show();
                }
            }
        });
    }
    void init(){
        rg=findViewById(R.id.rg);
        rb1=findViewById(R.id.rb_1);
        rb2=findViewById(R.id.rb_2);
    }
}
```

演示：

![image-20220922091555853](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220922091555853.png)

## 3.2AlertDialog对话框使用

### 3.2.2普通对话框

通过调用 `setMessage()`方法设置

```java
setMessage(ChaeSequence message)
```



```java
public class MainActivity extends AppCompatActivity {
    //        重写onBackPressed()----单击回退键时调用的方法
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    @Override
    public void onBackPressed() {
//        1.创建静态内部类对象
        AlertDialog.Builder builder = new AlertDialog.Builder(this);
//        2．创建对话框的标题区域----标题图标 标题名称
        builder.setTitle("普通对话框");
        builder.setIcon(R.mipmap.ic_launcher);
//        3．创建对话框的内容区域
        builder.setMessage("您确认退出程序吗？");
//        4．创建对话框的按钮区域
        builder.setPositiveButton("确认", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialogInterface, int i) {
                Toast.makeText(MainActivity.this, "您单击了确认按钮", Toast.LENGTH_LONG).show();
                dialogInterface.dismiss();
                MainActivity.this.finish();
            }
        });
        builder.setNegativeButton("取消", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialogInterface, int i) {
                Toast.makeText(MainActivity.this, "您单击了取消按钮", Toast.LENGTH_LONG).show();
                dialogInterface.dismiss();
            }
        });
//        5．创建对话框对象
        AlertDialog dialog = builder.create();
//        6．显示对话框
        dialog.show();

    }
}
```

![image-20221006082929853](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20221006082929853.png)

### 3.2.3单选对话框

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity"
    android:orientation="vertical">

    <TextView
        android:id="@+id/tv"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="今天课程真好"
        android:textSize="20sp" />

    <Button
        android:id="@+id/btn"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="设置字体大小" />
</LinearLayout>
```

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    //    2.定义成员变量
    Button btn;
    TextView tv;
    int textSize = 1;
    int[] textsizeArr = {10, 20, 30, 40, 50};

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
//        1.初始化
        init();
//        3.为控件绑定事件
        btn.setOnClickListener(this);
    }

    void init() {
        tv = findViewById(R.id.tv);
        btn = findViewById(R.id.btn);
    }

    @Override
    public void onClick(View view) {
//        创建对话框
//        1.创建静态内部类对象
        AlertDialog.Builder builder = new AlertDialog.Builder(this);
//        2.创建标题
        builder.setTitle("单选对话框");
        builder.setIcon(R.mipmap.ic_launcher);
//        3.创建内部区域
        builder.setSingleChoiceItems(new String[]{"小号", "默认", "中等", "大号", "超大号"}, textSize, new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialogInterface, int i) {
                textSize = i;
                Toast.makeText(MainActivity.this, "您单击了按钮", Toast.LENGTH_LONG).show();
                
            }
        });
//        4.创建按钮区域
        builder.setPositiveButton("确定", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialogInterface, int i) {

                tv.setTextSize(textsizeArr[textSize]);
                dialogInterface.dismiss();
            }
        });
        builder.setNegativeButton("取消", null);
//        5.创建对话框
        AlertDialog dialog = builder.create();
//        6.显示对话框
        dialog.show();
    }
}
```

![image-20221006082632884](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20221006082632884.png)

### 3.2.4多选对话框

```xml
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/btn"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="请选择爱好"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {
    String[] str = {"看代码", "敲代码", "改代码"};
    boolean[] str1 = {false, true, false};

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        findViewById(R.id.btn).setOnClickListener(this);

    }

    @Override
    public void onClick(View view) {
//        创建对话框
        AlertDialog dialog = new AlertDialog.Builder(this)
                .setTitle("多选对话框")
                .setIcon(R.mipmap.ic_launcher)
                .setMultiChoiceItems(str, str1, new DialogInterface.OnMultiChoiceClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i, boolean b) {
                        str1[i] = b;
                    }
                })
                .setPositiveButton("确定", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {
                        StringBuffer stringBuffer = new StringBuffer();
                        for (int j = 0; j < str1.length; j++) {
                            if (str1[j]) {
                                stringBuffer.append(str[j]).append("");
                            }
                        }
                        if (stringBuffer != null) {
                            Toast.makeText(MainActivity.this, stringBuffer, Toast.LENGTH_SHORT).show();
                        }
                    }
                })
                .setNegativeButton("取消", null)
                .create();
        dialog.show();
    }
}
```

![image-20221006083710559](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20221006083710559.png)

## 3.3 ListView的使用

```xml
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="16dp">

    <ImageView
        android:id="@+id/iv"
        android:layout_width="120dp"
        android:layout_height="90dp"
        android:layout_centerVertical="true" />

    <RelativeLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerVertical="true"
        android:layout_marginLeft="10dp"
        android:layout_toRightOf="@+id/iv">

        <TextView
            android:id="@+id/title"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="桌子"
            android:textColor="#000000"
            android:textSize="20sp" />

        <TextView
            android:id="@+id/tv_price"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@+id/title"
            android:layout_marginTop="10dp"
            android:text="价格："
            android:textColor="#FF8F03"
            android:textSize="20sp" />

        <TextView
            android:id="@+id/price"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@+id/title"
            android:layout_marginTop="10dp"
            android:layout_toRightOf="@+id/tv_price"
            android:text="1000"
            android:textColor="#FF8F03"
            android:textSize="20sp" />
    </RelativeLayout>
</RelativeLayout>
```

```java
public class MainActivity extends AppCompatActivity {
    //    1.准备数据
    int[] icons = {R.drawable.apple, R.drawable.cake, R.drawable.kiwifruit, R.drawable.scarf, R.drawable.table, R.drawable.wireclothes};
    String[] titles = {"苹果", "蛋糕", "猕猴桃", "围巾", "桌子", "毛衣"};
    String[] prices = {"320元", "10元/kg", "300元", "10元/kg", "2800元", "1600元"};

    //    2.准备控件
    TextView textView;
    ListView listView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        setContentView(R.layout.activity_main);
        listView = findViewById(R.id.lv);

        MyAdapter myAdapter = new MyAdapter();
        listView.setAdapter(myAdapter);
    }

    class MyAdapter extends BaseAdapter {

        @Override
        public int getCount() {
            return titles.length;
        }

        @Override
        public Object getItem(int i) {
            return null;
        }

        @Override
        public long getItemId(int i) {
            return 0;
        }

        @Override
        public View getView(int i, View view, ViewGroup viewGroup) {
            ViewHolder holder = null;
//            找控件
            if (view == null) {
                view = View.inflate(MainActivity.this, R.layout.list_item, null);
                holder = new ViewHolder();
                holder.title = view.findViewById(R.id.title);
                holder.price = view.findViewById(R.id.price);
                holder.iv = view.findViewById(R.id.iv);
                view.setTag(holder);
            } else {
                holder = (ViewHolder) view.getTag();
            }
//            为控件设置内容
            holder.title.setText(titles[i]);
            holder.price.setText(prices[i]);
            holder.iv.setBackgroundResource(icons[i]);
            return view;
        }
    }

    class ViewHolder {
        TextView title, price;
        ImageView iv;
    }
}
```

![image-20221006083432016](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20221006083432016.png)