[TOC]



## python基础

## 1.pip

### 1.1 使用pip管理Python包

```
pip install <包名> 安装指定的包 

pip uninstall <包名> 删除指定的包 

pip list 显示已经安装的包 

pip freeze 显示已经安装的包，并且以指定的格式显示
```

### 1.2 修改pip下载载源

运行pip install 命令会从网站上下载指定的python包，默认是从 https://files.pythonhosted.org/ 网站上下 

载。这是个国外的网站，遇到网络情况不好的时候，可能会下载失败，我们可以通过命令，修改pip现在软件时的 

源。 格式: 

pip install 包名 -i 国内源地址 

示例: pip install ipython -i https://pypi.mirrors.ustc.edu.cn/simple/ 就是从中国科技大学(ustc)的服务器 

上下载requests(基于python的第三方web框架) 

国内常用的pip下载源列表: 

阿里云 http://mirrors.aliyun.com/pypi/simple/ 

中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/ 

豆瓣(douban) http://pypi.douban.com/simple/ 

清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/ 

中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple/ 

### 1.3 退出python环境 

exit() 

ctrl + z ==>enter 

### 1.4 注释

1.一般注释要写到代码上面。（很少情况写到代码后面）

```python
#单行注释
# CTRL+ ? 或 shift + 3
#一般情况下注释要写到代码上面
print('你是我的宝贝')

#多行注释
'''
你是我的宝贝
print('你是我的宝贝')
'''
```

## 2. 变量的定义以及类型

### 2.1 变量的定义

```python
# 重复的值修改很麻烦
print('你是我的宝贝')
print('你是我的宝贝')
print('你是我的宝贝')
print('你是我的宝贝')

# 定义为变量，便于修改赋值
shuzhi = '你是我的宝贝'
print(shuzhi)
print(shuzhi)
print(shuzhi)
```

![image-20211226143711623](https://gitee.com/hhdxw/tuc/raw/master/img/image-20211226143711623.png)

### 2.2 数据类型

https://www.zhixi.com/view/125e9881



![image-20211226134510431](https://gitee.com/hhdxw/tuc/raw/master/img/image-20211226134510431.png)

| 数据类型 |          |
| :------: | :------: |
|   int    |   整型   |
|  float   |  长整型  |
| boolean  |  布尔型  |
|  string  | 字符串型 |
|   list   |   列表   |
|  tuple   |   元组   |
|   dict   |   字典   |

1.int

```python
money = 5000
print(money)
```

2.float

```python
money2 = 3.87
print(money2)
```

3.boolean

```python
# 1.流程控制语句
# 2.性别的控制（在实际企业开发中使用的单词 sex gender）
#   基本开发中 True 是男，False是女
sex = True
sex2 = False
print(sex)
print(sex2)
```

4.string

```python
# 字符串 使用的是 单引号 或者是 双引号 （英文的）
s = '你是我的宝贝'
s2 = "你不是的宝贝"
print(s)
print(s2)
# 不能单双混着写，屌丝写法
# 也不能 双双引号 或者 双单引号的嵌套
s3 = "'你是我的宝贝'"
s4 = '"你是我的宝贝"'
print(s3)
print(s4)
```

5.list

```python
# 应用场景：当获取到很多数据时，可以存储到列表中，然后直接访问。
name_list = ['王','赵']
print(name_list)
```

6.tuple

```python
age_tuple = (0,2,3)
print(age_tuple)
```

7.dict

```python
# 应用场景：scrapy框架常用
# 格式：变量名字 = {key:value,key1:value2}
person = {'name':'红浪漫','age':18}
print(person)
```

### 2.3 数据类型的查询

在python中，只要定义了一个变量，而且它有数据，那么它的类型就已经确定了，不需要开发者主动的 去说明它的类型，系统会自动辨别。也就是说在使用的时候 变量没有类型，数据才有类型。

```python
# 常用数据类型
# int
# float
# boolean
# string
# list
# tuple
# dict
a = 1
print(a)
print(type(a))

b = 2.12
print(b)
print(type(b))

c = True
print(c)
print(type(c))

d = '中国'
print(d)
print(type(d))

e = [1,2,3,4]
print(e)
print(type(e))

f = (1,2,3,4)
print(f)
print(type(f))

g = {'name':'张三','age':'18'}
print(g)
print(type(g))
```

![image-20211226180652499](https://gitee.com/hhdxw/tuc/raw/master/img/image-20211226180652499.png)

## 3. 标识符和关键字

### 3.1命名规范

计算机编程语言中，标识符是用户编程时使用的名字，用于给变量、常量、函数、语句块等命名，以建立起名称与 使用之间的关系。 

1. 标识符由字母、下划线和数字组成，且数字不能开头。 

2. 严格区分大小写。 

3. 不能使用关键字。 

```python
关键字：
False None True and as assert break class continue def del elif else except finally for from global if import in is lambda nonlocal not or pass raise return try while with yield
```

### 3.2 命名规范

​	驼峰原则

​		大驼峰：userName（常用） 第一个单词以小写字母开始；第二个单词的首字母大写

​		小驼峰：UserName				每一个单字的首字母都采用大写字母

​	下划线：	user_name				用下划线将单词相连 

## 4. 类型转换

|   函数   |        说明         |
| :------: | :-----------------: |
|  int(x)  |  将x对象转换为整型  |
| float(x) |  将x对象转换为浮点  |
|  str(x)  | 将x对象转换为字符串 |
| bool(x)  | 将x对象转换为布尔值 |

1.转换为整型

```python
# str --> int
a = '123'
print(type(a))

b = int(a)

# float --> int
# 如果我们将float转为整数 那么会返回的是小数点前面的数据
c = 1.63
d = int(c)

# boolean --> int
# 强制类型转换为谁 就写什么方法
# True ---> 1   False ---> 0
a = False
b = int(a)
```

![image-20211226210240692](https://gitee.com/hhdxw/tuc/raw/master/img/image-20211226210240692.png)

2.转换为浮点型

```python
# 在爬虫时获取的数据大部分是字符串类型,部分数据需要转换为浮点型，例如价格，数量等。
# string --> float
a = '12.34'
b = float(a)

#int -->float
c = 666
d = float(c)
```

![image-20211226205649057](https://gitee.com/hhdxw/tuc/raw/master/img/image-20211226205649057.png)

3.转换为字符串型

```python
#int -->string
a = 80
b = str(a)

# float --> string
a = 12.32
b = str(a)

# bool --> string
a = True
b = str(a)
```

![image-20211226211031791](https://gitee.com/hhdxw/tuc/raw/master/img/image-20211226211031791.png)

4.转换为布尔类型

```python
# int --> bool
# 非零整数转换为bool， 0为 False, 其他为True
a = 1
a = -1
a = 0

#float --> bool
# 非零数转换为bool， 0为 False, 其他为True
a = 0.0
a = -1.34
a = 1.54

#string --> bool
#非空（包括空格）返回True，空返回False
a = ''
a = ' '
a = '你是我宝贝'

#liat --> bool(列表)
#非空（包括空格）返回True，空返回False	(同string)

#tuple --> bool(元组)
#非空（包括空格）返回True，空返回False	(同string)

#dict --> bool(字典)
#非空（包括空格）返回True，空返回False	(同string)
```

## 5. 运算符

### 5.1 算数运算符

| 运算符 | 描述   | 实例         |
| ------ | ------ | ------------ |
| +      | 加     |              |
| -      | 减     |              |
| *      | 乘     |              |
| /      | 除     | 5/2结果为2.5 |
| //     | 取证   | 9//2 结果为4 |
| %      | 取余   | 9%2结果为1   |
| **     | 指数   | a的b次幂     |
| ()     | 小括号 | 运算级别     |

优先级 	**	高于	*/	%	//	高于	+	-

```python
#特殊类型
#在python中	+	两端只能是同种类型
#在python中	*	可以重复文本(文本*数量)
```

### 5.2 赋值运算符

```python
# 单个变量赋值 
num = 10 

# 同时为多个变量赋值(使用等号连接) 
a = b = 4 

# 多个变量赋值(使用逗号分隔) 
num1, f1, str1 = 100, 3.14, "hello" 
```

### 5.3 复合赋值运算符

| 运算符 |      描述      |          实例           |
| :----: | :------------: | :---------------------: |
|   +=   | 加法赋值运算符 |  c+=a   等效于   c=c+a  |
|   -=   | 减法赋值运算符 |  c-=a   等效于   c=c-a  |
|   *=   | 乘法赋值运算符 |  c*a   等效于   c=c*a   |
|   /=   | 除法赋值运算符 |  c/=a   等效于   c=c/a  |
|  //=   | 取整赋值运算符 | c//=a   等效于   c=c//a |
|   %=   | 取模赋值运算符 |  c%a   等效于   c=c%a   |
|  **=   |  幂赋值运算符  | c**a   等效于   c=c**a  |

### 5.4 比较运算符

| 运算符 |             描述              |        实例         |
| :----: | :---------------------------: | :-----------------: |
|   ==   |     等于:比较对象是否相等     | (a == b) 返回 False |
|   !=   | 不等于:比较两个对象是否不相等 | (a != b) 返回 True  |
|   >    |      大于:返回x是否大于y      | (a > b) 返回 False  |
|  \>=   |  大于等于:返回x是否大于等于y  | (a >= b) 返回 False |
|   <    |      小于:返回x是否小于y      |  (a < b) 返回 True  |
|   <=   |  小于等于:返回x是否小于等于y  | (a <= b) 返回 True  |

### 5.5 逻辑运算符

| 运算符 | 逻辑表达式  |                  描述                   |               实例               |
| :----: | :---------: | :-------------------------------------: | :------------------------------: |
|  and   | x   and   y | 只要有一个运算数是False，结果就是False; | True and True and False--> False |
|   or   | a   or   y  |  只要有一个运算数是True，结果就是True;  |  False or False or True-->True   |
|  not   |   not   x   |                  取反                   |        not True --> False        |

## 6. 输入输出

### 6.1 输出

```python
pirnt("我今年10岁")
pirnt("我今年11岁")

age = 10 
print("我今年%d岁" % age)

age = 18 
name = "红浪漫晶哥" 
print("我的姓名是%s, 年龄是%d" % (name, age))
```

### 6.2 输入

```python
password = input("请输入密码:") 
print('您刚刚输入的密码是:%s' % password)
```

注意事项：

input()的小括号中放入的是提示信息，用来在获取数据之前给用户的一个简单提示 

input()在从键盘获取了数据以后，会存放到等号右边的变量中 

input()会把用户输入的任何值都作为字符串来对待 

## 7. 流程控制语句

### 7.1 if判断语句

```python
age = 30 
if age >= 18: 
	print("我已经成年了")
```

注意：代码的缩进为一个tab键，或者4个空格 

### 7.2 if else 

```python
if 
	条件: 满足条件时的操作 
else:
	不满足条件时的操作
	
age = 18 
if age >= 18: 
    print("我可以去红浪漫了") 
else:
    print("未成年，不允许去"）
```

### 7.3 elif

```python
if xxx1: 
	事情1 
elif xxx2: 
	事情2 
elif xxx3: 
	事情3

score = 77 
if score>=90: 
    print('本次考试，等级为A') 
elif score>=80: 
    print('本次考试，等级为B') 
elif score>=70: 
    print('本次考试，等级为C') 
elif score>=60: 
    print('本次考试，等级为D') 
elif score<60: 
    print('本次考试，等级为E')
```

### 7.4 for循环

```python
for 临时变量 in 列表或者字符串等可迭代对象: 
    循环满足条件时执行的代码
    
for s in "hello": 
    print(s)
    
for i in range(5): 
    print(i)
```

### 7.5 range

```python
range(开始值，终止值，步长)  //左闭右开区间

for x in range(2, 10, 3):
	print(x)
```

## 8. 数据类型高级

### 8.1 字符串高级

|                     |              |                                                              |
| :-----------------: | :----------: | :----------------------------------------------------------: |
|         len         |   获取长度   |                len函数可以获取字符串的长度。                 |
|        find         |   查找内容   | 查找指定内容在字符串中是否存在，如果存在就返回该内容在字符串中第一次 出现的开始位置索引值，如果不存在，则返回-1. |
| startswith,endswith |     判断     |              判断字符串是不是以谁谁谁开头/结尾               |
|        count        | 计算出现次数 |       返回 str在start和end之间 在 mystr里面出现的次数        |
|       replace       |   替换内容   | 替换字符串中指定的内容，如果指定次数count，则替换不会超过count次 |
|        split        |  切割字符串  |                   通过参数的内容切割字符串                   |
|     upper,lower     |  修改大小写  |                    将字符串中的大小写互换                    |
|        strip        |   空格处理   |                            去空格                            |
|        join         |  字符串拼接  |                          字符串拼接                          |

### 8.2列表高级

列表增删改查

|        |                    |
| :----: | :----------------: |
| append |   在末尾添加元素   |
| insert | 在指定位置插入元素 |
| extend |    合并两个列表    |

**append** ：append会把新元素添加到列表末尾 

```python
#定义变量A，默认有3个元素 
A = ['xiaoWang','xiaoZhang','xiaoHua'] 
print("‐‐‐‐‐添加之前，列表A的数据‐‐‐‐‐A=%s" % A) 

#提示、并添加元素 
temp = input('请输入要添加的学生姓名:') 
A.append(temp) 

print("‐‐‐‐‐添加之后，列表A的数据‐‐‐‐‐A=%s" % A)
```

**insert** ：insert(index, object) 在指定位置index前插入元素object 

```python
#[0,1,2,3]
strs = ['a','b','m','s'] 
strs.insert(3,'h') 
print(strs) 
# ['a', 'b', 'm', 'h', 's']
```

**extend** ：通过extend可以将另一个列表中的元素逐一添加到列表中 

```python
a = ['a','b','c'] 
b = ['d','e','f'] 
a.extend(b) 
print(a) # ['a', 'b', 'c', 'd', 'e', 'f'] 将 b 添加到 a 里 
print(b) # ['d','e','f'] b的内容不变
```

