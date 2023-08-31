ViewGroup 和View

ViewGroup是布局，类似我们家的布局，哪里是厨房，哪里是卧室……，

View是控件，类似于家具，哪里放沙发，哪里放电视……；

字体大小使用sp



五种布局都继承与ViewGroup


res 文件夹中有drawable 和 mipmap 开头的多个文件夹,是用来存放图片资源的

分辨率不同设备会自动匹配相应的 drawable 和 mipmap 文件夹中的图片资源

## 通用属性

|       属性名称        |                 功能描述                 |
| :-------------------: | :--------------------------------------: |
|      android:id       |              设置布局的标识              |
| android:layout_width  |              设置布局的宽度              |
| android:layout_height |              设置布局的高度              |
|  android:background   |              设置布局的背景              |
| android:layout_margin | 设置当前布局与屏幕边界或与周围控件的距离 |
|    android:padding    |     设置当前布局与该布局中控件的距离     |

## RelativeLayout 相对布局

|             属性名称             |                 功能描述                 |
| :------------------------------: | :--------------------------------------: |
|  android:layout_centerInParent   |     设置当前控件位于父布局的中央位置     |
|  android:layout_centerVertical   |   设置当前控件位于父布局的垂直居中位置   |
| android:layout_centerHorizontal  |   设置当前控件位于父控件的水平居中位置   |
|       android:layout_above       |        设置当前控件位于某控件上方        |
|       android:layout_below       |        设置当前控件位于某控件下方        |
|     android:layout_toLeftOf      |        设置当前控件位于某控件左侧        |
|     android:layout_toRightOf     |        设置当前控件位于某控件右侧        |
|  android:layout_alignParentTop   |     设置当前控件是否与父控件顶端对齐     |
|  android:layout_alignParentLeft  |      设置当前控件是否与父控件左对齐      |
| android:layout_alignParentRight  |      设置当前控件是否与父控件右对齐      |
| android:layout_alignParemtBottom |     设置当前控件是否与父控件底端对齐     |
|     android:layout_alignTop      | 设置当前控件的上边界与某控件的上边界对齐 |
|    android:layout_alignBottom    | 设置当前控件的下边界与某控件的下边界对齐 |
|     android:layout_alignLeft     | 设置当前控件的左边界与某控件的左边界对齐 |
|    android:layout_alignRight     | 设置当前控件的右边界与某控件的右边界对齐 |

## LinearLayout 线性布局

|       属性名称        |                           功能描述                           |
| :-------------------: | :----------------------------------------------------------: |
|  android:oriemtation  |    设置布局内控件的排列顺序（写在LinearLayout标签里面的）    |
| android:layout_weight | 在布局内设置控件权重，属性值可直接写 int 值（需要把控件的宽度设置成0dp） |

## TableLayout 表格布局

三个布局属性:

|        属性名称         |                           功能描述                           |
| :---------------------: | :----------------------------------------------------------: |
| android:stretchColumns  | 设置可被拉伸的列。如：android:stretchColumns="0"，表示第 1 列可被拉伸 |
|  android:shrinkColumns  | 设置可被收缩的列。如：android:shrinkColumns="1,2"，表示2，3列可收缩 |
| android:collapseColumns | 设置可被隐藏的列。如：android:collapscColumns="0"，表示第 1 列可被隐藏 |

二个控件属性:

|       属性名称        |                           功能描述                           |
| :-------------------: | :----------------------------------------------------------: |
| android:layout_column | 设置该控件显示的位置，如android:layout_colum="1"表示在第2个位置显示 |
|  android:layout_span  |                设置该控件占据几行，默认为1行                 |

## FraameLayout 帧布局

|         属性名称          |                     功能描述                     |
| :-----------------------: | :----------------------------------------------: |
|    android:foreground     | 设置帧布局容器的前景图像（始终在所有子控件之上） |
| android:foregroundGravity |              设置前景图像显示的位置              |

## ConstraintLayout 约束布局

|                属性名称                |                功能描述                |
| :------------------------------------: | :------------------------------------: |
|     layout_constraintLeff_toLeffor     |   控件的左边与另外一个控件的左边对齐   |
|    layout_constraintLeft_toRightof     |   控件的左边与另外一个控件的右边对齐   |
|    layout_constraintRight_toLefiof     |   控件的右边与另外一个控件的左边对齐   |
|    layout_constraintRight_toRightof    |   控件的右边与另外一个控件的右边对齐   |
|      layout_constraintTop_toTopof      |   控件的上边与另外一个控件的上边对齐   |
|    layout_constraintTop_toBottomof     |   控件的上边与另外一个控件的底部对齐   |
| layout_constraintBaseline_toBaselineOf |          控件间的文本内容基齐          |
|     layout_constraintStart_toEndor     |  控件的起始边与另外一个控件的尾部对齐  |
|    layout_constraintStart_toStartor    | 控件的起始边与另外一个控件的起始边对齐 |
|     layout_constraintEnd_toStartof     |  控件的尾部与另外一个控件的起始边对齐  |
|      layout_constraintEnd_toEndor      |   控件的尾部与另外一个控件的尾部对齐   |

倾向属性

|             属性名称             |  功能描述  |
| :------------------------------: | :--------: |
| layout_constraintHorizontal_bias | 横向的倾向 |
|  layout_constraintVertical_bias  | 纵向的倾向 |

