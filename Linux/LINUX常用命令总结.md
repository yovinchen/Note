# LINUX常用命令总结

[TOC]



### 一、查询及帮助命令

#### 1.1 man

语法：man [命令]

实例：`[root@node02 ~]# man ls`

#### 1.2 help

语法：命令 --help

实例：`[root@node02 ~]# ls --help`

### 二、文件和目录操作命令

#### 2.1 ls

功能：是列出目录的内容及其内容属性信息

语法：`ls [-alrtAFR][name...]`

参数：

```javascript
 -a 显示所有文件及目录 (ls内定将文件名或目录名称开头为"."的视为隐藏档，不会列出)-l 除文件名称外，亦将文件型态、权限、拥有者、文件大小等资讯详细列出-r 将文件以相反次序显示(原定依英文字母次序)-t 将文件依建立时间之先后次序列出-A 同 -a ，但不列出 "." (目前目录) 及 ".." (父目录)-F 在列出的文件名称后加一符号；例如可执行档则加 "*", 目录则加 "/"-R 若目录下有文件，则以下之文件亦皆依序列出
```

#### 2.2 cd

功能：功能是从当前工作目录切换到指定的工作目录。

语法：`cd [dirName]`

补充：

"~" 也表示为 home 目录 的意思，"." 则是表示目前所在的目录，".." 则表示目前目录位置的上一层目录。

#### 2.3 cp

功能：复制文件或目录

语法：`cp [options] source dest`

参数：

```javascript
 -a：此选项通常在复制目录时使用，它保留链接、文件属性，并复制目录下的所有内容。其作用等于dpR参数组合。-d：复制时保留链接。这里所说的链接相当于Windows系统中的快捷方式。-f：覆盖已经存在的目标文件而不给出提示。-i：与-f选项相反，在覆盖目标文件之前给出提示，要求用户确认是否覆盖，回答"y"时目标文件将被覆盖。-p：除复制文件的内容外，还把修改时间和访问权限也复制到新文件中。-r：若给出的源文件是一个目录文件，此时将复制该目录下所有的子目录和文件。-l：不复制文件，只是生成链接文件。
```

#### 2.4 find

功能：用于查找目录及目录下的文件。

语法：`find [路径][选项] [操作]`

选项：

```javascript
 -name  根据文件名查找-perm  根据文件权限查找-prune  该选项可以排除某些查找目录-user  根据文件属主查找-group  根据文件属主查找-mtime -n | +n 根据文件更改时间查找-nogroup  查找无效属组的文件-nouser  查找无有效属主的文件-newer file1 ! file2  查找更改时间比file1新但比file2旧IDE文件-type  按文件类型查找-size -n +n 按文件大小查找-mindepth n 从n级子目录开始搜索-maxdepth n  最多搜索到n级子目录
```

实例：

- 查找 /etc 目录下以 conf 结尾的文件，文件名区分大小写

```javascript
 find /etc -name '*.conf'
```

- 查找当前目录下所有文件名为 aa 的文件，文件名不区分大小写

```javascript
 [root@node02 ~]# find . -iname aa./aa./AA[root@node02 ~]# 
```

- 查找文件所属用户为moonrong的所有文件

```javascript
 [root@node02 home]# find . -user moonrong./moonrong./moonrong/.mozilla./moonrong/.mozilla/extensions./moonrong/.mozilla/plugins./moonrong/.bash_logout./moonrong/.bash_profile./moonrong/.bashrc[root@node02 home]# 
```

- 查找文件所属组为moonrong的所有文件

```javascript
 find . -group moonrong
```

　-type　　根据类型查找：如下

```javascript
 f　　 文件　　　　　　　　find . -type fd　　目录　　　　　　　　find . -type dc　　字符设备文件　　　　find . -type cb　　块设备文件　　　　　find . -type bl　　 链接文件　　　　　　find . -type lp　　管道文件　　　　　　find . -type p
```

 **-size**　 根据文件大小查询

```javascript
 -n　　小于 大小为 n 的文件+n　　大于 大小为 n 的文件
```

- 查找 /ect 目录下，小于 10000 字节的文件

```javascript
  find /etc -size +10000c
```

- 查找 /etc 目录下，大于 1M 的文件

```javascript
 find /etc -size -1M
```

 **-mtime** 

```javascript
 -n　　n 天以内修改的文件。+n　　n 天以外修改的文件。n　　正好 n天 修改的文件
```

- 查询 /etc 目录下，5天以内修改 且以 conf 结尾的文件

```javascript
 find /etc -mtime -5 -name '*.conf'
```

查询 /etc 目录下，10天之前修改，且属于moonrong 的文件

```javascript
 find /etc -mtime +10 -user moonrong
```

 **-mmin** 

```javascript
 　-n　　n 分钟以内修改过的文件  +n　　n 分钟之前修改过的文件
```

- 查询 /etc 目录下 30分钟 之前修改过的文件

```javascript
 find /etc -mmin +30
```

- 查询 /etc 目录下 30分钟 之前修改过的目录

```javascript
 find /etc -mmin -30 -type d
```

**-mindepth n**　　从第 n 级目录开始搜索

- 从 /etc 的第三级子目录开始搜索

```javascript
 find /etc -mindepth 3
```

**-maxdepth n**　　表示至多搜索到第 n-1 级子目录。

- 在 /etc 中搜索符合条件的文件，但最多搜索到 2级 子目录

```javascript
 find /etc -maxdepth 3 -name '*.conf'find /etc -type f -name '*.conf' -size +10k -maxdepthc 2
```

- 将目前目录及其子目录下所有最近 20 天内更新过的文件列出

```javascript
 find . -ctime -20
```

- 查找/var/log目录中更改时间在7日以前的普通文件，并在删除之前询问它们：

```javascript
 find /var/log -type f -mtime +7 -ok rm {} \;
```

- 查找前目录中文件属主具有读、写权限，并且文件所属组的用户和其他用户具有读权限的文件：

```javascript
 find . -type f -perm 644 -exec ls -l {} \;
```

- 服务器磁盘满，一般由大的日志文件导致，需找到大文件并删除

```javascript
 find / -size +500M -print0|xargs -0 du -m|sort -nr
```

#### 2.5 mv

功能：用来为文件或目录改名、或将文件或目录移入其它位置

语法：

```javascript
 mv [options] source destmv [options] source... directory
```

参数：

```javascript
 -i: 若指定目录已有同名文件，则先询问是否覆盖旧文件-f: 在 mv 操作要覆盖某已有的目标文件时不给任何指示
```

实例：

- 将文件a.1b改名为a.2b

```javascript
 mv a.1b a.2b
```

- 将info目录放入logs目录中。注意，如果logs目录不存在，则该命令将info改名为logs

```javascript
 mv info/ logs
```

- 将/usr/student下的所有文件和目录移到当前目录下

```javascript
 mv /usr/student/*  . 
```

#### 2.6 pwd

功能：显示当前工作目录的绝对路径

#### 2.7 rm

功能：删除一个或多个文件或目录

语法：

```javascript
 rm [options] name...
```

参数：

```javascript
 -i 删除前逐一询问确认。-f 即使原档案属性设为唯读，亦直接删除，无需逐一确认。-r 将目录及以下之档案亦逐一删除。
```

实例：

- 删除当前目录下的所有文件及目录

```javascript
rm  -r  * 
```

- 删除当前目录下的所有文件及目录，并且是直接删除，无需逐一确认命令行为

```javascript
rm  -rf  要删除的文件名或目录
```

- 删除文件名 test.txt

```javascript
rm  -rf   test.txt
```

- 删除目录 test，不管该目录下是否有子目录或文件，都直接删除

```javascript
rm -rf test/
```

#### 2.8 rmdir

功能：删除空目录

语法：

```javascript
rmdir [-p] dirName
```

参数：

```javascript
-p 是当子目录被删除后使它也成为空目录的话，则顺便一并删除
```

实例：

- 将工作目录下，名为 AAA 的子目录删除

```javascript
rmdir AAA
```

- 在工作目录下的 BBB 目录中，删除名为 Test 的子目录。若 Test 删除后，BBB 目录成为空目录，则 BBB 亦予删除

```javascript
rmdir -p BBB/Test
```

#### 2.9 mkdir

功能：创建目录

语法：

```javascript
mkdir [-p] dirName
```

参数：

```javascript
-p 确保目录名称存在，不存在的就建一个
```

实例：

- 在工作目录下，建立一个名为 AAA 的子目录

```javascript
mkdir AAA
```

- 在工作目录下的 BBB 目录中，建立一个名为 Test 的子目录。 若 BBB 目录原本不存在，则建立一个

```javascript
mkdir -p BBB/Test
```

#### 2.10 touch

功能：创建新的空文件，改变已有文件的时间戳属性

语法：

```javascript
touch [-acfm][-d<日期时间>][-r<参考文件或目录>] [-t<日期时间>][--help][--version][文件或目录…]
```

参数：

```javascript
a 改变档案的读取时间记录。
m 改变档案的修改时间记录。
c 假如目的档案不存在，不会建立新的档案。与 --no-create 的效果一样。
f 不使用，是为了与其他 unix 系统的相容性而保留。
r 使用参考档的时间记录，与 --file 的效果一样。
d 设定时间与日期，可以使用各种不同的格式。
t 设定档案的时间记录，格式与 date 指令相同。
--no-create 不会建立新档案。
--help 列出指令格式。
--version 列出版本讯息。
```

实例：

- 使用指令"touch"修改文件"testfile"的时间属性为当前系统时间

```javascript
touch testfile
```

- 创建一个名为“file”的新的空白文件

```javascript
touch file
```

#### 2.11 tree

功能：以树形结构显示目录下的内容

语法：

```javascript
tree [-aACdDfFgilnNpqstux][-I <范本样式>][-P <范本样式>][目录...]
```

参数

```javascript
-a 显示所有文件和目录。
-A 使用ASNI绘图字符显示树状图而非以ASCII字符组合。
-C 在文件和目录清单加上色彩，便于区分各种类型。
-d 显示目录名称而非内容。
-D 列出文件或目录的更改时间。
-f 在每个文件或目录之前，显示完整的相对路径名称。
-F 在执行文件，目录，Socket，符号连接，管道名称名称，各自加上"*","/","=","@","|"号。
-g 列出文件或目录的所属群组名称，没有对应的名称时，则显示群组识别码。
-i 不以阶梯状列出文件或目录名称。
-I<范本样式> 不显示符合范本样式的文件或目录名称。
-l 如遇到性质为符号连接的目录，直接列出该连接所指向的原始目录。
-n 不在文件和目录清单加上色彩。
-N 直接列出文件和目录名称，包括控制字符。
-p 列出权限标示。
-P<范本样式> 只显示符合范本样式的文件或目录名称。
-q 用"?"号取代控制字符，列出文件和目录名称。
-s 列出文件或目录大小。
-t 用文件和目录的更改时间排序。
-u 列出文件或目录的拥有者名称，没有对应的名称时，则显示用户识别码。
-x 将范围局限在现行的文件系统中，若指定目录下的某些子目录，其存放于另一个文件系统上，则将该子目录予以排除在寻找范围外。
```

#### 2.12 file

功能：辨识文件类型

语法：`file [-bcLvz][-f <名称文件>][-m <魔法数字文件>...][文件或目录...]`

参数：

```javascript
-b 　列出辨识结果时，不显示文件名称。
-c 　详细显示指令执行过程，便于排错或分析程序执行的情形。
-f<名称文件> 　指定名称文件，其内容有一个或多个文件名称时，让file依序辨识这些文件，格式为每列一个文件名称。
-L 　直接显示符号连接所指向的文件的类别。
-m<魔法数字文件> 　指定魔法数字文件。
-v 　显示版本信息。
-z 　尝试去解读压缩文件的内容。
[文件或目录...] 要确定类型的文件列表，多个文件之间使用空格分开，可以使用shell通配符匹配多个文件。
```

### 三、查看文件及内容处理

#### 3.1 cat

功能：cat 命令用于连接文件并打印到标准输出设备上

语法：

```javascript
cat [-AbeEnstTuv] [--help] [--version] fileName
```

参数：

```javascript
-n 或 --number：由 1 开始对所有输出的行数编号。
-b 或 --number-nonblank：和 -n 相似，只不过对于空白行不编号。
-s 或 --squeeze-blank：当遇到有连续两行以上的空白行，就代换为一行的空白行。
-v 或 --show-nonprinting：使用 ^ 和 M- 符号，除了 LFD 和 TAB 之外。
-E 或 --show-ends : 在每行结束处显示 $。
-T 或 --show-tabs: 将 TAB 字符显示为 ^I。
-A, --show-all：等价于 -vET。
-e：等价于"-vE"选项；
-t：等价于"-vT"选项；
```

实例：

- 把 textfile1 的文档内容加上行号后输入 textfile2 这个文档里：

```javascript
cat -n textfile1 > textfile2
```

- 把 textfile1 和 textfile2 的文档内容加上行号（空白行不加）之后将内容附加到 textfile3 文档里：

```javascript
cat -b textfile1 textfile2 >> textfile3
```

- 清空 /etc/test.txt 文档内容：

```javascript
cat /dev/null > /etc/test.txt
```

#### 3.2 tac

功能：反向显示文件内容

#### 3.3 more

功能：分页显示文件内容

语法：

```javascript
more [-dlfpcsu] [-num] [+/pattern] [+linenum] [fileNames..]
```

参数：

```javascript
-num 一次显示的行数
-d 提示使用者，在画面下方显示 [Press space to continue, 'q' to quit.] ，如果使用者按错键，则会显示 [Press 'h' for instructions.] 而不是 '哔' 声
-l 取消遇见特殊字元 ^L（送纸字元）时会暂停的功能
-f 计算行数时，以实际上的行数，而非自动换行过后的行数（有些单行字数太长的会被扩展为两行或两行以上）
-p 不以卷动的方式显示每一页，而是先清除萤幕后再显示内容
-c 跟 -p 相似，不同的是先显示内容再清除其他旧资料
-s 当遇到有连续两行以上的空白行，就代换为一行的空白行
-u 不显示下引号 （根据环境变数 TERM 指定的 terminal 而有所不同）
+/pattern 在每个文档显示前搜寻该字串（pattern），然后从该字串之后开始显示
+num 从第 num 行开始显示
fileNames 欲显示内容的文档，可为复数个数
```

实例：

- 逐页显示 testfile 文档内容，如有连续两行以上空白行则以一行空白行显示。

```javascript
more -s testfile
```

- 从第 20 行开始显示 testfile 之文档内容。

```javascript
more +20 testfile
```

#### 3.4 less

功能：分页显示文件内容，与more相反

用 less 可以随意浏览文件，而 more 仅能向前移动，却不能向后移动，而且 less 在查看之前不会加载整个文件

语法：

```javascript
less [参数] 文件 
```

参数：

```javascript
-b <缓冲区大小> 设置缓冲区的大小
-e 当文件显示结束后，自动离开
-f 强迫打开特殊文件，例如外围设备代号、目录和二进制文件
-g 只标志最后搜索的关键词
-i 忽略搜索时的大小写
-m 显示类似more命令的百分比
-N 显示每行的行号
-o <文件名> 将less 输出的内容在指定文件中保存起来
-Q 不使用警告音
-s 显示连续空行为一行
-S 行过长时间将超出部分舍弃
-x <数字> 将"tab"键显示为规定的数字空格
/字符串：向下搜索"字符串"的功能
?字符串：向上搜索"字符串"的功能
n：重复前一个搜索（与 / 或 ? 有关）
N：反向重复前一个搜索（与 / 或 ? 有关）
b 向后翻一页
d 向后翻半页
h 显示帮助界面
Q 退出less 命令
u 向前滚动半页
y 向前滚动一行
空格键 滚动一页
回车键 滚动一行
```

[pagedown]

： 向下翻动一页

[pageup]

： 向上翻动一页

实例：

- 查看文件

```javascript
cat vmware-vmusr.log
```

- ps查看进程信息并通过less分页显示

```javascript
ps -ef |less
```

- 查看命令历史使用记录并通过less分页显示

```javascript
history | less
```

#### 3.5 head

功能：head命令以行为单位，取文件的内容,后面不接参数时默认打印前10行

语法：

```javascript
head [参数] [文件]
```

参数：

```javascript
-n	后面接数字，代表显示几行的意思
-c	指定显示头部内容的字符数
-v	总是显示文件名的头信息
-q	不显示文件名的头信息
```

实例：

- 显示前5行文件内容

```javascript
head -n 5 file
```

- 显示文件名信息，并显示文件前两行

```javascript
head -v -n 2 test.txt 
```

- 显示文件前5个字符

```javascript
head -c 5 test.txt
```

#### 3.6 tail

功能：显示文件尾部的内容，默认在屏幕上显示指定文件的末尾10行

语法：

```javascript
tail [参数] [文件]  
```

参数：

```javascript
-f 循环读取
-q 不显示处理信息
-v 显示详细的处理信息
-c<数目> 显示的字节数
-n<行数> 显示文件的尾部 n 行内容
--pid=PID 与-f合用,表示在进程ID,PID死掉之后结束
-q, --quiet, --silent 从不输出给出文件名的首部
-s, --sleep-interval=S 与-f合用,表示在每次反复的间隔休眠S秒
```

实例：

- 一直变化的文件总是显示后10行

```javascript
 tail -f 10 file
```

- 显示文件file的最后10行

```javascript
tail file
```

- 显示文件file的内容，从第20行至文件末尾

```javascript
tail +20 file 
```

- 显示文件file的最后10个字符

```javascript
tail -c 10 file
```

#### 3.7 cut

功能：两个主要的功能，第一是显示文件内容，第二是连接多个或多个文件。

 若不指定file参数，该命令将读取标准输入。 必须指定 -b、-c 或 -f 标志之一。

语法：

```javascript
cut [参数] [文件]
```

参数：

```javascript
-b	以字节为单位进行分割 ,仅显示行中指定直接范围的内容
-c	以字符为单位进行分割 , 仅显示行中指定范围的字符
-d	自定义分隔符，默认为制表符”TAB”
-f	显示指定字段的内容 , 与-d一起使用
-n	取消分割多字节字符
--complement	补足被选择的字节、字符或字段
--out-delimiter	指定输出内容是的字段分割符
```

#### 3.8 wc

功能：统计文件的行数、单词数或字节数

语法：

```javascript
wc [参数] [文件]
```

参数：

```javascript
-w	统计字数，或--words：只显示字数。一个字被定义为由空白、跳格或换行字符分隔的字符串
-c	统计字节数，或--bytes或--chars：只显示Bytes数
-l	统计行数，或--lines：只显示列数
-m	统计字符数
-L	打印最长行的长度
```

实例：

- 统计字数

```javascript
wc -w test.txt 
```

- 统计字节数

```javascript
wc -c test.txt
```

- 统计字符数

```javascript
wc -m test.txt
```

- 统计行数

```javascript
wc -l test.txt
```

- 打印最长行的长度

```javascript
wc -L test.txt
```

#### 3.9 grep

功能：强大的文本搜索工具

linux系统支持三种形式的grep命令，大儿子就是grep，标准，模仿的代表。二儿子兴趣爱好多-egrep，简称扩展grep命令，其实和grep -E等价，支持基本和扩展的正则表达式。小儿子跑的最快-fgrep，简称快速grep命令，其实和grep -F等价，不支持正则表达式，按照字符串表面意思进行匹配。

语法：

```javascript
grep [参数]
```

参数：

```javascript
-i	搜索时，忽略大小写
-c	只输出匹配行的数量
-l	只列出符合匹配的文件名，不列出具体的匹配行
-n	列出所有的匹配行，显示行号
-h	查询多文件时不显示文件名
-s	不显示不存在、没有匹配文本的错误信息
-v	显示不包含匹配文本的所有行
-w	匹配整词
-x	匹配整行
-r	递归搜索
-q	禁止输出任何结果，已退出状态表示搜索是否成功
-b	打印匹配行距文件头部的偏移量，以字节为单位
-o	与-b结合使用，打印匹配的词据文件头部的偏移量，以字节为单位
```

实例：

- 支持多文件查询并支持使用通配符

```javascript
grep zwx file_* /etc/hosts
```

待完善

#### 3.10 tr

功能：将字符进行替换、压缩、删除

语法：

```javascript
tr [参数] [字符串1] [字符串2]
```

参数：

```javascript
-c	选定字符串1中字符集的补集，即反选字符串1的补集
-d	删除字符串1中出现的所有字符
-s	删除所有重复出现的字符序列，只保留一个
```

实例：

- 实现大小写字母的互换

```javascript
tr "[a-z]" "[A-Z]" <file_1
```

- 删除file_1中的小写字母

```javascript
tr -d "[a-z]" <file_1
```

- 压缩重复的空白行

```javascript
tr -s "[\n]" <file_2
```

### 四、文件压缩及解压缩命令

#### 4.1 tar

功能：用来建立，还原备份文件的工具程序，它可以加入，解开备份文件内的文件

参数：

```javascript
-c: 建立压缩档案 
-x：解压 
-t：查看内容 
-r：向压缩归档文件末尾追加文件 
-u：更新原压缩包中的文件
```

这五个是独立的命令，压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个。下面的参数是根据需要在压缩或解压档案时可选的。

```javascript
-z：有gzip属性的 
-j：有bz2属性的 
-Z：有compress属性的 
-v：显示所有过程 
-O：将文件解开到标准输出 
```

下面的参数 -f 是必须的

```javascript
-f: 使用档案名字，切记，这个参数是最后一个参数，后面只能接档案名。 
```

实例：

- 将所有 .jpg 的文件打成一个名为 all.tar 的包。-c 是表示产生新的包，-f 指定包的文件名。

```javascript
tar -cf all.tar *.jpg
```

- 将所有 .gif 的文件增加到 all.tar 的包里面去。-r 是表示增加文件的意思

```javascript
 tar -rf all.tar *.gif 
```

- 更新原来 tar 包 all.tar 中 logo.gif 文件，-u 是表示更新文件的意思

```javascript
tar -uf all.tar logo.gif
```

- 列出 all.tar 包中所有文件，-t 是列出文件的意思

```javascript
tar -tf all.tar
```

- 解出 all.tar 包中所有文件，-x 是解开的意思

```javascript
tar -xf all.tar
```

压缩实例：

- 将目录里所有jpg文件打包成 tar.jpg

```javascript
tar –cvf jpg.tar *.jpg
```

- 将目录里所有jpg文件打包成 jpg.tar 后，并且将其用 gzip 压缩，生成一个 gzip 压缩过的包，命名为 jpg.tar.gz

```javascript
tar –czf jpg.tar.gz *.jpg
```

- 将目录里所有jpg文件打包成 jpg.tar 后，并且将其用 bzip2 压缩，生成一个 bzip2 压缩过的包，命名为jpg.tar.bz2

```javascript
tar –cjf jpg.tar.bz2 *.jpg
```

- 将目录里所有 jpg 文件打包成 jpg.tar 后，并且将其用 compress 压缩，生成一个 umcompress 压缩过的包，命名为jpg.tar.Z

```javascript
tar –cZf jpg.tar.Z *.jpg
```

解压实例：

```javascript
tar –xvf file.tar         // 解压 tar 包 
tar -xzvf file.tar.gz     // 解压 tar.gz 
tar -xjvf file.tar.bz2    // 解压 tar.bz2 
tar –xZvf file.tar.Z      // 解压 tar.Z 
unrar e file.rar          // 解压 rar 
unzip file.zip            // 解压 zip
```

#### 4.2 unzip

功能：用于.zip格式文件的解压缩工具 ，unzip命令将列出、测试或从zip格式存档中提取文件，这些文件通常位于MS-DOS系统上。

语法：

```javascript
unzip [参数] [文件]
```

参数：

```javascript
-l	显示压缩文件内所包含的文件
-v	执行时显示详细的信息
-c	将解压缩的结果显示到屏幕上，并对字符做适当的转换
-n	解压缩时不要覆盖原有的文件
-j	不处理压缩文件中原有的目录路径
```

实例：

- 把/home目录下面的mydata.zip解压到mydatabak目录里面

```javascript
unzip mydata.zip -d mydatabak
```

- 把/home目录下面的wwwroot.zip直接解压到/home目录里面

```javascript
unzip wwwroot.zip
```

- 把/home目录下面的abc12.zip、abc23.zip、abc34.zip同时解压到/home目录里面

```javascript
 unzip abc\*.zip
```

- 查看把/home目录下面的wwwroot.zip里面的内容

```javascript
unzip -v wwwroot.zip
```

- 验证/home目录下面的wwwroot.zip是否完整

```javascript
unzip -t wwwroot.zip
```

#### 4.3 gzip

功能：压缩和解压文件

gzip是在Linux系统中经常使用的一个对文件进行压缩和解压缩的命令，既方便又好用。gzip不仅可以用来压缩大的、较少使用的文件以节省磁盘空间，还可以和tar命令一起构成Linux操作系统中比较流行的压缩文件格式。据统计，gzip命令对文本文件有60%～70%的压缩率。减少文件大小有两个明显的好处，一是可以减少存储空间，二是通过网络传输文件时，可以减少传输的时间。

语法：

```javascript
gzip [参数]
```

参数：

```javascript
-a	使用ASCII文字模式
-d	解开压缩文件
-f	强行压缩文件
-l	列出压缩文件的相关信息
-c	把压缩后的文件输出到标准输出设备，不去更动原始文件
-r	递归处理，将指定目录下的所有文件及子目录一并处理
-q	不显示警告信息
```

实例：

- 把当前目录下的每个文件压缩成.gz文件

```javascript
gzip *
```

- 上例中每个压缩的文件解压，并列出详细的信息

```javascript
gzip -dv *
```

#### 4.4 zip

功能：压缩工具

zip程序将一个或多个压缩文件与有关文件的信息(名称、路径、日期、上次修改的时间、保护和检查信息以验证文件完整性)一起放入一个压缩存档中。可以使用一个命令将整个目录结构打包到zip存档中。

对于文本文件来说，压缩比为2：1和3：1是常见的。zip只有一种压缩方法(通缩)，并且可以在不压缩的情况下存储文件。(如果添加了bzip 2支持，zip也可以使用bzip 2压缩，但这些条目需要一个合理的现代解压缩来解压缩。当选择bzip 2压缩时，它将通货紧缩替换为默认方法。)zip会自动为每个要压缩的文件选择更好的两个文件(通缩或存储，如果选择bzip2，则选择bzip2或Store)。

语法：

```javascript
zip [参数] [文件]
```

参数：

```javascript
-q	不显示指令执行过程
-r	递归处理，将指定目录下的所有文件和子目录一并处理
-z	替压缩文件加上注释
-v	显示指令执行过程或显示版本信息
-n<字尾字符串>	不压缩具有特定字尾字符串的文件
```

实例：

- 将 /home/html/ 这个目录下所有文件和文件夹打包为当前目录下的 html.zip

```javascript
zip -q -r html.zip /home/html
```

- 压缩文件 cp.zip 中删除文件 a.c

```javascript
zip -dv cp.zip a.c
```

- 把/home目录下面的mydata目录压缩为mydata.zip

```javascript
zip -r mydata.zip mydata
```

- 把/home目录下面的abc文件夹和123.txt压缩成为abc123.zip

```javascript
zip -r abc123.zip abc 123.txt
```

- 将 logs目录打包成 log.zip

```javascript
zip -r log.zip ./logs
```

### 五、信息显示命令

#### 5.1 uname

功能：用于显示系统相关信息，比如主机名、内核版本号、硬件架构等。 如果未指定任何选项，其效果相当于执行”uname -s”命令，即显示系统内核的名字。

语法：

```javascript
uname [参数]
```

参数：

```javascript
-a	显示系统所有相关信息
-m	显示计算机硬件架构
-n	显示主机名称
-r	显示内核发行版本号
-s	显示内核名称
-v	显示内核版本
-p	显示主机处理器类型
-o	显示操作系统名称
-i	显示硬件平台
```

实例：

- 显示系统主机名、内核版本号、CPU类型等信息

```javascript
uname -a
```

- 仅显示系统主机名

```javascript
uname -n
```

- 显示当前系统的内核版本

```javascript
uname -r
```

#### 5.2 hostname

功能：显示和设置系统的主机名

在使用hostname命令设置主机名后，系统并不会永久保存新的主机名，重新启动机器之后还是原来的主机名。如果需要永久修改主机名，需要同时修改/etc/hosts和/etc/sysconfig/network的相关内容

语法：

```javascript
hostname [参数]
```

参数：

```javascript
-a	显示主机别名
-d	显示DNS域名
-f	显示FQDN名称
-i	显示主机的ip地址
-s	显示短主机名称，在第一个点处截断
-y	显示NIS域名
```

实例：

- 使用-a参数显示主机别名

```javascript
hostname -a
```

- 使用-i参数显示主机的ip地址

```javascript
hostname -i
```

#### 5.3 dmesg

功能：显示开机信息，用于诊断系统故障

语法：

```javascript
dmesg [参数]
```

参数：

```javascript
-c	显示信息后，清除ring buffer中的内容
-s <缓冲区大小>	预设置为8196，刚好等于ring buffer的大小
-n	设置记录信息的层级
```

实例：

- 搜索开机信息的关键词

```javascript
dmesg | grep sda
```

- 忽略大小写搜索关键词

```javascript
dmesg | grep -i memory
```

- 显示开机信息的前20行

```javascript
dmesg | head -20
```

- 显示开机信息的最后20行

```javascript
dmesg | tail -20
```

- 清空dmesg环形缓冲区中的日志

```javascript
dmesg -c
```

#### 5.4 uptime

功能：获取主机运行时间和查询Linux系统负载等信息

uptime命令可以显示系统已经运行了多长时间，信息显示依次为：现在时间、系统已经运行了多长时间、目前有多少登录用户、系统在过去的1分钟、5分钟和15分钟内的平均负载。

语法：

```javascript
uptime [参数]
```

参数：

```javascript
-p	以漂亮的格式显示机器正常运行的时间
-s	系统自开始运行时间，格式为yyyy-mm-dd hh:mm:ss
-h	显示帮助信息
```

实例：

- 显示当前系统运行负载情况

```javascript
uptime
```

- 使用-p参数显示机器正常运行的时间

```javascript
uptime -p
```

- 使用-s参数显示机器启动时间

```javascript
uptime -s
```

#### 5.5 stat

功能：显示文件或文件系统的详细信息

语法：

```javascript
stat [参数]
```

参数：

```javascript
-L	支持符号链接
-f	显示文件系统的信息
-t	以简洁的方式输出
--help	显示命令帮助信息
--version	显示命令版本信息
```

实例：

- 查看该文件或目录的状态Access，Modify，Change

```javascript
stat myfile
```

- 查看文件系统信息

```javascript
stat -f myfile
```

#### 5.6 du

功能：查看磁盘占用空间，与df不同，不是分区

语法：

```javascript
du [参数] [文件]
```

参数：

```javascript
-a	显示目录中所有文件大小
-k	以KB为单位显示文件大小
-m	以MB为单位显示文件大小
-g	以GB为单位显示文件大小
-h	以易读方式显示文件大小
-s	仅显示总计
```

#### 5.7 df

功能：报告文件系统磁盘空间的使用情况

语法：

```javascript
df [参数] [指定文件]
```

参数：

```javascript
-a	显示所有系统文件
-B <块大小>	指定显示时的块大小
-h	以容易阅读的方式显示
-H	以1000字节为换算单位来显示
-i	显示索引字节信息
-k	指定块大小为1KB
-l	只显示本地文件系统
-t <文件系统类型>	只显示指定类型的文件系统
-T	输出时显示文件系统类型
-- -sync	在取得磁盘使用信息前，先执行sync命令
```

实例：

- 显示磁盘分区使用情况

```javascript
[root@ecs-d2e4 ~]# df
Filesystem     1K-blocks     Used Available Use% Mounted on
/dev/xvda2      37024320 12876632  22260304  37% /
devtmpfs         1919268        0   1919268   0% /dev
tmpfs            1723972        0   1723972   0% /dev/shm
tmpfs            1723972   188820   1535152  11% /run
tmpfs            1723972        0   1723972   0% /sys/fs/cgroup
tmpfs             334148        0    334148   0% /run/user/0
```

[root@ecs-d2e4 ~]

\#

- 以容易阅读的方式显示磁盘分区使用情况

```javascript
[root@ecs-d2e4 ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda2       36G   13G   22G  37% /
devtmpfs        1.9G     0  1.9G   0% /dev
tmpfs           1.7G     0  1.7G   0% /dev/shm
tmpfs           1.7G  185M  1.5G  11% /run
tmpfs           1.7G     0  1.7G   0% /sys/fs/cgroup
tmpfs           327M     0  327M   0% /run/user/0
```

[root@ecs-d2e4 ~]

\#

- 显示文件类型为ext3的磁盘使用情况

```javascript
[root@ecs-d2e4 ~]# df -t ext3
Filesystem     1K-blocks     Used Available Use% Mounted on
/dev/xvda2      37024320 12876660  22260276  37% /
```

- 显示指定文件所在分区的磁盘使用情况

```javascript
[root@ecs-d2e4 ~]# df /etc/dhcp
Filesystem     1K-blocks     Used Available Use% Mounted on
/dev/xvda2      37024320 12876660  22260276  37% /
```

[root@ecs-d2e4 ~]

\#

#### 5.8 top

功能：实时显示系统资源使用情况

语法：

```javascript
top [参数]
```

参数：

```javascript
-d	改变显示的更新速度，或是在交谈式指令列( interactive command)按 s
-q	没有任何延迟的显示速度，如果使用者是有 superuser 的权限，则 top 将会以最高的优先序执行
-c 	切换显示模式
-s	安全模式，将交谈式指令取消, 避免潜在的危机
-i	不显示任何闲置 (idle) 或无用 (zombie) 的行程
-n	更新的次数，完成后将会退出 top
-b	批次档模式，搭配 “n” 参数一起使用，可以用来将 top 的结果输出到档案内
```

实例：

- 显示完整的进程信息

```javascript
[root@ecs-d2e4 ~]# top -c
top - 22:14:19 up 80 days,  6:01,  1 user,  load average: 0.00, 0.01, 0.05
Tasks: 119 total,   1 running, 118 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.2 sy,  0.0 ni, 99.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  3341448 total,   921024 free,   584604 used,  1835820 buff/cache
KiB Swap:  4194300 total,  4194300 free,        0 used.  2226764 avail Mem 
```

- 以批处理模式显示程序信息

```javascript
[root@ecs-d2e4 ~]# top -b
top - 22:16:23 up 80 days,  6:03,  1 user,  load average: 0.00, 0.01, 0.05
Tasks: 119 total,   1 running, 118 sleeping,   0 stopped,   0 zombie
%Cpu(s):  1.3 us,  0.1 sy,  0.0 ni, 98.6 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  3341448 total,   921580 free,   583984 used,  1835884 buff/cache
KiB Swap:  4194300 total,  4194300 free,        0 used.  2227360 avail Mem 
```

- 以累积模式显示程序信息

```javascript
[root@ecs-d2e4 ~]# top -s
top - 22:19:46 up 80 days,  6:06,  1 user,  load average: 0.00, 0.01, 0.05
Tasks: 119 total,   1 running, 118 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni, 99.8 id,  0.2 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  3341448 total,   921244 free,   584292 used,  1835912 buff/cache
KiB Swap:  4194300 total,  4194300 free,        0 used.  2227004 avail Mem 
```

- 设置信息更新次数

```javascript
[root@ecs-d2e4 ~]# top -n 2
top - 22:20:46 up 80 days,  6:07,  1 user,  load average: 0.00, 0.01, 0.05
Tasks: 121 total,   1 running, 120 sleeping,   0 stopped,   0 zombie
%Cpu(s):  1.3 us,  0.1 sy,  0.0 ni, 98.6 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  3341448 total,   919160 free,   586324 used,  1835964 buff/cache
KiB Swap:  4194300 total,  4194300 free,        0 used.  2224968 avail Mem 
```

#### 5.9 free

功能：显示系统内存情况

语法：

```javascript
free [参数]
```

参数：

```javascript
-b	以Byte显示内存使用情况
-k	以kb为单位显示内存使用情况
-m	以mb为单位显示内存使用情况
-g	以gb为单位显示内存使用情况
-s	持续显示内存
-t	显示内存使用总合
```

实例：

- 以总和的形式显示内存的使用信息

```javascript
[root@ecs-d2e4 ~]# free -t
              total        used        free      shared  buff/cache   available
Mem:        3341448      583832      921472      205696     1836144     2227400
Swap:       4194300           0     4194300
Total:      7535748      583832     5115772
```

- 周期性查询内存使用情况

```javascript
[root@ecs-d2e4 ~]# free -s 10
              total        used        free      shared  buff/cache   available
Mem:        3341448      583232      922064      205696     1836152     2228000
Swap:       4194300           0     4194300

              total        used        free      shared  buff/cache   available
Mem:        3341448      583976      921320      205696     1836152     2227256
Swap:       4194300           0     4194300

              total        used        free      shared  buff/cache   available
Mem:        3341448      584100      921196      205696     1836152     2227132
Swap:       4194300           0     4194300
……
```

#### 5.10 date

功能：显示日期与时间

语法：

```javascript
date [选项] [+输出形式]
```

参数：

```javascript
-d datestr	显示 datestr 中所设定的时间 (非系统时间)
-s datestr	将系统时间设为 datestr 中所设定的时间
-u	显示目前的格林威治时间
--help	显示帮助信息
--version	显示版本编号
```

实例：

- 显示当前时间

```javascript
[root@ecs-d2e4 ~]# date
Sun Jan 26 22:33:55 CST 2020
```

[root@ecs-d2e4 ~]

\# date '+%c' Sun 26 Jan 2020 10:34:21 PM CST

[root@ecs-d2e4 ~]

\#

- 显示时间后跳行，再显示目前日期

```javascript
[root@ecs-d2e4 ~]# date '+%T%n%D'
22:36:53
01/26/20
```

- 显示月份与日数

```javascript
[root@ecs-d2e4 ~]# date '+%B %d' 
January 26
```

- 显示日期与设定时间(12:34:56)

```javascript
[root@ecs-d2e4 ~]# date --date '12:34:56'
Sun Jan 26 12:34:56 CST 2020
```

#### 5.11 cal

功能：显示日历

语法：

```javascript
cal [参数] [月份] [年份]
```

参数：

```javascript
-l	单月分输出日历
-3	显示最近三个月的日历
-s	将星期天作为月的第一天
-m	将星期一作为月的第一天
-j	显示在当年中的第几天（儒略日）
-y	显示当年的日历
```

实例：

- 显示当前月份的日历

```javascript
[root@ecs-d2e4 ~]# cal
    January 2020    
Su Mo Tu We Th Fr Sa
          1  2  3  4
 5  6  7  8  9 10 11
12 13 14 15 16 17 18
19 20 21 22 23 24 25
26 27 28 29 30 31
```

- 显示近期三个月的日历(当前月，上一个月和下一个月)

```javascript
[root@ecs-d2e4 ~]# cal -3
    December 2019         January 2020          February 2020   
Su Mo Tu We Th Fr Sa  Su Mo Tu We Th Fr Sa  Su Mo Tu We Th Fr Sa
 1  2  3  4  5  6  7            1  2  3  4                     1
 8  9 10 11 12 13 14   5  6  7  8  9 10 11   2  3  4  5  6  7  8
15 16 17 18 19 20 21  12 13 14 15 16 17 18   9 10 11 12 13 14 15
22 23 24 25 26 27 28  19 20 21 22 23 24 25  16 17 18 19 20 21 22
29 30 31              26 27 28 29 30 31     23 24 25 26 27 28 29
```

- 显示指定年月的日历，如显示2020年2月的日历

```javascript
[root@ecs-d2e4 ~]# cal 2 2020
    February 2020   
Su Mo Tu We Th Fr Sa
                   1
 2  3  4  5  6  7  8
 9 10 11 12 13 14 15
16 17 18 19 20 21 22
23 24 25 26 27 28 29
```

### 六、搜索文件命令

#### 6.1 which

功能：在PATH变量指定的路径中，搜索某个系统命令的位置，并且返回第一个搜索结果

实例：

```javascript
[root@lvs ~]# which nginx
/usr/sbin/nginx
```

[root@lvs ~]

\#

#### 6.2 find

功能：从磁盘遍历查找文件或目录。

find命令用来在指定目录下查找文件。任何位于参数之前的字符串都将被视为欲查找的目录名。如果使用该命令时，不设置任何参数，则find命令将在当前目录下查找子目录与文件。并且将查找到的子目录和文件全部进行显示。

语法：

```javascript
find   path   -option   [   -print ]   [ -exec   -ok   command ]   {} \;
```

参数：

```javascript
find 根据下列规则判断 path 和 expression，在命令列上第一个 - ( ) , ! 之前的部份为 path，之后的是 expression。如果 path 是空字串则使用目前路径，如果 expression 是空字串则使用 -print 为预设 expression。

expression 中可使用的选项有二三十个之多，在此只介绍最常用的部份。
-mount, -xdev : 只检查和指定目录在同一个文件系统下的文件，避免列出其它文件系统中的文件
-amin n : 在过去 n 分钟内被读取过
-anewer file : 比文件 file 更晚被读取过的文件
-atime n : 在过去n天内被读取过的文件
-cmin n : 在过去 n 分钟内被修改过
-cnewer file :比文件 file 更新的文件
-ctime n : 在过去n天内被修改过的文件
-empty : 空的文件-gid n or -group name : gid 是 n 或是 group 名称是 name
-ipath p, -path p : 路径名称符合 p 的文件，ipath 会忽略大小写
-name name, -iname name : 文件名称符合 name 的文件。iname 会忽略大小写
-size n : 文件大小 是 n 单位，b 代表 512 位元组的区块，c 表示字元数，k 表示 kilo bytes，w 是二个位元组。-type c : 文件类型是 c 的文件。
```

实例：

- 按文件名查找：

```javascript
root@192 ~]# find /etc /usr /home -name "ifcfg-en*"
/etc/sysconfig/network-scripts/ifcfg-ens33
```

[root@192 ~]

\#

- 找大于5M的，找到后并列出来

```javascript
[root@LVS ~]# find /etc -size +5M -ls
101709377 7120 -r--r--r--   1 root     root      7289802 7月  5  2017 /etc/udev/hwdb.bin
```

找修改时间小于5天的文件

```javascript
[root@LVS ~]# find /etc -mtime -5
/etc
/etc/resolv.conf
/etc/sysconfig
```

#### 6.3 whereis

功能：查找二进制命令，按环境变量 PATH路径查找。

该指令会在特定目录中查找符合条件的文件。这些文件应属于原始代码、二进制文件，或是帮助文件。

该指令只能用于查找二进制文件、源代码文件和man手册页，一般文件的定位需使用locate命令。

语法：

```javascript
whereis [-bfmsu][-B <目录>...][-M <目录>...][-S <目录>...][文件...]
```

参数：

```javascript
-b 　只查找二进制文件。
-B<目录> 　只在设置的目录下查找二进制文件。
-f 　不显示文件名前的路径名称。
-m 　只查找说明文件。
-M<目录> 　只在设置的目录下查找说明文件。
-s 　只查找原始代码文件。
-S<目录> 　只在设置的目录下查找原始代码文件。
-u 　查找不包含指定类型的文件。
```

实例：

- 查看指令"bash"的位置

```javascript
[root@LVS ~]# whereis bash
bash: /usr/bin/bash /usr/share/man/man1/bash.1.gz
```

[root@LVS ~]

\#

- 只查二进制文件

```javascript
[root@LVS ~]# whereis -b bash 
bash: /usr/bin/bash
```

[root@LVS ~]

\#

#### 6.4 locate

功能：从[数据库](https://cloud.tencent.com/solution/database?from=10680) (/var/lib/mlocate/mlocate.db) 查找命令，使用 updatedb 更新库。

语法：

```javascript
locate [-d ][--help][--version][范本样式...]
```

实例：

```javascript
[root@LVS ~]# locate passwd
/etc/passwd
/etc/passwd-
/etc/pam.d/passwd
/etc/security/opasswd
```

### 七、用户管理命令

#### 7.1 useradd

功能：用于建立用户帐号

语法：

```javascript
useradd [-mMnr][-c <备注>][-d <登入目录>][-e <有效期限>][-f <缓冲天数>][-g <群组>][-G <群组>][-s <shell>][-u <uid>][用户帐号]
```

或

```javascript
useradd -D [-b][-e <有效期限>][-f <缓冲天数>][-g <群组>][-G <群组>][-s <shell>]
```

参数：

```javascript
-c<备注> 　加上备注文字。备注文字会保存在passwd的备注栏位中。
-d<登入目录> 　指定用户登入时的起始目录。
-D 　变更预设值．
-e<有效期限> 　指定帐号的有效期限。
-f<缓冲天数> 　指定在密码过期后多少天即关闭该帐号。
-g<群组> 　指定用户所属的群组。
-G<群组> 　指定用户所属的附加群组。
-m 　自动建立用户的登入目录。
-M 　不要自动建立用户的登入目录。
-n 　取消建立以用户名称为名的群组．
-r 　建立系统帐号。
-s<shell>　 　指定用户登入后所使用的shell。
-u<uid> 　指定用户ID。
```

实例：

- 添加的用户指定相应的用户组

```javascript
[root@LVS ~]# useradd -g root rossum
```

[root@LVS ~]

\# id rossum uid=1002(rossum) gid=0(root) 组=0(root)

- 添加用户，指定shell，指定用户组，指定用户id

```javascript
[root@LVS ~]# useradd rossuma -s /sbin/nologin -g root -u 1008
```

[root@LVS ~]

\# id 1008 uid=1008(rossuma) gid=0(root) 组=0(root)

[root@LVS ~]

\# tail -1 /etc/passwd rossuma:x:1008:0::/home/rossuma:/sbin/nologin

- 建立用户指定目录等信息

```javascript
[root@LVS ~]# useradd -d /home/mytest -g root rossumc
```

[root@LVS ~]

\# su rossumc

[rossumc@LVS root]

$ cd ~

[rossumc@LVS ~]

$ pwd /home/mytest

#### 7.2 usermod

功能：修改系统已经存在的用户属性。

语法：

```javascript
usermod [-LU][-c <备注>][-d <登入目录>][-e <有效期限>][-f <缓冲天数>][-g <群组>][-G <群组>][-l <帐号名称>][-s <shell>][-u <uid>][用户帐号]
```

参数：

```javascript
-c<备注> 　修改用户帐号的备注文字。
-d登入目录> 　修改用户登入时的目录。
-e<有效期限> 　修改帐号的有效期限。
-f<缓冲天数> 　修改在密码过期后多少天即关闭该帐号。
-g<群组> 　修改用户所属的群组。
-G<群组> 　修改用户所属的附加群组。
-l<帐号名称> 　修改用户帐号名称。
-L 　锁定用户密码，使密码无效。
-s<shell> 　修改用户登入后所使用的shell。
-u<uid> 　修改用户ID。
-U 　解除密码锁定。
```

实例：

- 锁定用户并查看，前面有!表示锁定

```javascript
[root@LVS ~]# usermod -L rossumc
```

[root@LVS ~]

\# grep rossumc /etc/shadow rossumc:!$6$L8f/R75W$cnjhzGGvxdNM/QCB2ZeSTRvGCi0giV7j3TC7GRSOpJowN1oP3eNhQygZrZOfMLiZif1mVNvWViTDuUCthrpsh/:18366:0:99999:7:::

[root@LVS ~]

\#

也可以这样查看用户的锁定状态

```javascript
[root@LVS ~]# passwd --status rossumc
rossumc LK 2020-04-14 0 99999 7 -1 (密码已被锁定。)
```

[root@LVS ~]

\#

- 解除以上用户的状态

```javascript
[root@LVS ~]# usermod -U rossumc
```

[root@LVS ~]

\# tail -1 /etc/shadow rossumc:$6$L8f/R75W$cnjhzGGvxdNM/QCB2ZeSTRvGCi0giV7j3TC7GRSOpJowN1oP3eNhQygZrZOfMLiZif1mVNvWViTDuUCthrpsh/:18366:0:99999:7:::

[root@LVS ~]

\#

- 设置用户过期时间

```javascript
[root@LVS ~]# usermod -e "2020-04-10" rossumc
```

- 修改用户属性

```javascript
[root@LVS ~]# groupadd hr
```

[root@LVS ~]

\# usermod -G hr rossumc

[root@LVS ~]

\# id rossumc uid=1009(rossumc) gid=0(root) 组=0(root),1002(hr)

[root@LVS ~]

\#

#### 7.3 userdel

功能：删除用户。可删除用户帐号与相关的文件。若不加参数，则仅删除用户帐号，而不删除相关文件。

语法：

```javascript
userdel [-r][用户帐号]
```

实例：

```javascript
[root@LVS ~]# userdel -r rossumc
userdel: user rossumc is currently used by process 11385
这里前面登录过，所以两次退出后自动删除
```

[root@LVS ~]

\# exit exit

[rossumc@LVS ~]

$ exit exit

[root@LVS ~]

\# userdel -r rossumc userdel：用户“rossumc”不存在

#### 7.4 groupadd

功能：添加用户组。

groupadd 命令用于创建一个新的工作组，新工作组的信息将被添加到系统文件中。

相关文件:

/etc/group 组账户信息。 /etc/gshadow 安全组账户信息。 /etc/login.defs Shadow密码套件配置。

语法：

```javascript
groupadd [-g gid [-o]] [-r] [-f] group
```

参数：

```javascript
-g：指定新建工作组的 id；
-r：创建系统工作组，系统工作组的组ID小于 1000；
-K：覆盖配置文件 "/ect/login.defs"；
-o：允许添加组 ID 号不唯一的工作组。
-f,--force: 如果指定的组已经存在，此选项将失明了仅以成功状态退出。当与 -g 一起使用，并且指定的GID_MIN已经存在时，选择另一个唯一的GID（即-g关闭）。
```

实例：

- 添加net01组，并指定gid为2000

```javascript
[root@LVS ~]# groupadd net01 -g 2000
```

[root@LVS ~]

\# grep 'net01' /etc/group net01:x:2000:

[root@LVS ~]

\#

#### 7.5 passwd

功能： 修改用户密码。

语法：

```javascript
passwd [-k] [-l] [-u [-f]] [-d] [-S] [username]
```

参数：

```javascript
-d 删除密码
-f 强制执行
-k 更新只能发送在过期之后
-l 停止账号使用
-S 显示密码信息
-u 启用已被停止的账户
-x 设置密码的有效期
-g 修改群组密码
-i 过期后停止用户账号
```

实例：

- 修改rossuma的密码

```javascript
[root@LVS ~]# passwd rossuma
更改用户 rossuma 的密码 。
新的 密码：
重新输入新的 密码：
passwd：所有的身份验证令牌已经成功更新。
```

- 查看账户密码信息

```javascript
[root@LVS ~]# passwd -S rossuma
rossuma PS 2020-04-15 0 99999 7 -1 (密码已设置，使用 SHA512 算法。)
```

- 清除用户密码

```javascript
[root@LVS ~]# passwd -d rossuma
清除用户的密码 rossuma。
passwd: 操作成功
```

#### 7.6 chage

功能：修改用户密码有效期限。

语法：

```javascript
chage [options] user
```

参数：

```javascript
  -d, --lastday 最近日期        将最近一次密码设置时间设为“最近日期”
  -E, --expiredate 过期日期     将帐户过期时间设为“过期日期”
  -h, --help                    显示此帮助信息并推出
  -I, --inactive INACITVE       过期 INACTIVE 天数后，设定密码为失效状态
  -l, --list                    显示帐户年龄信息
  -m, --mindays 最小天数        将两次改变密码之间相距的最小天数设为“最小天数”
  -M, --maxdays 最大天数        将两次改变密码之间相距的最大天数设为“最大天数”
  -R, --root CHROOT_DIR         chroot 到的目录
  -W, --warndays 警告天数       将过期警告天数设为“警告天数”
```

实例：

- 查看mysql账户的密码及有效期

```javascript
[root@LVS ~]# chage -l mysql
最近一次密码修改时间					：3月 29, 2020
密码过期时间					：从不
密码失效时间					：从不
帐户过期时间						：从不
两次改变密码之间相距的最小天数		：-1
两次改变密码之间相距的最大天数		：-1
在密码过期之前警告的天数	：-1
```

[root@LVS ~]

\#

```javascript
[root@LVS ~]# LANG='en_us'
```

[root@LVS ~]

\# chage -l mysql Last password change : Mar 29, 2020 Password expires : never Password inactive : never Account expires : never Minimum number of days between password change : -1 Maximum number of days between password change : -1 Number of days of warning before password expires : -1

[root@LVS ~]

\#

- 设置mysql用户60天后密码过期，至少7天后才能修改密码，密码过期前7天开始收到告警信息。

```javascript
[root@LVS ~]# chage -M 60 -m 7 -W 7 mysql
```

[root@LVS ~]

\# chage -l mysql Last password change : Mar 29, 2020 Password expires : May 28, 2020 Password inactive : never Account expires : never Minimum number of days between password change : 7 Maximum number of days between password change : 60 Number of days of warning before password expires : 7

[root@LVS ~]

\#

- 强制新建用户第一次登陆时修改密码

```javascript
[root@LVS ~]# chage -d 0 rossuma
```

[root@LVS ~]

\# chage -l rossuma Last password change : password must be changed Password expires : password must be changed Password inactive : password must be changed Account expires : never Minimum number of days between password change : 0 Maximum number of days between password change : 99999 Number of days of warning before password expires : 7

[root@LVS ~]

\#

#### 7.7 id

功能：查看用户的 uid,gid 及归属的用户组。

id会显示用户以及所属群组的实际与有效ID。若两个ID相同，则仅显示实际ID。若仅指定用户名称，则显示目前用户的ID。

语法：

```javascript
id [-gGnru][--help][--version][用户名称]
```

参数：

```javascript
-g或--group 　显示用户所属群组的ID。
-G或--groups 　显示用户所属附加群组的ID。
-n或--name 　显示用户，所属群组或附加群组的名称。
-r或--real 　显示实际ID。
-u或--user 　显示用户ID。
```

实例：

- 显示当前用户和其他用户

```javascript
[root@LVS ~]# id
uid=0(root) gid=0(root) groups=0(root) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
```

[root@LVS ~]

\# id rossuma uid=1008(rossuma) gid=0(root) groups=0(root)

[root@LVS ~]

\#

- 显示所有群组信息

```javascript
[root@LVS ~]# usermod -G hr rossuma
```

[root@LVS ~]

\# id -G rossuma 0 1002

[root@LVS ~]

\#

#### 7.8 su

功能：用于变更为其他使用者的身份，除 root 外，需要键入该使用者的密码。

语法：

```javascript
su [-fmp] [-c command] [-s shell] [--help] [--version] [-] [USER [ARG]]
```

参数：

```javascript
-f 或 --fast 不必读启动档（如 csh.cshrc 等），仅用于 csh 或 tcsh
-m -p 或 --preserve-environment 执行 su 时不改变环境变数
-c command 或 --command=command 变更为帐号为 USER 的使用者并执行指令（command）后再变回原来使用者
-s shell 或 --shell=shell 指定要执行的 shell （bash csh tcsh 等），预设值为 /etc/passwd 内的该使用者（USER） shell
--help 显示说明文件
--version 显示版本资讯
- -l 或 --login 这个参数加了之后，就好像是重新 login 为该使用者一样，大部份环境变数（HOME SHELL USER等等）都是以该使用者（USER）为主，并且工作目录也会改变，如果没有指定 USER ，内定是 root
USER 欲变更的使用者帐号
ARG 传入新的 shell 参数
```

实例：

- 变更帐号为 root 并在执行 ls 指令后退出变回原使用者

```javascript
[rossuma@LVS ~]# su -c ls root
```

- 切换用户改变环境变量，不加-不改变环境变量

```javascript
[toma@LVS ~]$ whoami
toma
```

[toma@LVS ~]

$ su - root Password: Last login: Wed Apr 15 09:12:37 CST 2020 on pts/2

[root@LVS ~]

\# whoami root

[root@LVS ~]

\# pwd /root

[root@LVS ~]

\#

- 切换用户，不改变环境变量

```javascript
[toma@LVS ~]$ whoami
toma
```

[toma@LVS ~]

$ pwd /home/toma

[toma@LVS ~]

$ su root 密码：

[root@LVS toma]

\# whoami root

[root@LVS toma]

\# pwd /home/toma

[root@LVS toma]

\#

#### 7.9 sudo

功能： 以系统管理者的身份执行指令，也就是说，经由 sudo 所执行的指令就好像是 root 亲自执行。

严谨些说，sudo 允许一个已授权用户以超级用户或者其它用户的角色运行一个命令。当然，能做什么不能做什么都是通过安全策略来指定的。sudo 支持插件架构的安全策略，并能把输入输出写入日志。第三方可以开发并发布自己的安全策略和输入输出日志插件，并让它们无缝的和 sudo 一起工作。默认的安全策略记录在 /etc/sudoers 文件中。而安全策略可能需要用户通过密码来验证他们自己。也就是在用户执行 sudo 命令时要求用户输入自己账号的密码。如果验证失败，sudo 命令将会退出。

语法：

```javascript
sudo [-bhHpV][-s ][-u <用户>][指令]
```

参数：

```javascript
  -b  在后台执行指令。
  -h  显示帮助。
  -H  将HOME环境变量设为新身份的HOME环境变量。
  -k  结束密码的有效期限，也就是下次再执行sudo时便需要输入密码。
  -l  列出目前用户可执行与无法执行的指令。
  -p  改变询问密码的提示符号。
  -s  执行指定的shell。
  -u  <用户>  以指定的用户作为新的身份。若不加上此参数，则预设以root作为新的身份。
  -v  延长密码有效期限5分钟。
  -V  显示版本信息。
  -S  从标准输入流替代终端来获取密码
```

实例：

- 加入轮子组并添加用户

```javascript
[root@LVS ~]# su tomb
```

[tomb@LVS root]

$ id uid=1010(tomb) gid=1010(tomb) groups=1010(tomb),10(wheel) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023

[tomb@LVS root]

$ useradd tomc bash: /usr/sbin/useradd: Permission denied

[tomb@LVS root]

$ sudo useradd tomc

[sudo]

password for tomb:

[tomb@LVS root]

$ id tomc uid=1011(tomc) gid=1011(tomc) groups=1011(tomc)

### 八、基础网络操作命令

#### 8.1 scp

功能：scp 全拼 secure copy，用于不同主机之间复制文件。

语法：

```javascript
scp [-1246BCpqrv] [-c cipher] [-F ssh_config] [-i identity_file]
[-l limit] [-o ssh_option] [-P port] [-S program]
[[user@]host1:]file1 [...] [[user@]host2:]file2
```

简易写法

```javascript
scp [可选参数] file_source file_target 
```

参数：

```javascript
-1： 强制scp命令使用协议ssh1
-2： 强制scp命令使用协议ssh2
-4： 强制scp命令只使用IPv4寻址
-6： 强制scp命令只使用IPv6寻址
-B： 使用批处理模式（传输过程中不询问传输口令或短语）
-C： 允许压缩。（将-C标志传递给ssh，从而打开压缩功能）
-p：保留原文件的修改时间，访问时间和访问权限。
-q： 不显示传输进度条。
-r： 递归复制整个目录。
-v：详细方式显示输出。scp和ssh(1)会显示出整个过程的调试信息。这些信息用于调试连接，验证和配置问题。
-c cipher： 以cipher将数据传输进行加密，这个选项将直接传递给ssh。
-F ssh_config： 指定一个替代的ssh配置文件，此参数直接传递给ssh。
-i identity_file： 从指定文件中读取传输时使用的密钥文件，此参数直接传递给ssh。
-l limit： 限定用户所能使用的带宽，以Kbit/s为单位。
-o ssh_option： 如果习惯于使用ssh_config(5)中的参数传递方式，
-P port：注意是大写的P, port是指定数据传输用到的端口号
-S program： 指定加密传输时所使用的程序。此程序必须能够理解ssh(1)的选项。
```

实例：

- 从本地复制文件到远程服务器

```javascript
scp local_file remote_username@remote_ip:remote_folder 
或者 
scp local_file remote_username@remote_ip:remote_file 
或者 
scp local_file remote_ip:remote_folder 
或者 
scp local_file remote_ip:remote_file 
[root@LVS ~]# scp score.sql 192.168.100.106:/home/toma
root@192.168.100.106's password: 
score.sql                                         100% 1936     1.9KB/s   00:00    
[root@LVS ~]# scp student1.txt root@192.168.100.106:/home/toma/
root@192.168.100.106's password: 
student1.txt                                      100%  198     0.2KB/s   00:00    
```

[root@LVS ~]

\#

- 从本地复制目录到远程服务器

```javascript
[root@LVS ~]# mkdir -p  /mydira/mydir{1..5}
```

[root@LVS ~]

\# tree /mydira /mydira ├── mydir1 ├── mydir2 ├── mydir3 ├── mydir4 └── mydir5 5 directories, 0 files

[root@LVS ~]

\#

[root@LVS ~]

\# scp -r /mydira root@192.168.100.106:/home/toma root@192.168.100.106's password:

[root@LVS ~]

\#

#### 8.2 wget

功能： 非交互式的网络文件下载工具。

语法：

```javascript
 wget [选项]... [URL]...
```

参数：

```javascript
启动：
  -V,  --version           显示 Wget 的版本信息并退出。
  -h,  --help              打印此帮助。
  -b,  --background        启动后转入后台。
  -e,  --execute=COMMAND   运行一个“.wgetrc”风格的命令。

日志和输入文件：
  -o,  --output-file=FILE    将日志信息写入 FILE。
  -a,  --append-output=FILE  将信息添加至 FILE。
  -d,  --debug               打印大量调试信息。
  -q,  --quiet               安静模式 (无信息输出)。
  -v,  --verbose             详尽的输出 (此为默认值)。
  -nv, --no-verbose          关闭详尽输出，但不进入安静模式。
       --report-speed=TYPE   Output bandwidth as TYPE.  TYPE can be bits.
  -i,  --input-file=FILE     下载本地或外部 FILE 中的 URLs。
  -F,  --force-html          把输入文件当成 HTML 文件。
  -B,  --base=URL            解析与 URL 相关的
                             HTML 输入文件 (由 -i -F 选项指定)。
       --config=FILE         Specify config file to use.

下载：
  -t,  --tries=NUMBER            设置重试次数为 NUMBER (0 代表无限制)。
       --retry-connrefused       即使拒绝连接也是重试。
  -O,  --output-document=FILE    将文档写入 FILE。
  -nc, --no-clobber              skip downloads that would download to
                                 existing files (overwriting them).
  -c,  --continue                断点续传下载文件。
       --progress=TYPE           选择进度条类型。
  -N,  --timestamping            只获取比本地文件新的文件。
  --no-use-server-timestamps     不用服务器上的时间戳来设置本地文件。
  -S,  --server-response         打印服务器响应。
       --spider                  不下载任何文件。
  -T,  --timeout=SECONDS         将所有超时设为 SECONDS 秒。
       --dns-timeout=SECS        设置 DNS 查寻超时为 SECS 秒。
       --connect-timeout=SECS    设置连接超时为 SECS 秒。
       --read-timeout=SECS       设置读取超时为 SECS 秒。
  -w,  --wait=SECONDS            等待间隔为 SECONDS 秒。
       --waitretry=SECONDS       在获取文件的重试期间等待 1..SECONDS 秒。
       --random-wait             获取多个文件时，每次随机等待间隔
                                 0.5*WAIT...1.5*WAIT 秒。
       --no-proxy                禁止使用代理。
  -Q,  --quota=NUMBER            设置获取配额为 NUMBER 字节。
       --bind-address=ADDRESS    绑定至本地主机上的 ADDRESS (主机名或是 IP)。
       --limit-rate=RATE         限制下载速率为 RATE。
       --no-dns-cache            关闭 DNS 查寻缓存。
       --restrict-file-names=OS  限定文件名中的字符为 OS 允许的字符。
       --ignore-case             匹配文件/目录时忽略大小写。
  -4,  --inet4-only              仅连接至 IPv4 地址。
  -6,  --inet6-only              仅连接至 IPv6 地址。
       --prefer-family=FAMILY    首先连接至指定协议的地址
                                 FAMILY 为 IPv6，IPv4 或是 none。
       --user=USER               将 ftp 和 http 的用户名均设置为 USER。
       --password=PASS           将 ftp 和 http 的密码均设置为 PASS。
       --ask-password            提示输入密码。
       --no-iri                  关闭 IRI 支持。
       --local-encoding=ENC      IRI (国际化资源标识符) 使用 ENC 作为本地编码。
       --remote-encoding=ENC     使用 ENC 作为默认远程编码。
       --unlink                  remove file before clobber.

目录：
  -nd, --no-directories           不创建目录。
  -x,  --force-directories        强制创建目录。
  -nH, --no-host-directories      不要创建主目录。
       --protocol-directories     在目录中使用协议名称。
  -P,  --directory-prefix=PREFIX  以 PREFIX/... 保存文件
       --cut-dirs=NUMBER          忽略远程目录中 NUMBER 个目录层。

HTTP 选项：
       --http-user=USER        设置 http 用户名为 USER。
       --http-password=PASS    设置 http 密码为 PASS。
       --no-cache              不在服务器上缓存数据。
       --default-page=NAME     改变默认页
                               (默认页通常是“index.html”)。
  -E,  --adjust-extension      以合适的扩展名保存 HTML/CSS 文档。
       --ignore-length         忽略头部的‘Content-Length’区域。
       --header=STRING         在头部插入 STRING。
       --max-redirect          每页所允许的最大重定向。
       --proxy-user=USER       使用 USER 作为代理用户名。
       --proxy-password=PASS   使用 PASS 作为代理密码。
       --referer=URL           在 HTTP 请求头包含‘Referer: URL’。
       --save-headers          将 HTTP 头保存至文件。
  -U,  --user-agent=AGENT      标识为 AGENT 而不是 Wget/VERSION。
       --no-http-keep-alive    禁用 HTTP keep-alive (永久连接)。
       --no-cookies            不使用 cookies。
       --load-cookies=FILE     会话开始前从 FILE 中载入 cookies。
       --save-cookies=FILE     会话结束后保存 cookies 至 FILE。
       --keep-session-cookies  载入并保存会话 (非永久) cookies。
       --post-data=STRING      使用 POST 方式；把 STRING 作为数据发送。
       --post-file=FILE        使用 POST 方式；发送 FILE 内容。
       --content-disposition   当选中本地文件名时
                               允许 Content-Disposition 头部 (尚在实验)。
       --content-on-error      output the received content on server errors.
       --auth-no-challenge     发送不含服务器询问的首次等待
                               的基本 HTTP 验证信息。

HTTPS (SSL/TLS) 选项：
       --secure-protocol=PR     选择安全协议，可以是 auto、SSLv2、
                                SSLv3 或是 TLSv1 中的一个。
       --no-check-certificate   不要验证服务器的证书。
       --certificate=FILE       客户端证书文件。
       --certificate-type=TYPE  客户端证书类型，PEM 或 DER。
       --private-key=FILE       私钥文件。
       --private-key-type=TYPE  私钥文件类型，PEM 或 DER。
       --ca-certificate=FILE    带有一组 CA 认证的文件。
       --ca-directory=DIR       保存 CA 认证的哈希列表的目录。
       --random-file=FILE       带有生成 SSL PRNG 的随机数据的文件。
       --egd-file=FILE          用于命名带有随机数据的 EGD 套接字的文件。

FTP 选项：
       --ftp-user=USER         设置 ftp 用户名为 USER。
       --ftp-password=PASS     设置 ftp 密码为 PASS。
       --no-remove-listing     不要删除‘.listing’文件。
       --no-glob               不在 FTP 文件名中使用通配符展开。
       --no-passive-ftp        禁用“passive”传输模式。
       --preserve-permissions  保留远程文件的权限。
       --retr-symlinks         递归目录时，获取链接的文件 (而非目录)。

WARC options:
       --warc-file=FILENAME      save request/response data to a .warc.gz file.
       --warc-header=STRING      insert STRING into the warcinfo record.
       --warc-max-size=NUMBER    set maximum size of WARC files to NUMBER.
       --warc-cdx                write CDX index files.
       --warc-dedup=FILENAME     do not store records listed in this CDX file.
       --no-warc-compression     do not compress WARC files with GZIP.
       --no-warc-digests         do not calculate SHA1 digests.
       --no-warc-keep-log        do not store the log file in a WARC record.
       --warc-tempdir=DIRECTORY  location for temporary files created by the
                                 WARC writer.

递归下载：
  -r,  --recursive          指定递归下载。
  -l,  --level=NUMBER       最大递归深度 (inf 或 0 代表无限制，即全部下载)。
       --delete-after       下载完成后删除本地文件。
  -k,  --convert-links      让下载得到的 HTML 或 CSS 中的链接指向本地文件。
  --backups=N   before writing file X, rotate up to N backup files.
  -K,  --backup-converted   在转换文件 X 前先将它备份为 X.orig。
  -m,  --mirror             -N -r -l inf --no-remove-listing 的缩写形式。
  -p,  --page-requisites    下载所有用于显示 HTML 页面的图片之类的元素。
       --strict-comments    用严格方式 (SGML) 处理 HTML 注释。

递归接受/拒绝：
  -A,  --accept=LIST               逗号分隔的可接受的扩展名列表。
  -R,  --reject=LIST               逗号分隔的要拒绝的扩展名列表。
       --accept-regex=REGEX        regex matching accepted URLs.
       --reject-regex=REGEX        regex matching rejected URLs.
       --regex-type=TYPE           regex type (posix|pcre).
  -D,  --domains=LIST              逗号分隔的可接受的域列表。
       --exclude-domains=LIST      逗号分隔的要拒绝的域列表。
       --follow-ftp                跟踪 HTML 文档中的 FTP 链接。
       --follow-tags=LIST          逗号分隔的跟踪的 HTML 标识列表。
       --ignore-tags=LIST          逗号分隔的忽略的 HTML 标识列表。
  -H,  --span-hosts                递归时转向外部主机。
  -L,  --relative                  只跟踪有关系的链接。
  -I,  --include-directories=LIST  允许目录的列表。
  --trust-server-names             use the name specified by the redirection
                                   url last component.
  -X,  --exclude-directories=LIST  排除目录的列表。
  -np, --no-parent                 不追溯至父目录。
```

实例：

- 使用wget -O下载并以不同的文件名保存(-O：下载文件到对应目录，并且修改文件名称)

```javascript
[root@LVS ~]# wget -O index.zip http://www.haopython.com
--2020-04-15 14:41:24--  http://www.haopython.com/
正在解析主机 www.haopython.com (www.haopython.com)... 212.64.86.215
正在连接 www.haopython.com (www.haopython.com)|212.64.86.215|:80... 已连接。
已发出 HTTP 请求，正在等待回应... 200 OK
长度：未指定 [text/html]
正在保存至: “index.zip”

    [  <=>                                                                                                         ] 90,566       221KB/s 用时 0.4s   

2020-04-15 14:41:25 (221 KB/s) - “index.zip” 已保存 [90566]
```

- 使用wget -b后台下载，可以使用tail -f wget-log查看进度

```javascript
[root@LVS ~]# wget -b  http://www.haopython.com
继续在后台运行，pid 为 17404。
将把输出写入至 “wget-log”。
```

[root@LVS ~]

\# tail -f wget-log 正在连接 www.haopython.com (www.haopython.com)|212.64.86.215|:80... 已连接。 已发出 HTTP 请求，正在等待回应... 200 OK 长度：未指定 [text/html] 正在保存至: “index.html.1” 0K .......... .......... .......... .......... .......... 174K 50K .......... .......... .......... ........ 321K=0.4s 2020-04-15 14:45:52 (217 KB/s) - “index.html.1” 已保存 [90566]

- 利用-spider: 模拟下载 ，不会下载，只是会检查是否网站是否好着

```javascript
[root@LVS ~]# wget --spider http://www.haopython.com
开启 Spider 模式。检查是否存在远程文件。
--2020-04-15 14:48:46--  http://www.haopython.com/
正在解析主机 www.haopython.com (www.haopython.com)... 212.64.86.215
正在连接 www.haopython.com (www.haopython.com)|212.64.86.215|:80... 已连接。
已发出 HTTP 请求，正在等待回应... 200 OK
长度：未指定 [text/html]
存在远程文件且该文件可能含有更深层的链接，
但不能进行递归操作 -- 无法获取。
```

- 模拟下载打印服务器响应

```javascript
[root@LVS ~]# wget -S http://www.haopython.com
--2020-04-15 14:50:48--  http://www.haopython.com/
正在解析主机 www.haopython.com (www.haopython.com)... 212.64.86.215
正在连接 www.haopython.com (www.haopython.com)|212.64.86.215|:80... 已连接。
已发出 HTTP 请求，正在等待回应... 
  HTTP/1.1 200 OK
  Server: nginx
  Date: Wed, 15 Apr 2020 06:50:49 GMT
  Content-Type: text/html; charset=UTF-8
  Transfer-Encoding: chunked
  Connection: keep-alive
  Keep-Alive: timeout=60
  Link: <http://www.haopython.com/wp-json/>; rel="https://api.w.org/"
长度：未指定 [text/html]
正在保存至: “index.html.2”

    [  <=>                                                                                                         ] 90,566       217KB/s 用时 0.4s   

2020-04-15 14:50:49 (217 KB/s) - “index.html.2” 已保存 [90566]
```

- 设定指定次数

```javascript
[root@LVS ~]# wget -r --tries=2  www.haopython.com
--2020-04-15 14:53:45--  http://www.haopython.com/
正在解析主机 www.haopython.com (www.haopython.com)... 212.64.86.215
正在连接 www.haopython.com (www.haopython.com)|212.64.86.215|:80... 已连接。
已发出 HTTP 请求，正在等待回应... 200 OK
长度：未指定 [text/html]
正在保存至: “www.haopython.com/index.html”

    [  <=>                                                                                                         ] 90,566       231KB/s 用时 0.4s   

2020-04-15 14:53:45 (231 KB/s) - “www.haopython.com/index.html” 已保存 [90566]

正在载入 robots.txt；请忽略错误消息。
--2020-04-15 14:53:45--  http://www.haopython.com/robots.txt
再次使用存在的到 www.haopython.com:80 的连接。
已发出 HTTP 请求，正在等待回应... 200 OK
长度：未指定 [text/plain]
正在保存至: “www.haopython.com/robots.txt”

    [ <=>                                                                                                          ] 67          --.-K/s 用时 0s      

2020-04-15 14:53:46 (664 KB/s) - “www.haopython.com/robots.txt” 已保存 [67]
```

#### 8.3 ping

功能： 测试主机之间网络的连通性。

语法：

```javascript
ping [-dfnqrRv][-c<完成次数>][-i<间隔秒数>][-I<网络界面>][-l<前置载入>][-p<范本样式>][-s<数据包大小>][-t<存活数值>][主机名称或IP地址]
```

参数：

```javascript
-d 使用Socket的SO_DEBUG功能。
-c<完成次数> 设置完成要求回应的次数。
-f 极限检测。
-i<间隔秒数> 指定收发信息的间隔时间。
-I<网络界面> 使用指定的网络接口送出数据包。
-l<前置载入> 设置在送出要求信息之前，先行发出的数据包。
-n 只输出数值。
-p<范本样式> 设置填满数据包的范本样式。
-q 不显示指令执行过程，开头和结尾的相关信息除外。
-r 忽略普通的Routing Table，直接将数据包送到远端主机上。
-R 记录路由过程。
-s<数据包大小> 设置数据包的大小。
-t<存活数值> 设置存活数值TTL的大小。
-v 详细显示指令的执行过程。
```

实例：

- 检测是否与主机连通

```javascript
[root@LVS ~]# ping www.haopython.com
PING www.haopython.com (212.64.86.215) 56(84) bytes of data.
^C
--- www.haopython.com ping statistics ---
17 packets transmitted, 0 received, 100% packet loss, time 16013ms
```

[root@LVS ~]

\# ping www.baidu.com PING www.a.shifen.com (220.181.38.150) 56(84) bytes of data. 64 bytes from 220.181.38.150 (220.181.38.150): icmp_seq=1 ttl=53 time=22.3 ms 64 bytes from 220.181.38.150 (220.181.38.150): icmp_seq=2 ttl=53 time=22.2 ms ^C --- www.a.shifen.com ping statistics --- 2 packets transmitted, 2 received, 0% packet loss, time 1001ms rtt min/avg/max/mdev = 22.249/22.295/22.342/0.156 ms

[root@LVS ~]

\#

- 指定接收包的次数

```javascript
[root@LVS ~]# ping -c 6 www.baidu.com
PING www.baidu.com (220.181.38.150) 56(84) bytes of data.
64 bytes from 220.181.38.150 (220.181.38.150): icmp_seq=1 ttl=53 time=21.8 ms
64 bytes from 220.181.38.150 (220.181.38.150): icmp_seq=2 ttl=53 time=25.0 ms
64 bytes from 220.181.38.150 (220.181.38.150): icmp_seq=3 ttl=53 time=21.6 ms
64 bytes from 220.181.38.150 (220.181.38.150): icmp_seq=4 ttl=53 time=21.5 ms
64 bytes from 220.181.38.150 (220.181.38.150): icmp_seq=5 ttl=53 time=27.3 ms
64 bytes from 220.181.38.150 (220.181.38.150): icmp_seq=6 ttl=53 time=22.2 ms

--- www.baidu.com ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5011ms
rtt min/avg/max/mdev = 21.526/23.265/27.315/2.171 ms
```

[root@LVS ~]

\#

- 指定发送周期为3秒包大小1000Byte和TTL时间linux下ping包的默认大小为64Byte，次数不限。但有时我们需要尝试ping[大数据](https://cloud.tencent.com/solution/bigdata?from=10680)包，来测试网络的状况，这时，就要指定ping包的大小了。

```javascript
[root@LVS ~]# ping -i 3 -s 1000 -t 255 -c 2 www.baidu.com
PING www.baidu.com (220.181.38.150) 1000(1028) bytes of data.
1008 bytes from 220.181.38.150 (220.181.38.150): icmp_seq=1 ttl=53 time=24.2 ms
1008 bytes from 220.181.38.150 (220.181.38.150): icmp_seq=2 ttl=53 time=27.0 ms

--- www.baidu.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 24.267/25.676/27.085/1.409 ms
```

#### 8.4 route

功能：显示和设置 linux 系统的路由表。

实例：

- 查看本机路由表

```javascript
[root@LVS ~]# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.100.1   0.0.0.0         UG    100    0        0 ens33
192.168.100.0   0.0.0.0         255.255.255.0   U     100    0        0 ens33
192.168.122.0   0.0.0.0         255.255.255.0   U     0      0        0 virbr0
```

[root@LVS ~]

\#

#### 8.5 ifconfig

功能： 命令用于显示或设置网络设备。

语法：

```javascript
ifconfig [网络设备][down up -allmulti -arp -promisc][add<地址>][del<地址>][<hw<网络设备类型><硬件地址>][io_addr<I/O地址>][irq<IRQ地址>][media<网络媒介类型>][mem_start<内存地址>][metric<数目>][mtu<字节>][netmask<子网掩码>][tunnel<地址>][-broadcast<地址>][-pointopoint<地址>][IP地址]
```

参数：

```javascript
add<地址> 设置网络设备IPv6的IP地址。
del<地址> 删除网络设备IPv6的IP地址。
down 关闭指定的网络设备。
<hw<网络设备类型><硬件地址> 设置网络设备的类型与硬件地址。
io_addr<I/O地址> 设置网络设备的I/O地址。
irq<IRQ地址> 设置网络设备的IRQ。
media<网络媒介类型> 设置网络设备的媒介类型。
mem_start<内存地址> 设置网络设备在主内存所占用的起始地址。
metric<数目> 指定在计算数据包的转送次数时，所要加上的数目。
mtu<字节> 设置网络设备的MTU。
netmask<子网掩码> 设置网络设备的子网掩码。
tunnel<地址> 建立IPv4与IPv6之间的隧道通信地址。
up 启动指定的网络设备。
-broadcast<地址> 将要送往指定地址的数据包当成广播数据包来处理。
-pointopoint<地址> 与指定地址的网络设备建立直接连线，此模式具有保密功能。
-promisc 关闭或启动指定网络设备的promiscuous模式。
[IP地址] 指定网络设备的IP地址。
[网络设备] 指定网络设备的名称。
```

实例：

- 显示网络设备信息

```javascript
[root@LVS ~]# ifconfig
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.100.106  netmask 255.255.255.0  broadcast 192.168.100.255
        inet6 fe80::e65:fc2a:ca83:27d0  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:68:45:f4  txqueuelen 1000  (Ethernet)
        RX packets 129442  bytes 20024014 (19.0 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 11350  bytes 1422667 (1.3 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1  (Local Loopback)
        RX packets 4362  bytes 452809 (442.1 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 4362  bytes 452809 (442.1 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

virbr0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 192.168.122.1  netmask 255.255.255.0  broadcast 192.168.122.255
        ether 52:54:00:a4:73:ea  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

- 启动关闭指定网卡

```javascript
[root@LVS ~]# ifconfig eth0 down
```

[root@LVS ~]

\# ifconfig eth0 up

[root@LVS ~]

\#

- 配置IP地址

```javascript
[root@LVS ~]# ifconfig ens33 192.168.100.106 netmask 255.255.255.0 broadcast 192.168.100.255
```

[root@LVS ~]

\#

- 启用和关闭arp协议

```javascript
 [root@LVS ~]# ifconfig ens33 arp[root@LVS ~]# ifconfig ens33 -arp #关闭后SSH无法连接[root@LVS ~]# 
```

- 设置最大传输单元

```javascript
 [root@LVS ~]# ifconfig ens33 mtu 1500
```

补：启动和关闭网卡

```javascript
[root@LVS ~]# ifup ens33
```

[root@LVS ~]

\# ifdown ens33

#### 8.6 netstat

功能：查看网络状态。

语法：

```javascript
netstat [-acCeFghilMnNoprstuvVwx][-A<网络类型>][--ip]
```

参数：

```javascript
-a或--all 显示所有连线中的Socket。
-A<网络类型>或--<网络类型> 列出该网络类型连线中的相关地址。
-c或--continuous 持续列出网络状态。
-C或--cache 显示路由器配置的快取信息。
-e或--extend 显示网络其他相关信息。
-F或--fib 显示FIB。
-g或--groups 显示多重广播功能群组组员名单。
-h或--help 在线帮助。
-i或--interfaces 显示网络界面信息表单。
-l或--listening 显示监控中的服务器的Socket。
-M或--masquerade 显示伪装的网络连线。
-n或--numeric 直接使用IP地址，而不通过域名服务器。
-N或--netlink或--symbolic 显示网络硬件外围设备的符号连接名称。
-o或--timers 显示计时器。
-p或--programs 显示正在使用Socket的程序识别码和程序名称。
-r或--route 显示Routing Table。
-s或--statistice 显示网络工作信息统计表。
-t或--tcp 显示TCP传输协议的连线状况。
-u或--udp 显示UDP传输协议的连线状况。
-v或--verbose 显示指令执行过程。
-V或--version 显示版本信息。
-w或--raw 显示RAW传输协议的连线状况。
-x或--unix 此参数的效果和指定"-A unix"参数相同。
--ip或--inet 此参数的效果和指定"-A inet"参数相同。
```

实例：

- 显示详细的网络状况

```javascript
[root@LVS ~]# netstat -a
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 0.0.0.0:sunrpc          0.0.0.0:*               LISTEN     
tcp        0      0 LVS:domain              0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN     
tcp        0      0 localhost:ipp           0.0.0.0:*               LISTEN     
tcp        0      0 localhost:6011          0.0.0.0:*               LISTEN     
tcp        0      0 localhost:6012          0.0.0.0:*               LISTEN     
tcp        0      0 localhost:6013          0.0.0.0:*               LISTEN     
tcp        0      0 localhost:6014          0.0.0.0:*               LISTEN     
```

- 显示当前户籍UDP连接状况

```javascript
[root@LVS ~]# netstat -nu
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
udp        0      0 192.168.100.106:59819   111.230.189.174:123     ESTABLISHED
udp        0      0 192.168.100.106:46885   78.46.102.180:123       ESTABLISHED
udp        0      0 192.168.100.106:50997   162.159.200.123:123     ESTABLISHED
udp        0      0 192.168.100.106:39874   108.59.2.24:123         ESTABLISHED
```

[root@LVS ~]

\#

- 显示UDP端口号的使用情况

```javascript
[root@LVS ~]# netstat -apu
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
udp        0      0 0.0.0.0:mdns            0.0.0.0:*                           588/avahi-daemon: r 
udp        0      0 0.0.0.0:hpoms-dps-lstn  0.0.0.0:*                           21193/dhclient      
udp        0      0 localhost:323           0.0.0.0:*                           572/chronyd         
udp        0      0 LVS:59819               111.230.189.174:ntp     ESTABLISHED 572/chronyd         
udp        0      0 LVS:46885               78.46.102.180:ntp       ESTABLISHED 572/chronyd         
udp        0      0 LVS:50997               162.159.200.123:ntp     ESTABLISHED 572/chronyd         
```

- 显示网卡列表

```javascript
[root@LVS ~]# netstat -i
Kernel Interface table
Iface      MTU    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
ens33     1500   133114      0      0 0         12200      0      0      0 BMRU
lo       65536     4643      0      0 0          4643      0      0      0 LRU
virbr0    1500        0      0      0 0             0      0      0      0 BMU
```

[root@LVS ~]

\#

- 显示组播组的关系

```javascript
[root@LVS ~]# netstat -g
IPv6/IPv4 Group Memberships
Interface       RefCnt Group
--------------- ------ ---------------------
lo              1      224.0.0.1
ens33           1      224.0.0.251
ens33           1      224.0.0.1
virbr0          1      224.0.0.251
virbr0          1      224.0.0.1
```

- 显示网络统计信息

```javascript
[root@LVS ~]# netstat -s
Ip:
    118440 total packets received
    0 forwarded
    0 incoming packets discarded
    20653 incoming packets delivered
    15660 requests sent out
    31 outgoing packets dropped
    28 dropped because of missing route
    2 fragments received ok
    4 fragments created
Icmp:
    90 ICMP messages received
    0 input ICMP message failed.
```

- 显示监听的套接口

```javascript
[root@LVS ~]# netstat -l
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 0.0.0.0:sunrpc          0.0.0.0:*               LISTEN     
tcp        0      0 LVS:domain              0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN     
tcp        0      0 localhost:ipp           0.0.0.0:*               LISTEN     
tcp        0      0 localhost:6011          0.0.0.0:*               LISTEN     
tcp        0      0 localhost:6012          0.0.0.0:*               LISTEN     
tcp        0      0 localhost:6013          0.0.0.0:*               LISTEN     
tcp        0      0 localhost:6014          0.0.0.0:*               LISTEN     
```

### 九、高级网络操作命令

#### 9.1 ss

功能：查看网络状态。

ss是Socket Statistics的缩写。顾名思义，ss命令可以用来获取socket统计信息，它可以显示和netstat类似的内容。ss的优势在于它能够显示更多更详细的有关TCP和连接状态的信息，而且比netstat更快速更高效。

当服务器的socket连接数量变得非常大时，无论是使用netstat命令还是直接cat /proc/net/tcp，执行速度都会很慢。

ss快的秘诀在于，它利用到了TCP协议栈中tcp_diag。tcp_diag是一个用于分析统计的模块，可以获得Linux 内核中第一手的信息，这就确保了ss的快捷高效。

语法：

```javascript
ss [ OPTIONS ]
ss [ OPTIONS ] [ FILTER ]
```

参数：

```javascript
   -h, --help          this message
   -V, --version       output version information
   -n, --numeric       don't resolve service names
   -r, --resolve       resolve host names
   -a, --all           display all sockets
   -l, --listening     display listening sockets
   -o, --options       show timer information
   -e, --extended      show detailed socket information
   -m, --memory        show socket memory usage
   -p, --processes     show process using socket
   -i, --info          show internal TCP information
   -s, --summary       show socket usage summary
   -b, --bpf           show bpf filter socket information
   -Z, --context       display process SELinux security contexts
   -z, --contexts      display process and socket SELinux security contexts
   -N, --net           switch to the specified network namespace name

   -4, --ipv4          display only IP version 4 sockets
   -6, --ipv6          display only IP version 6 sockets
   -0, --packet        display PACKET sockets
   -t, --tcp           display only TCP sockets
   -u, --udp           display only UDP sockets
   -d, --dccp          display only DCCP sockets
   -w, --raw           display only RAW sockets
   -x, --unix          display only Unix domain sockets
   -f, --family=FAMILY display sockets of type FAMILY
```

实例：

- 显示TCP连接

```javascript
[root@LVS ~]# ss -t -a
State       Recv-Q Send-Q                            Local Address:Port                                             Peer Address:Port                
LISTEN      0      128                                           *:sunrpc                                                      *:*                    
LISTEN      0      5                                 192.168.122.1:domain                                                      *:*                    
LISTEN      0      128                                           *:ssh                                                         *:*                    
LISTEN      0      128                                   127.0.0.1:ipp                                                         *:*                    
LISTEN      0      128                                   127.0.0.1:x11-ssh-offset                                              *:*                    
LISTEN      0      128                                   127.0.0.1:6011                                                        *:*                    
LISTEN      0      128                                   127.0.0.1:6012                                                        *:*                    
LISTEN      0      128                                   127.0.0.1:6013                                                        *:*                    
LISTEN      0      128                                   127.0.0.1:6014                                                        *:*                    
ESTAB       0      0                               192.168.100.106:ssh                                           192.168.100.103:59804                
ESTAB       0      0                               192.168.100.106:ssh                                           192.168.100.103:53121                
ESTAB       0      0                               192.168.100.106:ssh                                           192.168.100.103:59822                
ESTAB       0      52                              192.168.100.106:ssh                                           192.168.100.103:60220                
ESTAB       0      0                               192.168.100.106:ssh                                           192.168.100.103:59744                
LISTEN      0      128                                          :::sunrpc                                                     :::*                    
LISTEN      0      128                                          :::ssh                                                        :::*                    
LISTEN      0      128                                          :::telnet                                                     :::*                    
LISTEN      0      128                                         ::1:ipp                                                        :::*                    
LISTEN      0      128                                         ::1:x11-ssh-offset                                             :::*                    
LISTEN      0      128                                         ::1:6011                                                       :::*                    
LISTEN      0      128                                         ::1:6012                                                       :::*                    
LISTEN      0      128                                         ::1:6013                                                       :::*                    
LISTEN      0      128                                         ::1:6014                                                       :::*                    
```

[root@LVS ~]

\#

#### 9.2 tcpdump

功能：抓包工具

语法：

```javascript
tcpdump [-adeflnNOpqStvx][-c<数据包数目>][-dd][-ddd][-F<表达文件>][-i<网络界面>][-r<数据包文件>][-s<数据包大小>][-tt][-T<数据包类型>][-vv][-w<数据包文件>][输出数据栏位]
```

参数：

```javascript
-a 尝试将网络和广播地址转换成名称。
-c<数据包数目> 收到指定的数据包数目后，就停止进行倾倒操作。
-d 把编译过的数据包编码转换成可阅读的格式，并倾倒到标准输出。
-dd 把编译过的数据包编码转换成C语言的格式，并倾倒到标准输出。
-ddd 把编译过的数据包编码转换成十进制数字的格式，并倾倒到标准输出。
-e 在每列倾倒资料上显示连接层级的文件头。
-f 用数字显示网际网络地址。
-F<表达文件> 指定内含表达方式的文件。
-i<网络界面> 使用指定的网络截面送出数据包。
-l 使用标准输出列的缓冲区。
-n 不把主机的网络地址转换成名字。
-N 不列出域名。
-O 不将数据包编码最佳化。
-p 不让网络界面进入混杂模式。
-q 快速输出，仅列出少数的传输协议信息。
-r<数据包文件> 从指定的文件读取数据包数据。
-s<数据包大小> 设置每个数据包的大小。
-S 用绝对而非相对数值列出TCP关联数。
-t 在每列倾倒资料上不显示时间戳记。
-tt 在每列倾倒资料上显示未经格式化的时间戳记。
-T<数据包类型> 强制将表达方式所指定的数据包转译成设置的数据包类型。
-v 详细显示指令执行过程。
-vv 更详细显示指令执行过程。
-x 用十六进制字码列出数据包资料。
-w<数据包文件> 把数据包数据写入指定的文件。
```

实例：

- 显示TCP包信息

```javascript
[root@LVS ~]# tcpdump
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on virbr0, link-type EN10MB (Ethernet), capture size 65535 bytes
```

- 显示指定数量包

```javascript
[root@LVS ~]# tcpdump -c 10
```

- 转换阅读格式

```javascript
[root@LVS ~]# tcpdump -d
(000) ret      #65535
```

[root@LVS ~]

\#

#### 9.3 nmap

功能：也就是Network Mapper，是Linux下的网络扫描和嗅探工具包。

语法：

```javascript
nmap [Scan Type(s)] [Options] {target specification}
```

参数：

```javascript
TARGET SPECIFICATION:
  Can pass hostnames, IP addresses, networks, etc.
  Ex: scanme.nmap.org, microsoft.com/24, 192.168.0.1; 10.0.0-255.1-254
  -iL <inputfilename>: Input from list of hosts/networks
  -iR <num hosts>: Choose random targets
  --exclude <host1[,host2][,host3],...>: Exclude hosts/networks
  --excludefile <exclude_file>: Exclude list from file
HOST DISCOVERY:
  -sL: List Scan - simply list targets to scan
  -sn: Ping Scan - disable port scan
  -Pn: Treat all hosts as online -- skip host discovery
  -PS/PA/PU/PY[portlist]: TCP SYN/ACK, UDP or SCTP discovery to given ports
  -PE/PP/PM: ICMP echo, timestamp, and netmask request discovery probes
  -PO[protocol list]: IP Protocol Ping
  -n/-R: Never do DNS resolution/Always resolve [default: sometimes]
  --dns-servers <serv1[,serv2],...>: Specify custom DNS servers
  --system-dns: Use OS's DNS resolver
  --traceroute: Trace hop path to each host
SCAN TECHNIQUES:
  -sS/sT/sA/sW/sM: TCP SYN/Connect()/ACK/Window/Maimon scans
  -sU: UDP Scan
  -sN/sF/sX: TCP Null, FIN, and Xmas scans
  --scanflags <flags>: Customize TCP scan flags
  -sI <zombie host[:probeport]>: Idle scan
  -sY/sZ: SCTP INIT/COOKIE-ECHO scans
  -sO: IP protocol scan
  -b <FTP relay host>: FTP bounce scan
PORT SPECIFICATION AND SCAN ORDER:
  -p <port ranges>: Only scan specified ports
    Ex: -p22; -p1-65535; -p U:53,111,137,T:21-25,80,139,8080,S:9
  -F: Fast mode - Scan fewer ports than the default scan
  -r: Scan ports consecutively - don't randomize
  --top-ports <number>: Scan <number> most common ports
  --port-ratio <ratio>: Scan ports more common than <ratio>
SERVICE/VERSION DETECTION:
  -sV: Probe open ports to determine service/version info
  --version-intensity <level>: Set from 0 (light) to 9 (try all probes)
  --version-light: Limit to most likely probes (intensity 2)
  --version-all: Try every single probe (intensity 9)
  --version-trace: Show detailed version scan activity (for debugging)
SCRIPT SCAN:
  -sC: equivalent to --script=default
  --script=<Lua scripts>: <Lua scripts> is a comma separated list of 
           directories, script-files or script-categories
  --script-args=<n1=v1,[n2=v2,...]>: provide arguments to scripts
  --script-args-file=filename: provide NSE script args in a file
  --script-trace: Show all data sent and received
  --script-updatedb: Update the script database.
  --script-help=<Lua scripts>: Show help about scripts.
           <Lua scripts> is a comma separted list of script-files or
           script-categories.
OS DETECTION:
  -O: Enable OS detection
  --osscan-limit: Limit OS detection to promising targets
  --osscan-guess: Guess OS more aggressively
TIMING AND PERFORMANCE:
  Options which take <time> are in seconds, or append 'ms' (milliseconds),
  's' (seconds), 'm' (minutes), or 'h' (hours) to the value (e.g. 30m).
  -T<0-5>: Set timing template (higher is faster)
  --min-hostgroup/max-hostgroup <size>: Parallel host scan group sizes
  --min-parallelism/max-parallelism <numprobes>: Probe parallelization
  --min-rtt-timeout/max-rtt-timeout/initial-rtt-timeout <time>: Specifies
      probe round trip time.
  --max-retries <tries>: Caps number of port scan probe retransmissions.
  --host-timeout <time>: Give up on target after this long
  --scan-delay/--max-scan-delay <time>: Adjust delay between probes
  --min-rate <number>: Send packets no slower than <number> per second
  --max-rate <number>: Send packets no faster than <number> per second
FIREWALL/IDS EVASION AND SPOOFING:
  -f; --mtu <val>: fragment packets (optionally w/given MTU)
  -D <decoy1,decoy2[,ME],...>: Cloak a scan with decoys
  -S <IP_Address>: Spoof source address
  -e <iface>: Use specified interface
  -g/--source-port <portnum>: Use given port number
  --data-length <num>: Append random data to sent packets
  --ip-options <options>: Send packets with specified ip options
  --ttl <val>: Set IP time-to-live field
  --spoof-mac <mac address/prefix/vendor name>: Spoof your MAC address
  --badsum: Send packets with a bogus TCP/UDP/SCTP checksum
OUTPUT:
  -oN/-oX/-oS/-oG <file>: Output scan in normal, XML, s|<rIpt kIddi3,
     and Grepable format, respectively, to the given filename.
  -oA <basename>: Output in the three major formats at once
  -v: Increase verbosity level (use -vv or more for greater effect)
  -d: Increase debugging level (use -dd or more for greater effect)
  --reason: Display the reason a port is in a particular state
  --open: Only show open (or possibly open) ports
  --packet-trace: Show all packets sent and received
  --iflist: Print host interfaces and routes (for debugging)
  --log-errors: Log errors/warnings to the normal-format output file
  --append-output: Append to rather than clobber specified output files
  --resume <filename>: Resume an aborted scan
  --stylesheet <path/URL>: XSL stylesheet to transform XML output to HTML
  --webxml: Reference stylesheet from Nmap.Org for more portable XML
  --no-stylesheet: Prevent associating of XSL stylesheet w/XML output
MISC:
  -6: Enable IPv6 scanning
  -A: Enable OS detection, version detection, script scanning, and traceroute
  --datadir <dirname>: Specify custom Nmap data file location
  --send-eth/--send-ip: Send using raw ethernet frames or IP packets
  --privileged: Assume that the user is fully privileged
  --unprivileged: Assume the user lacks raw socket privileges
  -V: Print version number
  -h: Print this help summary page.
EXAMPLES:
  nmap -v -A scanme.nmap.org
  nmap -v -sn 192.168.0.0/16 10.0.0.0/8
  nmap -v -iR 10000 -Pn -p 80
```

实例：

- 获取远程主机的系统类型及开放端口

```javascript
 [root@LVS ~]# nmap -sS -P0 -sV -O 192.168.100.106Starting Nmap 6.40 ( http://nmap.org ) at 2020-04-16 07:57 CSTNmap scan report for 192.168.100.106 (192.168.100.106)Host is up (0.000064s latency).Not shown: 998 closed portsPORT    STATE SERVICE VERSION22/tcp  open  ssh     OpenSSH 6.6.1 (protocol 2.0)111/tcp open  rpcbind 2-4 (RPC #100000)Device type: general purposeRunning: Linux 3.XOS CPE: cpe:/o:linux:linux_kernel:3OS details: Linux 3.7 - 3.9Network Distance: 0 hopsOS and Service detection performed. Please report any incorrect results at http://nmap.org/submit/ .Nmap done: 1 IP address (1 host up) scanned in 7.91 seconds
这里的 < target > 可以是单一 IP, 或主机名，或域名，或子网
-sS TCP SYN 扫描 (又称半开放,或隐身扫描)
-P0 允许你关闭 ICMP pings.
-sV 打开系统版本检测
-O 尝试识别远程操作系统
其它选项:
-A 同时打开操作系统指纹和版本检测
-v 详细输出扫描情况.
```

- 列出开放了指定端口的主机列表

```javascript
[root@LVS ~]# nmap -sT -p 80 -oG – 192.168.100.* | grep open
80/tcp open  http
```

- 在网络寻找所有在线主机

```javascript
[root@LVS ~]# nmap -sP 192.168.100.*

Starting Nmap 6.40 ( http://nmap.org ) at 2020-04-16 08:06 CST
Nmap scan report for 192.168.100.1 (192.168.100.1)
Host is up (0.0031s latency).
MAC Address: 30:FC:68:35:C8:CC (Unknown)
Nmap scan report for 192.168.100.100 (192.168.100.100)
Host is up (0.058s latency).
MAC Address: 08:EA:40:F9:C8:40 (Unknown)
Nmap scan report for 192.168.100.101 (192.168.100.101)
Host is up (0.00012s latency).
MAC Address: E4:02:9B:9D:9D:24 (Unknown)
Nmap scan report for 192.168.100.103 (192.168.100.103)
Host is up (0.059s latency).
MAC Address: 50:1A:C5:F9:3D:DB (Unknown)
Nmap scan report for 192.168.100.104 (192.168.100.104)
Host is up (0.11s latency).
MAC Address: A4:D1:8C:24:2A:14 (Unknown)
Nmap scan report for 192.168.100.105 (192.168.100.105)
Host is up (0.079s latency).
MAC Address: D8:1C:79:34:87:21 (Unknown)
Nmap scan report for 192.168.100.108 (192.168.100.108)
Host is up (0.14s latency).
MAC Address: 0C:2C:54:14:D4:7F (Unknown)
Nmap scan report for 192.168.100.106 (192.168.100.106)
Host is up.
Nmap done: 256 IP addresses (8 hosts up) scanned in 2.91 seconds
```

[root@LVS ~]

\#

或者这样也行，指定子网

```javascript
root@LVS ~]# nmap -sP 192.168.100.0/24

Starting Nmap 6.40 ( http://nmap.org ) at 2020-04-16 08:07 CST
Nmap scan report for 192.168.100.1 (192.168.100.1)
Host is up (0.0050s latency).
MAC Address: 30:FC:68:35:C8:CC (Unknown)
Nmap scan report for 192.168.100.100 (192.168.100.100)
Host is up (0.017s latency).
MAC Address: 08:EA:40:F9:C8:40 (Unknown)
Nmap scan report for 192.168.100.101 (192.168.100.101)
Host is up (0.00018s latency).
MAC Address: E4:02:9B:9D:9D:24 (Unknown)
Nmap scan report for 192.168.100.103 (192.168.100.103)
Host is up (0.023s latency).
MAC Address: 50:1A:C5:F9:3D:DB (Unknown)
Nmap scan report for 192.168.100.105 (192.168.100.105)
Host is up (0.21s latency).
MAC Address: D8:1C:79:34:87:21 (Unknown)
Nmap scan report for 192.168.100.106 (192.168.100.106)
Host is up.
Nmap done: 256 IP addresses (6 hosts up) scanned in 3.64 seconds
```

[root@LVS ~]

\#

- Ping 指定范围内的 IP 地址

```javascript
 [root@LVS ~]# nmap -sP 192.168.100.100-200Starting Nmap 6.40 ( http://nmap.org ) at 2020-04-16 08:09 CSTNmap scan report for 192.168.100.100 (192.168.100.100)Host is up (0.043s latency).MAC Address: 08:EA:40:F9:C8:40 (Unknown)Nmap scan report for 192.168.100.101 (192.168.100.101)Host is up (0.00026s latency).MAC Address: E4:02:9B:9D:9D:24 (Unknown)Nmap scan report for 192.168.100.103 (192.168.100.103)Host is up (0.034s latency).MAC Address: 50:1A:C5:F9:3D:DB (Unknown)Nmap scan report for 192.168.100.106 (192.168.100.106)Host is up.Nmap done: 101 IP addresses (4 hosts up) scanned in 1.51 seconds[root@LVS ~]# 
```

- 在某段子网上查找未占用的 IP

```javascript
# nmap -T4 -sP 192.168.100.0/24 && egrep “00:00:00:00:00:00″ /proc/net/arpStarting Nmap 6.40 ( http://nmap.org ) at 2020-04-16 08:11 CSTNmap scan report for 192.168.100.1 (192.168.100.1)Host is up (0.0050s latency).MAC Address: 30:FC:68:35:C8:CC (Unknown)Nmap scan report for 192.168.100.100 (192.168.100.100)Host is up (0.11s latency).MAC Address: 08:EA:40:F9:C8:40 (Unknown)Nmap scan report for 192.168.100.101 (192.168.100.101)Host is up (0.00022s latency).MAC Address: E4:02:9B:9D:9D:24 (Unknown)Nmap scan report for 192.168.100.102 (192.168.100.102)Host is up (0.23s latency).MAC Address: 90:AD:F7:1A:21:C2 (Unknown)Nmap scan report for 192.168.100.103 (192.168.100.103)Host is up (0.11s latency).MAC Address: 50:1A:C5:F9:3D:DB (Unknown)Nmap scan report for 192.168.100.105 (192.168.100.105)Host is up (0.038s latency).MAC Address: D8:1C:79:34:87:21 (Unknown)Nmap scan report for 192.168.100.106 (192.168.100.106)Host is up.Nmap done: 256 IP addresses (7 hosts up) scanned in 5.22 seconds[root@LVS ~]# cat /proc/net/arpIP address       HW type     Flags       HW address            Mask     Device192.168.100.101  0x1         0x2         e4:02:9b:9d:9d:24     *        ens33192.168.100.103  0x1         0x2         50:1a:c5:f9:3d:db     *        ens33192.168.100.104  0x1         0x2         a4:d1:8c:24:2a:14     *        ens33192.168.100.108  0x1         0x2         0c:2c:54:14:d4:7f     *        ens33192.168.100.100  0x1         0x2         08:ea:40:f9:c8:40     *        ens33192.168.100.102  0x1         0x2         90:ad:f7:1a:21:c2     *        ens33192.168.100.105  0x1         0x2         d8:1c:79:34:87:21     *        ens33192.168.100.1    0x1         0x2         30:fc:68:35:c8:cc     *        ens33[root@LVS ~]# 
```

- 在局域网上扫找 Conficker 蠕虫病毒

```javascript
[root@LVS ~]# nmap -PN -T4 -p139,445 -n -v –script=smb-check-vulns –script-args safe=1 192.168.100.1-254
```

- 扫描网络上的恶意接入点 （rogue APs）

```javascript
[root@LVS ~]# nmap -A -p1-85,113,443,8080-8100 -T4 –min-hostgroup 50 –max-rtt-timeout 2000 –initial-rtt-timeout 300 –max-retries 3 –host-timeout 2ax-scan-delay 1000 -oA wapscan 192.168.100.0/24
```

- 使用诱饵扫描方法来扫描主机端口

```javascript
[root@LVS ~]# nmap -sS 192.168.100.106 -D 192.168.100.108

Starting Nmap 6.40 ( http://nmap.org ) at 2020-04-16 08:30 CST
Nmap scan report for 192.168.100.106 (192.168.100.106)
Host is up (0.000010s latency).
Not shown: 998 closed ports
PORT    STATE SERVICE
22/tcp  open  ssh
111/tcp open  rpcbind

Nmap done: 1 IP address (1 host up) scanned in 0.13 seconds
```

- 显示网络上共有多少台 Linux 及 Win 设备?

```javascript
# nmap -F -O 192.168.100.1-255 | grep "Running: " > /tmp/os; echo "$(cat /tmp/os | grep Linux | wc -l) Linux device(s)"; echo "$(cat /tmp/os | grep Windows | wc -l) Window(s) device"
RTTVAR has grown to over 2.3 seconds, decreasing to 2.0
RTTVAR has grown to over 2.3 seconds, decreasing to 2.0
RTTVAR has grown to over 2.3 seconds, decreasing to 2.0
RTTVAR has grown to over 2.3 seconds, decreasing to 2.0
RTTVAR has grown to over 2.3 seconds, decreasing to 2.0
RTTVAR has grown to over 2.3 seconds, decreasing to 2.0
RTTVAR has grown to over 2.3 seconds, decreasing to 2.0
1 Linux device(s)
1 Window(s) device
```

#### 9.4 lsof

功能：列出当前系统打开文件的工具。

语法：

```javascript
lsof -i[46] [protocol][@hostname|hostaddr][:service|port]
```

参数：

```javascript
Defaults in parentheses; comma-separated set (s) items; dash-separated ranges.
  -?|-h list help          -a AND selections (OR)     -b avoid kernel blocks
  -c c  cmd c ^c /c/[bix]  +c w  COMMAND width (9)    +d s  dir s files
  -d s  select by FD set   +D D  dir D tree *SLOW?*   +|-e s  exempt s *RISKY*
  -i select IPv[46] files  -K list tasKs (threads)    -l list UID numbers
  -n no host names         -N select NFS files        -o list file offset
  -O no overhead *RISKY*   -P no port names           -R list paRent PID
  -s list file size        -t terse listing           -T disable TCP/TPI info
  -U select Unix socket    -v list version info       -V verbose search
  +|-w  Warnings (+)       -X skip TCP&UDP* files     -Z Z  context [Z]
  -- end option scan     
  +f|-f  +filesystem or -file names     +|-f[gG] flaGs 
  -F [f] select fields; -F? for help  
  +|-L [l] list (+) suppress (-) link counts < l (0 = all; default = 0)
                                        +m [m] use|create mount supplement
  +|-M   portMap registration (-)       -o o   o 0t offset digits (8)
  -p s   exclude(^)|select PIDs         -S [t] t second stat timeout (15)
  -T qs TCP/TPI Q,St (s) info
  -g 展开 exclude(^)|select and print process group IDs
  -i i   select by IPv[46] address: [46][proto][@host|addr][:svc_list|port_list]
  +|-r [t[m<fmt>]] repeat every t seconds (15);  + until no files, - forever.
       An optional suffix to t is m<fmt>; m must separate t from <fmt> and
      <fmt> is an strftime(3) format for the marker line.
  -s p:s  exclude(^)|select protocol (p = TCP|UDP) states by name(s).
  -u s   exclude(^)|select login|UID set s
  -x [fl] cross over +d|+D File systems or symbolic Links
  names  select named files or files on named file systems
Anyone can list all files; /dev warnings disabled; kernel ID check disabled.
```





实例：

- 使用-i:port来显示与指定端口相关的网络信息(查看服务器端口22占用情况)

```javascript
# lsof -i:22
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd    1245 root    3u  IPv4  22280      0t0  TCP *:ssh (LISTEN)
sshd    1245 root    4u  IPv6  22292      0t0  TCP *:ssh (LISTEN)
sshd    3456 root    3u  IPv4  32140      0t0  TCP 192.168.100.106:ssh->192.168.100.103:62943 (ESTABLISHED)
```





- 使用-i显示所有连接

```javascript
# lsof -i
COMMAND    PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
systemd      1   root   46u  IPv6  15646      0t0  TCP *:sunrpc (LISTEN)
systemd      1   root   47u  IPv4  15647      0t0  TCP *:sunrpc (LISTEN)
chronyd    558 chrony    1u  IPv4  16920      0t0  UDP localhost:323 
chronyd    558 chrony    2u  IPv6  16921      0t0  UDP localhost:323 
chronyd    558 chrony    3u  IPv4  61445      0t0  UDP 192.168.100.106:51617->sv1.ggsrv.de:ntp 
avahi-dae  564  avahi   12u  IPv4  17552      0t0  UDP *:mdns 
avahi-dae  564  avahi   13u  IPv4  17559      0t0  UDP *:50471 
dhclient   769   root    6u  IPv4  20109      0t0  UDP *:bootpc 
dhclient   769   root   20u  IPv4  20098      0t0  UDP *:9399 
dhclient   769   root   21u  IPv6  20099      0t0  UDP *:tr-rsrb-port 
cupsd      986   root   11u  IPv6  22638      0t0  TCP localhost:ipp (LISTEN)
cupsd      986   root   12u  IPv4  22639      0t0  TCP localhost:ipp (LISTEN)
sshd      1245   root    3u  IPv4  22280      0t0  TCP *:ssh (LISTEN)
sshd      1245   root    4u  IPv6  22292      0t0  TCP *:ssh (LISTEN)
dnsmasq   2183 nobody    3u  IPv4  23602      0t0  UDP *:bootps 
dnsmasq   2183 nobody    5u  IPv4  23609      0t0  UDP 192.168.122.1:domain 
dnsmasq   2183 nobody    6u  IPv4  23610      0t0  TCP 192.168.122.1:domain (LISTEN)
sshd      3456   root    3u  IPv4  32140      0t0  TCP 192.168.100.106:ssh->192.168.100.103:62943 (ESTABLISHED)
sshd      3456   root    8u  IPv6  32247      0t0  TCP localhost:6011 (LISTEN)
sshd      3456   root    9u  IPv4  32248      0t0  TCP localhost:6011 (LISTEN)
rpcbind   3578    rpc    4u  IPv6  15646      0t0  TCP *:sunrpc (LISTEN)
rpcbind   3578    rpc    5u  IPv4  15647      0t0  TCP *:sunrpc (LISTEN)
rpcbind   3578    rpc    8u  IPv4  33103      0t0  UDP *:sunrpc 
rpcbind   3578    rpc    9u  IPv4  33104      0t0  UDP *:785 
rpcbind   3578    rpc   10u  IPv6  33105      0t0  UDP *:sunrpc 
rpcbind   3578    rpc   11u  IPv6  33106      0t0  UDP *:785 
```





- 使用-i 6仅获取IPv6流量

```javascript
# lsof -i 6
COMMAND   PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
systemd     1   root   46u  IPv6  15646      0t0  TCP *:sunrpc (LISTEN)
chronyd   558 chrony    2u  IPv6  16921      0t0  UDP localhost:323 
dhclient  769   root   21u  IPv6  20099      0t0  UDP *:tr-rsrb-port 
cupsd     986   root   11u  IPv6  22638      0t0  TCP localhost:ipp (LISTEN)
sshd     1245   root    4u  IPv6  22292      0t0  TCP *:ssh (LISTEN)
sshd     3456   root    8u  IPv6  32247      0t0  TCP localhost:6011 (LISTEN)
rpcbind  3578    rpc    4u  IPv6  15646      0t0  TCP *:sunrpc (LISTEN)
rpcbind  3578    rpc   10u  IPv6  33105      0t0  UDP *:sunrpc 
rpcbind  3578    rpc   11u  IPv6  33106      0t0  UDP *:785 
```

- 仅显示TCP连接（同理可获得UDP连接）

```javascript
# lsof -iTCP
COMMAND  PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
systemd    1   root   46u  IPv6  15646      0t0  TCP *:sunrpc (LISTEN)
systemd    1   root   47u  IPv4  15647      0t0  TCP *:sunrpc (LISTEN)
cupsd    986   root   11u  IPv6  22638      0t0  TCP localhost:ipp (LISTEN)
cupsd    986   root   12u  IPv4  22639      0t0  TCP localhost:ipp (LISTEN)
sshd    1245   root    3u  IPv4  22280      0t0  TCP *:ssh (LISTEN)
sshd    1245   root    4u  IPv6  22292      0t0  TCP *:ssh (LISTEN)
dnsmasq 2183 nobody    6u  IPv4  23610      0t0  TCP 192.168.122.1:domain (LISTEN)
sshd    3456   root    3u  IPv4  32140      0t0  TCP 192.168.100.106:ssh->192.168.100.103:62943 (ESTABLISHED)
sshd    3456   root    8u  IPv6  32247      0t0  TCP localhost:6011 (LISTEN)
sshd    3456   root    9u  IPv4  32248      0t0  TCP localhost:6011 (LISTEN)
rpcbind 3578    rpc    4u  IPv6  15646      0t0  TCP *:sunrpc (LISTEN)
rpcbind 3578    rpc    5u  IPv4  15647      0t0  TCP *:sunrpc (LISTEN)
```





- 使用@host来显示指定到指定主机的连接

```javascript
# lsof -i@192.168.100.103
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd    3456 root    3u  IPv4  32140      0t0  TCP 192.168.100.106:ssh->192.168.100.103:62943 (ESTABLISHED)
```









- 使用@host:port显示基于主机与端口的连接

```javascript
# lsof -i@192.168.100.106:22
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd    3456 root    3u  IPv4  32140      0t0  TCP 192.168.100.106:ssh->192.168.100.103:62943 (ESTABLISHED)
```





- 找出监听端口

```javascript
#  lsof  -i -sTCP:LISTEN
COMMAND  PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
systemd    1   root   46u  IPv6  15646      0t0  TCP *:sunrpc (LISTEN)
systemd    1   root   47u  IPv4  15647      0t0  TCP *:sunrpc (LISTEN)
cupsd    986   root   11u  IPv6  22638      0t0  TCP localhost:ipp (LISTEN)
cupsd    986   root   12u  IPv4  22639      0t0  TCP localhost:ipp (LISTEN)
sshd    1245   root    3u  IPv4  22280      0t0  TCP *:ssh (LISTEN)
sshd    1245   root    4u  IPv6  22292      0t0  TCP *:ssh (LISTEN)
dnsmasq 2183 nobody    6u  IPv4  23610      0t0  TCP 192.168.122.1:domain (LISTEN)
sshd    3456   root    8u  IPv6  32247      0t0  TCP localhost:6011 (LISTEN)
sshd    3456   root    9u  IPv4  32248      0t0  TCP localhost:6011 (LISTEN)
rpcbind 3578    rpc    4u  IPv6  15646      0t0  TCP *:sunrpc (LISTEN)
rpcbind 3578    rpc    5u  IPv4  15647      0t0  TCP *:sunrpc (LISTEN)
```





- 找出已建立的连接

```javascript
#  lsof  -i -sTCP:ESTABLISHED
COMMAND  PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
chronyd  558 chrony    3u  IPv4  61445      0t0  UDP 192.168.100.106:51617->107.76.76.144.in-addr.arpa:ntp 
sshd    3456   root    3u  IPv4  32140      0t0  TCP 192.168.100.106:ssh->192.168.100.103:62943 (ESTABLISHED)
```





- 使用-u显示指定用户打开了什么

```javascript
# lsof -u tomb
COMMAND  PID USER   FD   TYPE DEVICE  SIZE/OFF      NODE NAME
bash    5365 tomb  cwd    DIR    8,1      4096  67146817 /root
bash    5365 tomb  rtd    DIR    8,1      4096        64 /
bash    5365 tomb  txt    REG    8,1    960392 100816322 /usr/bin/bash
bash    5365 tomb  mem    REG    8,1     61752    127376 /usr/lib64/libnss_files-2.17.so
bash    5365 tomb  mem    REG    8,1 106070960  67377229 /usr/lib/locale/locale-archive
bash    5365 tomb  mem    REG    8,1   2116736    127358 /usr/lib64/libc-2.17.so
bash    5365 tomb  mem    REG    8,1     19344    127364 /usr/lib64/libdl-2.17.so
bash    5365 tomb  mem    REG    8,1    174520    187237 /usr/lib64/libtinfo.so.5.9
bash    5365 tomb  mem    REG    8,1    155064    127351 /usr/lib64/ld-2.17.so
bash    5365 tomb  mem    REG    8,1    123267  33773547 /usr/share/locale/zh_CN/LC_MESSAGES/bash.mo
bash    5365 tomb  mem    REG    8,1     26254 100784447 /usr/lib64/gconv/gconv-modules.cache
bash    5365 tomb    0u   CHR  136,0       0t0         3 /dev/pts/0
bash    5365 tomb    1u   CHR  136,0       0t0         3 /dev/pts/0
bash    5365 tomb    2u   CHR  136,0       0t0         3 /dev/pts/0
bash    5365 tomb  255u   CHR  136,0       0t0         3 /dev/pts/0
```





- 杀死指定用户所做的一切事情

```javascript
# kill  -9  `lsof -t -u tomb`
```

#### 9.5 nskookup

功能：查dns信息

语法：

```javascript
nslookup [-option] [name | -] [server]
```

实例：

```javascript
 # nslookup www.haopython.comServer:     192.168.1.1Address:    192.168.1.1#53Non-authoritative answer:Name:   www.haopython.comAddress: 212.64.86.215# nslookup > www.haopython.comServer:     192.168.1.1Address:    192.168.1.1#53Non-authoritative answer:Name:   www.haopython.comAddress: 212.64.86.215> set q=mx> mail.haopython.com         Server:     192.168.1.1Address:    192.168.1.1#53** server can't find mail.haopython.com: NXDOMAIN> mail.kzbd.cnServer:     192.168.1.1Address:    192.168.1.1#53Non-authoritative answer:mail.kzbd.cn    canonical name = mail.mxhichina.com.Authoritative answers can be found from:mxhichina.com    origin = dns1.hichina.com    mail addr = hostmaster.hichina.com    serial = 2018050200    refresh = 3600    retry = 1200    expire = 86400    minimum = 360> set q=any> haopython.comServer:     192.168.1.1Address:    192.168.1.1#53Non-authoritative answer:haopython.com   nameserver = dns23.hichina.com.haopython.com   nameserver = dns24.hichina.com.Authoritative answers can be found from:> 
```

#### 9.6 dig

功能：查询DNS包括NS记录，A记录，MX记录等相关信息的工具。

语法：

参数：

实例：

- 查找baidu.com的A记录：（此处一定是域而不是主机)

```javascript
# dig baidu.com A +noall +answer

; <<>> DiG 9.9.4-RedHat-9.9.4-37.el7 <<>> baidu.com A +noall +answer
;; global options: +cmd
baidu.com.		93	IN	A	220.181.38.148
baidu.com.		93	IN	A	39.156.69.79
```

- 查找baidu.com MX记录的列表

```javascript
# dig baidu.com MX +noall +answer

; <<>> DiG 9.9.4-RedHat-9.9.4-37.el7 <<>> baidu.com MX +noall +answer
;; global options: +cmd
baidu.com.		7200	IN	MX	20 mx50.baidu.com.
baidu.com.		7200	IN	MX	10 mx.maillb.baidu.com.
baidu.com.		7200	IN	MX	20 mx1.baidu.com.
baidu.com.		7200	IN	MX	20 jpmx.baidu.com.
baidu.com.		7200	IN	MX	15 mx.n.shifen.com.
```

- 查找baidu.com的权威DNS

```javascript
# dig baidu.com NS +noall +answer

; <<>> DiG 9.9.4-RedHat-9.9.4-37.el7 <<>> baidu.com NS +noall +answer
;; global options: +cmd
baidu.com.		69562	IN	NS	ns3.baidu.com.
baidu.com.		69562	IN	NS	ns7.baidu.com.
baidu.com.		69562	IN	NS	dns.baidu.com.
baidu.com.		69562	IN	NS	ns4.baidu.com.
baidu.com.		69562	IN	NS	ns2.baidu.com.
```

- 查询上面所有的记录

```javascript
# dig baidu.com ANY +noall +answer

; <<>> DiG 9.9.4-RedHat-9.9.4-37.el7 <<>> baidu.com ANY +noall +answer
;; global options: +cmd
baidu.com.		418	IN	A	39.156.69.79
baidu.com.		418	IN	A	220.181.38.148
baidu.com.		67962	IN	NS	dns.baidu.com.
baidu.com.		67962	IN	NS	ns3.baidu.com.
baidu.com.		67962	IN	NS	ns7.baidu.com.
baidu.com.		67962	IN	NS	ns2.baidu.com.
baidu.com.		67962	IN	NS	ns4.baidu.com.
```

- 以使用AAAA的选项查询主机的IPv6 AAAA记录

```javascript
# dig www.baidu.com AAAA +short
www.a.shifen.com.
```

- 精简模式快速输出

```javascript
# dig www.baidu.com +short
www.a.shifen.com.
220.181.38.149
220.181.38.150
```

#### 9.7 host

功能：host命令是常用的分析域名查询工具，可以用来测试域名系统工作是否正常。

语法：

```javascript
host(选项)(参数)
```

参数：

```javascript
-a	显示详细的DNS信息；
-c<类型>	指定查询类型，默认值为“IN“；
-C	查询指定主机的完整的SOA记录；
-r	在查询域名时，不使用递归的查询方式；
-t<类型>	指定查询的域名信息类型；
-v	显示指令执行的详细信息；
-w	如果域名服务器没有给出应答信息，则总是等待，直到域名服务器给出应答；
-W<时间>	指定域名查询的最长时间，如果在指定时间内域名服务器没有给出应答信息，则退出指令；
-4	使用IPv4；
-6	使用IPv6.
```

实例：

```javascript
# host www.haopython.com
www.haopython.com has address 212.64.86.215
```

#### 9.8 traceroute

功能：信息从你的计算机到互联网另一端的主机是走的什么路径。

语法：

```javascript
traceroute [ -46dFITnreAUDV ] [ -f first_ttl ] [ -g gate,... ] [ -i device ] [ -m max_ttl ] [ -N squeries ] [ -p port ] [ -t tos ] [ -l flow_label ] [ -w waittime ] [ -q nqueries ] [ -s src_addr ] [ -z sendwait ] [ --fwmark=num ] host [ packetlen ]
```

参数：

实例：

- 多命令使用（说明： -n 显示IP地址，不查主机名， -m 设置跳数 -q 4每个网关发送4个数据包 -w 把对外发探测包的等待响应时间设置为3秒）

```javascript
# traceroute -n -m 5 -q 4 -w 3 www.baidu.com
traceroute to www.baidu.com (220.181.38.149), 5 hops max, 60 byte packets
 1  192.168.100.1  1.694 ms  4.476 ms  4.253 ms  4.041 ms
 2  192.168.1.1  5.791 ms  7.691 ms  7.498 ms  7.276 ms
 3  10.10.64.1  8.125 ms  8.125 ms  7.739 ms  7.509 ms
 4  27.129.43.229  7.221 ms  8.176 ms  7.847 ms  7.635 ms
 5  27.129.43.237  15.968 ms  16.080 ms  17.177 ms  16.976 ms
```

### 十、磁盘相关操作命令

#### 10.1 mount

功能：用于挂载Linux系统外的文件。

语法：

```javascript
 mount [-lhV]
 mount -a [选项]
 mount [选项] [--source] <源> | [--target] <目录>
 mount [选项] <源> <目录>
 mount <操作> <挂载点> [<目标>]
```

参数：

```javascript
 -a, --all               挂载 fstab 中的所有文件系统
 -c, --no-canonicalize   不对路径规范化
 -f, --fake              空运行；跳过 mount(2) 系统调用
 -F, --fork              对每个设备禁用 fork(和 -a 选项一起使用)
 -T, --fstab <路径>      /etc/fstab 的替代文件
 -h, --help              显示此帮助并退出
 -i, --internal-only     不调用 mount.<类型> 助手程序
 -l, --show-labels       列出所有带有指定标签的挂载
 -n, --no-mtab           不写 /etc/mtab
 -o, --options <列表>    挂载选项列表，以英文逗号分隔
 -O, --test-opts <列表>  限制文件系统集合(和 -a 选项一起使用)
 -r, --read-only         以只读方式挂载文件系统(同 -o ro)
 -t, --types <列表>      限制文件系统类型集合
     --source <源>       指明源(路径、标签、uuid)
     --target <目标>     指明挂载点
 -v, --verbose           打印当前进行的操作
 -V, --version           显示版本信息并退出
 -w, --rw, --read-write  以读写方式挂载文件系统(默认)

 -h, --help     显示此帮助并退出
 -V, --version  输出版本信息并退出

源：
 -L, --label <标签>      同 LABEL=<label>
 -U, --uuid <uuid>       同 UUID=<uuid>
 LABEL=<标签>            按文件系统标签指定设备
 UUID=<uuid>             按文件系统 UUID 指定设备
 PARTLABEL=<标签>        按分区标签指定设备
 PARTUUID=<uuid>         按分区 UUID 指定设备
 <设备>                  按路径指定设备
 <目录>                  绑定挂载的挂载点(参阅 --bind/rbind)
 <文件>                  用于设置回环设备的常规文件

操作：
 -B, --bind              挂载其他位置的子树(同 -o bind)
 -M, --move              将子树移动到其他位置
 -R, --rbind             挂载其他位置的子树及其包含的所有挂载
 --make-shared           将子树标记为 共享
 --make-slave            将子树标记为 从属
 --make-private          将子树标记为 私有
 --make-unbindable       将子树标记为 不可绑定
 --make-rshared          递归地将整个子树标记为 共享
 --make-rslave           递归地将整个子树标记为 从属
 --make-rprivate         递归地将整个子树标记为 私有
 --make-runbindable      递归地将整个子树标记为 不可绑定
```

实例：

- 将 /dev/hda1 挂在 /mnt 之下。

```javascript
mount /dev/hda1 /mnt
```

- 将 /dev/hda1 用唯读模式挂在 /mnt 之下。

```javascript
mount -o ro /dev/hda1 /mnt
```

- 将 /tmp/image.iso 这个光碟的 image 档使用 loop 模式挂在 /mnt/cdrom之下。用这种方法可以将一般网络上可以找到的 Linux 光 碟 ISO 档在不烧录成光碟的情况下检视其内容。

```javascript
mount -o loop /tmp/image.iso /mnt/cdrom
```

#### 10.2 umount

功能：卸除文件系统。

语法：

```javascript
umount [-ahnrvV][-t <文件系统类型>][文件系统]
```

参数：

```javascript
-a 卸除/etc/mtab中记录的所有文件系统。
-h 显示帮助。
-n 卸除时不要将信息存入/etc/mtab文件中。
-r 若无法成功卸除，则尝试以只读的方式重新挂入文件系统。
-t<文件系统类型> 仅卸除选项中所指定的文件系统。
-v 执行时显示详细的信息。
-V 显示版本信息。
[文件系统] 除了直接指定文件系统外，也可以用设备名称或挂入点来表示文件系统。
```

实例：

- 分别通过设备名和挂载点卸载文件系统，同时输出详细信息

```javascript
# umount -v /dev/sda1          通过设备名卸载  
/dev/sda1 umounted  
# umount -v /mnt/mymount/      通过挂载点卸载  
/tmp/diskboot.img umounted 
```

- 如果设备正忙，卸载即告失败。卸载失败的常见原因是，某个打开的shell当前目录为挂载点里的某个目录：

```javascript
#umount -v /mnt/mymount/  
umount: /mnt/mymount: device is busy  
umount: /mnt/mymount: device is busy 
```

#### 10.3 fsck

功能：命令用于 检查与修复 Linux 档案系统，可以同时检查一个或多个 Linux 档案系统。

语法：

```javascript
fsck [-sACVRP] [-t fstype] [--] [fsck-options] filesys [...]
```

参数：

```javascript
filesys ： device 名称(eg./dev/sda1)，mount 点 (eg. / 或 /usr)
-t : 给定档案系统的型式，若在 /etc/fstab 中已有定义或 kernel 本身已支援的则不需加上此参数
-s : 依序一个一个地执行 fsck 的指令来检查
-A : 对/etc/fstab 中所有列出来的 partition 做检查
-C : 显示完整的检查进度
-d : 列印 e2fsck 的 debug 结果
-p : 同时有 -A 条件时，同时有多个 fsck 的检查一起执行
-R : 同时有 -A 条件时，省略 / 不检查
-V : 详细显示模式
-a : 如果检查有错则自动修复
-r : 如果检查有错则由使用者回答是否修复
```

实例：

- 自动修复

```javascript
# fsck -y /dev/sda1
fsck，来自 util-linux 2.23.2
/sbin/fsck.xfs: XFS file system.
```

#### 10.4 dd

功能： 转换或复制文件

用于读取、转换并输出数据。

dd可从标准输入或文件中读取数据，根据指定的格式来转换数据，再输出到文件、设备或标准输出。

语法：

```javascript
dd [操作数] ...
　或：dd 选项
```

参数：

```javascript
Copy a file, converting and formatting according to the operands.

  bs=BYTES        read and write up to BYTES bytes at a time
  cbs=BYTES       convert BYTES bytes at a time
  conv=CONVS      convert the file as per the comma separated symbol list
  count=N         copy only N input blocks
  ibs=BYTES       read up to BYTES bytes at a time (default: 512)
  if=FILE         read from FILE instead of stdin
  iflag=FLAGS     read as per the comma separated symbol list
  obs=BYTES       write BYTES bytes at a time (default: 512)
  of=FILE         write to FILE instead of stdout
  oflag=FLAGS     write as per the comma separated symbol list
  seek=N          skip N obs-sized blocks at start of output
  skip=N          skip N ibs-sized blocks at start of input
  status=LEVEL    The LEVEL of information to print to stderr;
                  'none' suppresses everything but error messages,
                  'noxfer' suppresses the final transfer statistics,
                  'progress' shows periodic transfer statistics

N and BYTES may be followed by the following multiplicative suffixes:
c =1, w =2, b =512, kB =1000, K =1024, MB =1000*1000, M =1024*1024, xM =M
GB =1000*1000*1000, G =1024*1024*1024, and so on for T, P, E, Z, Y.

Each CONV symbol may be:

  ascii     from EBCDIC to ASCII
  ebcdic    from ASCII to EBCDIC
  ibm       from ASCII to alternate EBCDIC
  block     pad newline-terminated records with spaces to cbs-size
  unblock   replace trailing spaces in cbs-size records with newline
  lcase     change upper case to lower case
  ucase     change lower case to upper case
  sparse    try to seek rather than write the output for NUL input blocks
  swab      swap every pair of input bytes
  sync      pad every input block with NULs to ibs-size; when used
            with block or unblock, pad with spaces rather than NULs
  excl		fail if the output file already exists
  nocreat	do not create the output file
  notrunc	不截断输出文件
  noerror	读取数据发生错误后仍然继续
  fdatasync	结束前将输出文件数据写入磁盘
  fsync	类似上面，但是元数据也一同写入

FLAG 符号可以是：

  append	追加模式(仅对输出有意义；隐含了conv=notrunc)
  direct	使用直接I/O 存取模式
  directory	除非是目录，否则 directory 失败
  dsync		使用同步I/O 存取模式
  sync		与上者类似，但同时也对元数据生效
  fullblock	为输入积累完整块(仅iflag)
  nonblock	使用无阻塞I/O 存取模式
  noatime	不更新存取时间
  nocache	丢弃缓存数据
  noctty	不根据文件指派控制终端
  nofollow	不跟随链接文件
  count_bytes  treat 'count=N' as a byte count (iflag only)
  skip_bytes  treat 'skip=N' as a byte count (iflag only)
  seek_bytes  treat 'seek=N' as a byte count (oflag only)

Sending a USR1 signal to a running 'dd' process makes it
print I/O statistics to standard error and then resume copying.

  $ dd if=/dev/zero of=/dev/null& pid=$!
  $ kill -USR1 $pid; sleep 1; kill $pid
  18335302+0 records in
  18335302+0 records out
  9387674624 bytes (9.4 GB) copied, 34.6279 seconds, 271 MB/s
```

实例：

- 制作系统盘

```javascript
# dd if=boot.img of=/dev/fd0 bs=1440k
```

- 将testfile文件中的所有英文字母转换为大写，然后转成为testfile_1文件

```javascript
 # cat testfile HELLO LINUX!  Linux is a free unix-type opterating system.  This is a linux testfile!  Linux test # dd if=testfile of=testfile_1 conv=ucase记录了0+1 的读入记录了0+1 的写出102字节(102 B)已复制，0.0003327 秒，307 kB/秒# cat testfiletestfile    testfile_1  # cat testfile_1HELLO LINUX!  LINUX IS A FREE UNIX-TYPE OPTERATING SYSTEM.  THIS IS A LINUX TESTFILE!  LINUX TEST # 
```

- 由标准输入设备读入字符串，并将字符串转换成大写后，再输出到标准输出设备

```javascript
# dd conv=ucase 
mytest
MYTEST
记录了0+1 的读入
记录了0+1 的写出
7字节(7 B)已复制，5.3222 秒，0.0 kB/秒
```

#### 10.5  fdisk

功能：磁盘分区命令，适用于 2TB以下磁盘分区。

语法：

```javascript
fdisk [必要参数][选择参数]
```

参数：

```javascript
必要参数：

-l 列出素所有分区表
-u 与"-l"搭配使用，显示分区数目
选择参数：

-s<分区编号> 指定分区
-v 版本信息
菜单操作说明

m ：显示菜单和帮助信息
a ：活动分区标记/引导分区
d ：删除分区
l ：显示分区类型
n ：新建分区
p ：显示分区信息
q ：退出不保存
t ：设置分区号
v ：进行分区检查
w ：保存修改
x ：扩展应用，高级功能
```

实例：

- 显示当前分区情况

```javascript
[root@localhost ~]# fdisk -l

磁盘 /dev/sda：42.9 GB, 42949672960 字节，83886080 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x000c72ed

   设备 Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200    83886079    40893440   8e  Linux LVM

磁盘 /dev/mapper/cl-root：39.7 GB, 39720058880 字节，77578240 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节


磁盘 /dev/mapper/cl-swap：2147 MB, 2147483648 字节，4194304 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
```

- 显示SCSI硬盘的每个分区情况

```javascript
[root@localhost ~]# fdisk -lu

磁盘 /dev/sda：42.9 GB, 42949672960 字节，83886080 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x000c72ed

   设备 Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200    83886079    40893440   8e  Linux LVM

磁盘 /dev/mapper/cl-root：39.7 GB, 39720058880 字节，77578240 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节


磁盘 /dev/mapper/cl-swap：2147 MB, 2147483648 字节，4194304 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
```

#### 10.6 parted

功能：磁盘分区命令，没有磁盘大小限制，常用于2TB以下磁盘分区。

语法：

```javascript
parted [参数] [设备]
```

参数：

```javascript
-i	交互式模式
-s	脚本模式，不提示用户
-l	列出所有块设备上的分区布局
-h	显示帮助信息
```

实例：

- 列出所有块设备上的分区布局

```javascript
[root@localhost ~]# parted -l
Model: VMware Virtual disk (scsi)
Disk /dev/sda: 42.9GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags: 

Number  Start   End     Size    Type     File system  标志
 1      1049kB  1075MB  1074MB  primary  xfs          启动
 2      1075MB  42.9GB  41.9GB  primary               lvm
```

- 磁盘设备分区

```javascript
[root@localhost ~]# parted
```

### 十一、系统权限及用户授权

#### 11.1 chmod

功能：改变文件或目录权限。

语法：

```javascript
chmod [参数] [文件]
```

参数：

```javascript
-c	若该文件权限确实已经更改，才显示其更改动作
-f	若该文件权限无法被更改也不显示错误讯息
-v	显示权限变更的详细资料
-R	对目前目录下的所有文件与子目录进行相同的权限变更(即以递回的方式逐个变更)
```

实例：

- 将档案 test.txt 设为所有人皆可读取

```javascript
[root@localhost ~]# chmod a+r test.txt
```

[root@localhost ~]

\# ll test.txt -rw-r--r-- 1 root root 0 4月 17 07:51 test.txt

[root@localhost ~]

\#

- 将目前目录下的所有文件与子目录皆设为任何人可读取

```javascript
[root@localhost mydirall]# chmod -R a+r * 
```

- 将 file.txt 设定为只有该文件拥有者可以执行

```javascript
[root@localhost ~]# chmod u+x test.txt
```

[root@localhost ~]

\# ll test.txt -rwxr--r-- 1 root root 0 4月 17 07:51 test.txt

[root@localhost ~]

\#

#### 11.2 chown

功能：改变文件或目录的属主和属组。

语法：

```javascript
chown [参数]
```

参数：

```javascript
-R	对目前目录下的所有文件与子目录进行相同的拥有者变更
-c	若该文件拥有者确实已经更改，才显示其更改动作
-f	若该文件拥有者无法被更改也不要显示错误讯息
-h	只对于连结(link)进行变更，而非该 link 真正指向的文件
-v	显示拥有者变更的详细资料
```

实例：

- 更改文件的属主和属组

```javascript
[root@localhost ~]# ll file1
-rw-r--r-- 1 root root 0 4月  17 20:54 file1
```

[root@localhost ~]

\# chown tom file1

[root@localhost ~]

\# ll file1 -rw-r--r-- 1 tom root 0 4月 17 20:54 file1

[root@localhost ~]

\# chown .tom file1

[root@localhost ~]

\# ll file1 -rw-r--r-- 1 tom tom 0 4月 17 20:54 file1

[root@localhost ~]

\#

[root@localhost ~]

\# chown tom.tom file1

[root@localhost ~]

\# ll file1 -rw-r--r-- 1 tom tom 0 4月 17 20:54 file1

[root@localhost ~]

\#

#### 11.3 chgrp

功能：更改文件用户组

语法：

```javascript
chgrp [参数] [目录]
```

参数：

```javascript
-c	效果类似”-v”参数，但仅回报更改的部分
-f	不显示错误信息
-h	对符号连接的文件作修改，而不更动其他任何相关文件
-R	递归处理，将指定目录下的所有文件及子目录一并处理
-v	显示指令执行过程
--reference	把指定文件或目录的所属群组全部设成和参考文件或目录的所属群组相同
```

实例：

- 更改文件属组为tom组

```javascript
[root@localhost ~]# ll file2
-rw-r--r-- 1 root root 0 4月  17 21:05 file2
```

[root@localhost ~]

\# chgrp tom file2

[root@localhost ~]

\# ll file2 -rw-r--r-- 1 root tom 0 4月 17 21:05 file2

[root@localhost ~]

\#

- 更改目录的属组，-p子目录也更改属组

```javascript
[root@localhost ~]# chgrp -R hr mytestdir
```

[root@localhost ~]

\# ll -d mytestdir drwxr-xr-x 3 root hr 20 4月 17 21:09 mytestdir

[root@localhost ~]

\# ll -d mytestdir/mydir2/ drwxr-xr-x 2 root hr 6 4月 17 21:09 mytestdir/mydir2/

#### 11.4 umask

功能：设定权限掩码

语法：

```javascript
umask [参数] [权限掩码]
```

参数：

```javascript
-S	以文字的方式来表示权限掩码
-p	输出的权限掩码可直接作为指令来执行
```

实例：

- 查看当前权限掩码

```javascript
[root@localhost ~]# umask
0022
```

- 使组用户的写权限，其他用户的读、写和执行权限都被取消

```javascript
[root@localhost ~]# umask u=,g=w,o=rwx
```

### 十二、查看系统用户登陆信息的命令

#### 12.1 whoami

功能：打印当前登录用户，本指令相当于执行”id -un”指令。

实例：

```javascript
[root@localhost ~]# id -un
root
```

[root@localhost ~]

\# whoami root

#### 12.2 who

功能：打印当前登录用户。

who命令的输出信息默认来自文件“/var/log/utmp”和“/var/log/wtmp”。

语法：

```javascript
who [参数] [选项]
```

参数：

```javascript
-a	打印全面信息
-b	打印系统最近启动时间
-d	打印死掉的进程
-l	打印系统登录进程
-H	带有列标题打印用户名，登录终端和登录时间
-t	打印系统上次锁定时间
-u	打印已登录用户列表
```

实例：

- 打印用户登录信息（-H 带有列标题打印）

```javascript
[root@localhost ~]# who -H
名称   线路       时间           备注
(unknown) :0           2020-02-11 00:14 (:0)
root     pts/0        2020-04-17 20:53 (123.183.158.11)
```

[root@localhost ~]

\#

- 打印系统最近启动时间

```javascript
[root@localhost ~]# who -b
         系统引导 2020-02-11 00:13
```

[root@localhost ~]

\#

- 打印系统登录进程

```javascript
[root@localhost ~]# who -l
```

#### 12.3 w

功能：显示已登录用户。

语法：

```javascript
w [参数]
```

参数：

```javascript
-h/--no-header	不打印头信息
-u/--no-current	当显示当前进程和cpu时间时忽略用户名
-s/--short	使用短输出格式
-f/--from	显示用户从哪登录
-o/--old-style	老式输出
-i/--ip-addr	显示IP地址而不是主机名（如果可能）
```

实例：

- 显示目前登入系统的用户信息

```javascript
[root@localhost ~]# w
 21:38:35 up 66 days, 21:25,  2 users,  load average: 0.00, 0.01, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
root     pts/0    123.183.158.11   20:53    3.00s  0.04s  0.00s w
```

[root@localhost ~]

\#

#### 12.4 last

功能： 显示用户或终端的登录情况

实例：

- 显示近期用户或终端的登录情况

```javascript
[root@localhost ~]# last
root     pts/0        123.183.158.11   Fri Apr 17 20:53   still logged in   
root     pts/0        123.183.158.11   Fri Apr 17 07:35 - 10:16  (02:41)    
root     pts/0        123.183.158.11   Tue Feb 11 22:27 - 01:37  (03:10)    
```

- 简略显示，并指定显示的个数

```javascript
[root@localhost ~]# last -n 5 -R
root     pts/0        Fri Apr 17 20:53   still logged in   
root     pts/0        Fri Apr 17 07:35 - 10:16  (02:41)    
root     pts/0        Tue Feb 11 22:27 - 01:37  (03:10)    
root     pts/0        Tue Feb 11 00:14 - 03:21  (03:06)    
(unknown :0           Tue Feb 11 00:14   still logged in   

wtmp begins Mon Apr  1 14:10:36 2019
```

[root@localhost ~]

\#

- 显示最后一列显示主机IP地址

```javascript
[root@localhost ~]# last -n 5 -a -i
root     pts/0        Fri Apr 17 20:53   still logged in    123.183.158.11
root     pts/0        Fri Apr 17 07:35 - 10:16  (02:41)     123.183.158.11
root     pts/0        Tue Feb 11 22:27 - 01:37  (03:10)     123.183.158.11
root     pts/0        Tue Feb 11 00:14 - 03:21  (03:06)     123.183.158.11
(unknown :0           Tue Feb 11 00:14   still logged in    0.0.0.0

wtmp begins Mon Apr  1 14:10:36 2019
```

[root@localhost ~]

\#

#### 12.5 lastlog

功能： 显示用户最近一次登录信息

参数：

```javascript
-b <天数>	显示指定天数前的登录信息
-t <天数>	显示指定天数以来的登录信息
-u <用户名>	显示指定用户的最近登录信息
```

实例：

- 显示系统中所有用户最近一次登录信息

```javascript
[root@localhost ~]# lastlog
用户名           端口     来自             最后登陆时间
root             pts/0    123.183.158.11   六 4月 18 08:20:06 +0800 2020
bin                                        **从未登录过**
daemon                                     **从未登录过**
adm                                        **从未登录过**
```

- 显示指定天数前的登录信息

```javascript
[root@localhost ~]# lastlog -b 2
```

- 显示指定天数以来的登录信息

```javascript
[root@localhost ~]# lastlog -t 7
用户名           端口     来自             最后登陆时间
root             pts/0    123.183.158.11   六 4月 18 08:20:06 +0800 2020
```

[root@localhost ~]

\#

- 显示指定用户的最近登录信息

```javascript
[root@localhost ~]# lastlog -u tom
用户名           端口     来自             最后登陆时间
tom              pts/1                     六 4月 18 08:24:21 +0800 2020
```

#### 12.6 users

功能：显示当前登录的用户

实例：

```javascript
[root@localhost ~]# users
(unknown) root root
```

[root@localhost ~]

\#

#### 12.7 finger

功能：查询其他使用者的资料

参数：

```javascript
-l	列出该用户的账号名称、真实姓名、用户根目录、登录所用的 shell、登录时间、邮件地址、电子邮件状态等
-m	排除查找用户的真实姓名
-s	列出该用户的帐号名称，真实姓名，登入终端机，闲置时间，登入时间以及地址和电话
-p	列出该用户的帐号名称，真实姓名，用户专属目录，登入所用的Shell，登入时间，转信地址，电子邮件状态，但不显示该用户的计划文件和方案文件内容
```

实例：

- 列出当前登录用户的相关信息

```javascript
Login: root           			Name: root
Directory: /root                    	Shell: /bin/bash
On since 六 4月 18 08:20 (CST) on pts/0 from 123.183.158.11
   5 seconds idle
On since 六 4月 18 08:26 (CST) on pts/2 from 123.183.158.11
   5 minutes 16 seconds idle
New mail received 四 4月 18 08:43 2019 (CST)
     Unread since 三 4月 17 17:57 2019 (CST)
No Plan.
```

[root@localhost ~]

\#

- 显示远程用户信息

```javascript
[root@localhost ~]# finger -m root@121.42.11.190
[121.42.11.190]
finger: connect: 拒绝连接
```

[root@localhost ~]

\#

### 十三、内置命令相关

#### 13.1 echo

功能：输出字符串或提取Shell变量的值

语法：

```javascript
echo [参数] [字符串]
```

参数：

```javascript
-n	不输出结尾的换行符
-e “\a”	发出警告音
-e “\b”	删除前面的一个字符
-e “\c”	结尾不加换行符
-e “\f”	换行，光标扔停留在原来的坐标位置
-e “\n”	换行，光标移至行首
-e “\r”	光标移至行首，但不换行
-E	禁止反斜杠转移，与-e参数功能相反
```

实例：

- 输出一段字符串

```javascript
[root@localhost ~]# echo "www.haopython.cm"
www.haopython.cm
```

[root@localhost ~]

\#

- 输出变量提取后的值

```javascript
[root@localhost ~]# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/www/wdlinux/mysql/bin:/root/bin
```

[root@localhost ~]

\#

- 对内容进行转义，不让$符号的提取变量值功能生效

```javascript
[root@localhost ~]# echo \$PATH
$PATH
```

[root@localhost ~]

\#

- 结合输出重定向符，将字符串信息导入文件中

```javascript
[root@localhost ~]# echo "It is a test" > mytest.txt
```

[root@localhost ~]

\# cat mytest.txt It is a test

[root@localhost ~]

\#

- 使用反引号符执行命令，并输出其结果到终端

```javascript
 [root@localhost ~]# echo `date`2020年 04月 18日 星期六 08:47:14 CST[root@localhost ~]# date2020年 04月 18日 星期六 08:47:17 CST[root@localhost ~]# 
```

- 输出带有换行符的内容

```javascript
[root@localhost ~]# echo -e "abc\ncdef\nghi"
abc
cdef
ghi
```

[root@localhost ~]

\#

#### 13.2 printf

功能：格式化 shell 输出

语法：

```javascript
 printf [格式控制字符串] [参数]
```

参数：

```javascript
\a	警告字符，通常为ASCII的BEL字符
\n	换行
\r	回车
\0ddd	表示1到3位的八进制值字符
\ddd	表示1到3位数八进制值的字符。仅在格式字符串中有效
```

实例：

- 字符串输出

```javascript
[root@localhost ~]# printf "HELLO WORLD\n"
HELLO WORLD
```

[root@localhost ~]

\#

- 格式化输出

```javascript
[root@localhost ~]# cat test.sh
printf "%-10s %-8s %-4s\n" 姓名 性别 体重kg
printf "%-10s %-8s %-4.2f\n" 郭靖 男 66.1234  
printf "%-10s %-8s %-4.2f\n" 杨过 男 48.6543  
printf "%-10s %-8s %-4.2f\n" 郭芙 女 47.9876 
```

[root@localhost ~]

\#

```javascript
[root@localhost ~]# sh test.sh
姓名     性别   体重kg
郭靖     男      66.12
杨过     男      48.65
郭芙     女      47.99
```

[root@localhost ~]

\#

#### 13.3 rpm

功能： RPM软件包管理器

rpm命令包含了五种基本功能：安装、卸载、升级、查询和验证。

语法：

```javascript
rpm [参数] [软件包]
```

参数：

```javascript
-a	查询所有的软件包
-b或-t	设置包装套件的完成阶段，并指定套件档的文件名称；
-c	只列出组态配置文件，本参数需配合”-l”参数使用
-d	只列出文本文件，本参数需配合”-l”参数使用
-e或--erase	卸载软件包
-f	查询文件或命令属于哪个软件包
-h或--hash	安装软件包时列出标记
-i	显示软件包的相关信息
--install	安装软件包
-l	显示软件包的文件列表
-p	查询指定的rpm软件包
-q	查询软件包
-R	显示软件包的依赖关系
-s	显示文件状态，本参数需配合”-l”参数使用
-U或--upgrade	升级软件包
-v	显示命令执行过程
-vv	详细显示指令执行过程
```

- 直接安装软件包

```javascript
[root@localhost ~]# rpm -ivh packge.rpm 
```

- 忽略报错，强制安装

```javascript
[root@localhost ~]# rpm --force -ivh package.rpm
```

- 列出所有安装过的包

```javascript
[root@localhost ~]# rpm -qa
```

- 查询rpm包中的文件安装的位置

```javascript
[root@localhost ~]# rpm -ql nginx
/etc/logrotate.d/nginx
/etc/nginx/fastcgi.conf
/etc/nginx/fastcgi.conf.default
```

- 卸载rpm包

```javascript
[root@localhost ~]# rpm -e package.rpm
```

- 升级软件包

```javascript
[root@localhost ~]# rpm -U file.rpm
```

#### 13.4 yum

功能：基于RPM的软件包管理器

语法：

```javascript
yum [参数]
```

参数：

```javascript
-y	对所有的提问都回答“yes”
-c	指定配置文件
-q	安静模式
-v	详细模式
-t	检查外部错误
-d	设置调试等级（0-10）
-e	设置错误等级（0-10）
-R	设置yum处理一个命令的最大等待时间
-C	完全从缓存中运行，而不去下载或者更新任何头文件
install	安装rpm软件包
update	更新rpm软件包
check-update	检查是否有可用的更新rpm软件包
remove	删除指定的rpm软件包
list	显示软件包的信息
search	检查软件包的信息
info	显示指定的rpm软件包的描述信息和概要信息
clean	清理yum过期的缓存
shell	进入yum的shell提示符
resolvedep	显示rpm软件包的依赖关系
localinstall	安装本地的rpm软件包
localupdate	显示本地rpm软件包进行更新
deplist	显示rpm软件包的所有依赖关系
```

实例：

- 自动搜索最快镜像插件

```javascript
[root@localhost ~]# yum install yum-fastestmirror
已加载插件：fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.bit.edu.cn
 * epel: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
正在解决依赖关系
--> 正在检查事务
---> 软件包 yum-plugin-fastestmirror.noarch.0.1.1.31-40.el7 将被 升级
---> 软件包 yum-plugin-fastestmirror.noarch.0.1.1.31-52.el7 将被 更新
--> 解决依赖关系完成
```

- 安装yum图形窗口插件

```javascript
[root@localhost ~]# yum install yumex
```

- 清除缓存目录下的软件包

```javascript
[root@localhost ~]# yum clean packages
已加载插件：fastestmirror, langpacks
正在清理软件源： base docker-ce-stable epel extras updates
1527 package 文件已移除
```

[root@localhost ~]

\#

- 卸载/删除vim-common

```javascript
[root@localhost ~]# yum remove vim-common.x86_64  
已加载插件：fastestmirror, langpacks
```

- 列出匹配到“foo*”的可用的数据包

```javascript
[root@localhost ~]# yum list available 'foo*' 
已加载插件：fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.bit.edu.cn
 * epel: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
可安装的软件包
foomatic.x86_64                                                                                    4.0.9-9.el7                                                                               base
foomatic-db.noarch                                                                                 4.0-41.20130911.el7                                                                       base
foomatic-db-filesystem.noarch                                                                      4.0-41.20130911.el7                                                                       base
foomatic-db-ppds.noarch                                                                            4.0-41.20130911.el7                                                                       base
foomatic-filters.x86_64                                                                            4.0.9-9.el7                                                                               base
```

[root@localhost ~]

\#

- 查看可能批量安装的列表

```javascript
[root@localhost ~]# yum grouplist  
已加载插件：fastestmirror, langpacks
没有安装组信息文件
Maybe run: yum groups mark convert (see man yum)
Loading mirror speeds from cached hostfile
 * base: mirror.bit.edu.cn
 * epel: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
可用的环境分组：
   最小安装
   基础设施服务器
   计算节点
   文件及打印服务器
   Cinnamon 桌面环境
   MATE 桌面环境
   基本网页服务器
   虚拟化主机
   带 GUI 的服务器
   GNOME 桌面
   KDE Plasma Workspaces
   开发及生成工作站
可用组：
   Cinnamon
   Fedora Packager
   Haskell
   LXQt 桌面环境
   MATE
   Milkymist
   TurboGears 应用程序构架
   Xfce
   传统 UNIX 兼容性
   兼容性程序库
   图形管理工具
   安全性工具
   开发工具
   控制台互联网工具
   教育软件
   智能卡支持
   电子实验室
   科学记数法支持
   系统管理
   系统管理工具
   通用桌面
```

- 显示httpd安装包信息

```javascript
[root@localhost ~]# yum info httpd.x86_64   
已加载插件：fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.bit.edu.cn
 * epel: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
已安装的软件包
名称    ：httpd
架构    ：x86_64
版本    ：2.4.6
发布    ：90.el7.centos
大小    ：9.4 M
源    ：installed
来自源：base
简介    ： Apache HTTP Server
网址    ：http://httpd.apache.org/
协议    ： ASL 2.0
描述    ： The Apache HTTP Server is a powerful, efficient, and extensible
         : web server.
```

[root@localhost ~]

\#

#### 13.5 watch

功能：周期性的执行给定的命令，并将命令的输出以全屏方式显示

语法：

```javascript
watch [参数] [命令]
```

参数：

```javascript
-n/--interval	watch默认每2秒运行一下程序，可以用-n或-interval来指定间隔的时间
-d/--differences	用-d或--differences 选项watch 会高亮显示变化的区域。 而-d=cumulative选项会把变动过的地方(不管最近的那次有没有变动)都高亮显示出来
-t/--no-title	关闭watch命令在顶部的时间间隔、命令、当前时间的输出
```

实例：

- 重复执行uptime命令

```javascript
[root@localhost ~]# watch uptime
```

- 每隔一秒高亮显示网络链接数的变化情况

```javascript
[root@localhost ~]# watch -n 1 -d netstat -ant
```

- 每10秒一次输出系统的平均负载

```javascript
[root@localhost ~]# watch -n 10 'cat /proc/loadavg'
```

- 监测磁盘inode和block数目变化情况

```javascript
[root@localhost ~]#  watch -n 1 "df -i;df"
```

- 监测当前目录中test.txt文件的变化

```javascript
[root@localhost ~]# watch -d 'ls -l|grep test.txt'
```

#### 13.6 history

功能：查看命令执行的历史纪录。

语法：

```javascript
history [参数] [目录]
```

参数：

```javascript
-a	将当前shell会话的历史命令追加到命令历史文件中,命令历史文件是保存历史命令的配置文件
-c	清空当前历史命令列表
-d	删除历史命令列表中指定序号的命令
-n	从命令历史文件中读取本次Shell会话开始时没有读取的历史命令
-r	读取命令历史文件到当前的Shell历史命令内存缓冲区
-s	将指定的命令作为单独的条目加入命令历史内存缓冲区。在执行添加之前先删除命令历史内存缓冲区中最后一条命令
-w	把当前的shell历史命令内存缓冲区的内容写入命令历史文件
```

- 显示最近的10条命令

```javascript
[root@localhost ~]# history 10
  800  yum info httpd.x86_64   
  801  yum info nginx
  802  watch uptime
  803  watch -n 1 -d netstat -ant
  804  watch -n 1 -d netstat -ant > a.txt
  805  cat a.txt
  806  watch -n 10 'cat /proc/loadavg'
  807   watch -n 1 "df -i;df"
  808  watch -d 'ls -l|grep test.txt'
  809  history 10
```

[root@localhost ~]

\#

- 将本次登录的命令写入历史文件中

```javascript
[root@localhost ~]# history -w
```

- 将命令历史文件中的内容读入到目前shell的history记忆中

```javascript
[root@localhost ~]# history -r  
```

- 将当前Shell会话的历史命令追加到命令历史文件中

```javascript
[root@localhost ~]# history -a
```

- 清空当前历史命令列表

```javascript
root@localhost ~]# history -c
```

#### 13.7 nc

功能：功能强大的网络工具。

语法：

```javascript
nc [参数]
```

参数：

```javascript
-l	使用监听模式，管控传入的资料
-p	设置本地主机使用的通信端口
-s	设置本地主机送出数据包的IP地址
-u	使用UDP传输协议
-v	显示指令执行过程
-w	设置等待连线的时间
-z	使用0输入/输出模式，只在扫描通信端口时使用
```

实例：

- 扫描80端口

```javascript
[root@localhost ~]# nc -nvv 10.128.25.130 80
Ncat: Version 6.40 ( http://nmap.org/ncat )
libnsock nsi_new2(): nsi_new (IOD #1)
libnsock nsock_connect_tcp(): TCP connection requested to 10.128.25.130:80 (IOD #1) EID 8
libnsock nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 8 [10.128.25.130:80]
Ncat: Connected to 10.128.25.130:80.
libnsock nsi_new2(): nsi_new (IOD #2)
libnsock nsock_read(): Read request from IOD #1 [10.128.25.130:80] (timeout: -1ms) EID 18
libnsock nsock_readbytes(): Read request for 0 bytes from IOD #2 [peer unspecified] EID 26
```

- 扫描UDP端口

```javascript
[root@localhost ~]# nc -u -w2 10.128.25.130 1-1000
```

- 扫描TCP端口

```javascript
[root@localhost ~]# nc -v -w2 10.128.25.130 1-1000
Ncat: Version 6.40 ( http://nmap.org/ncat )
Ncat: Connection refused.
```

[root@localhost ~]

\#

#### 13.8 type

功能：显示指定命令的类型

语法：

```javascript
type [参数] [命令]
```

参数：

```javascript
lias	别名
keyword	关键字，Shell保留字
function	函数，Shell函数
builtin	内建命令，Shell内建命令
file	文件，磁盘文件，外部命令
unfound	没有找到
```

实例：

- 别名命令

```javascript
[root@localhost ~]# type ls
ls 是 `ls --color=auto' 的别名
```

[root@localhost ~]

\# type ll ll 是 `ls -l --color=auto' 的别名

[root@localhost ~]

\#

- 内建命令

```javascript
[root@localhost ~]# type cd
cd 是 shell 内嵌
```

[root@localhost ~]

\#

- 关键字

```javascript
[root@localhost ~]# type if
if 是 shell 关键字
```

[root@localhost ~]

\#

#### 13.9 bc

功能：浮点运算

语法：

```javascript
bc [选项]
```

参数：

```javascript
-i	强制进入交互式模式
-l	定义使用的标准数学库
-w	定义使用的标准数学库
-q	打印正常的GNU bc环境信息
```

实例：

- 算术操作高级运算bc命令它可以执行浮点运算和一些高级函数

```javascript
[root@localhost ~]# echo "1.212*3" | bc 
3.636
```

[root@localhost ~]

\# echo "1.212*3" 1.212*3

- 设定小数精度（数值范围）

```javascript
[root@localhost ~]# echo "scale=2;3/8" | bc
.37
```

[root@localhost ~]

\#

- 计算平方和平方根

```javascript
[root@localhost ~]# echo "10^10" | bc
10000000000
```

[root@localhost ~]

\# echo "sqrt(100)" | bc 10

#### 13.10 time

功能： 指令执行时所消耗的时间

语法：

```javascript
time [参数] [命令]
```

参数：

```javascript
-o	设定结果输出档。这个选项会将 time 的输出写入 所指定的档案中。
-a	配合 -o 使用，会将结果写到档案的末端，而不会覆盖掉原来的内容。
-f FORMAT	以 FORMAT 字串设定显示方式。
```

实例：

- 显示命令date的时间统计结果

```javascript
[root@localhost ~]# time date
2020年 04月 18日 星期六 10:00:27 CST

real	0m0.001s
user	0m0.000s
sys	0m0.001s
```

[root@localhost ~]

\#

### 十四、关机 /重启 / 注销

#### 14.1 shutdown

功能：关闭服务器

语法：

```javascript
shutdown [选项] [参数]
```

参数：

```javascript
-c	当执行“shutdown -h 11:50”指令时，只要按+键就可以中断关机的指令
-f	重新启动时不执行fsck
-F	重新启动时执行fsck
-h	将系统关机
-k	只是送出信息给所有用户，但不会实际关机
-n	不调用init程序进行关机，而由shutdown自己进行
-r	shutdown之后重新启动
-t	送出警告信息和删除信息之间要延迟多少秒
```

实例：

- 指定现在立即关机

```javascript
[root@localhost ~]# shutdown -h now
```

- 指定5分钟后关机，同时送出警告信息给登入用户

```javascript
[root@localhost ~]#  shutdown +5 "System will shutdown after 5 minutes" 
Shutdown scheduled for 六 2020-04-18 10:13:03 CST, use 'shutdown -c' to cancel.
```

[root@localhost ~]

\# Broadcast message from root@localhost.localdomain (Sat 2020-04-18 10:08:03 CST): System will shutdown after 5 minutes The system is going down for power-off at Sat 2020-04-18 10:13:03 CST!

- 取消按预定时间关闭系统

```javascript
[root@localhost ~]# shutdown -c

Broadcast message from root@localhost.localdomain (Sat 2020-04-18 10:08:13 CST):

The system shutdown has been cancelled at Sat 2020-04-18 10:09:13 CST!
```

[root@localhost ~]

\#

- 重启服务器

```javascript
[root@localhost ~]# shutdown -r now
```

- 按预定时间关闭系统

```javascript
[root@localhost ~]# shutdown -h hours:minutes
```

#### 14.2 halt

功能：关机

halt命令会先检测系统的runlevel，若runlevel为0或6，则关闭系统，否则即调用shutdown来关闭系统。

参数：

```javascript
-w	并不是真正的重启或关机，只是写wtmp(/var/log/wtmp)纪录
-d	不写wtmp纪录(已包含在选项[-n]中)
-f	没有调用shutdown而强制关机或重启
-i	关机(或重启)前关掉所有的网络接口
-p	该选项为缺省选项，就是关机时调用poweroff
```

实例：

- 使用-p参数关闭操作系统，等同于poweroff 命令，并关闭了操作系统的电源

```javascript
[root@localhost ~]# halt -p
```

- 使用-f参数强制关机，操作系统电源未关闭

```javascript
[root@localhost ~]# halt -f
```

#### 14.3 poweroff

功能：关闭操作系统并切断电源

语法：

```javascript
poweroff [参数]
```

参数：

```javascript
-n	关闭操作系统时不执行sync操作
-w	不真正关闭操作系统，仅在日志文件“/var/log/wtmp”中
-d	关闭操作系统时，不将操作写入日志文件“/var/log/wtmp”中添加相应的记录
-f	强制关闭操作系统
-i	关闭操作系统之前关闭所有的网络接口
-h	关闭操作系统之前将系统中所有的硬件设置为备用模式
```

实例：

- 关闭计算机操作系统并且切断系统电源

```javascript
[root@localhost ~]# poweroff
```

- 使用-h参数关闭操作系统之前将系统中所有的硬件设置为备用模式

```javascript
[root@localhost ~]# poweroff -h
```

#### 14.4 logout

功能： 退出系统

实例：

```javascript
[root@localhost ~]# logout
```

#### 14.5 exit

功能：退出shell

执行exit可使shell以指定的状态值退出。若不设置状态值参数，则shell以预设值退出。状态值0代表执行成功，其他值代表执行失败。状态值参数多用于脚本中，在终端状态下，直接输入“exit” 退出终端 。

快捷键：ctrl+d

参数：

```javascript
0	执行成功
1	执行失败
$?	参照上一个状态值
```

实例：

- 退出当前shell

```javascript
[tom@localhost root]$ exit
exit
```

[root@localhost ~]

\#

### 十五、进程管理相关命令

#### 15.1 runlevel

功能：显示系统的运行级

runlevel命令是读取/var/run/utmp/文件或指定的其他文件，检索其中最近的运行级记录，显示系统先前与当前的运行级。

如果运行级记录中没有先前的运行级，则使用字符N表示先前的运行级。如果找不到运行级记录，runlevel将会输出一条表示不知道的错误信息，然后退出，返回一个非0的结束状态。

实例：

```javascript
[root@localhost ~]# runlevel
N 5
```

#### 15.2 jobs

功能： 查看当前有多少在后台运行的命令

语法：

```javascript
 jobs [参数] [目录]
```

参数：

```javascript
-l	显示作业列表时包括进程号
-n	显示上次使用jobs后状态发生变化的作业
-p	显示作业列表时仅显示其对应的进程号
-r	仅显示运行的（running）作业
-s	仅显示暂停的（stopped）作业
```

实例：

- 显示当前的作业列表

```javascript
[root@localhost ~]# jobs 
```

- 显示当前的作业号以及对应的进程号

```javascript
[root@localhost ~]# jobs -l
```

- 仅显示运行的作业

```javascript
[root@localhost ~]# jobs -r
```

- 仅显示暂停的作业

```javascript
[root@localhost ~]# jobs -s
```

- 显示上次使用jobs后状态发生变化的作业

```javascript
[root@localhost ~# jobs -n 
```

#### 15.3 kill

功能：杀死进程

kill命令可将指定的信号发送给相应的进程或工作。 kill命令默认使用信号为15，用于结束进程或工作。如果进程或工作忽略此信号，则可以使用信号9，强制杀死进程或作业。

语法：

```javascript
kill [参数] [进程号]
```

参数：

```javascript
-l	列出系统支持的信号
-s	指定向进程发送的信号
-a	处理当前进程时不限制命令名和进程号的对应关系
-p	指定kill命令只打印相关进程的进程号，而不发送任何信号
```

实例：

- 列出系统支持的信号列表

```javascript
[root@localhost ~]# kill -l
 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
16) SIGSTKFLT	17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU	25) SIGXFSZ
26) SIGVTALRM	27) SIGPROF	28) SIGWINCH	29) SIGIO	30) SIGPWR
31) SIGSYS	34) SIGRTMIN	35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3
38) SIGRTMIN+4	39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7
58) SIGRTMAX-6	59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
63) SIGRTMAX-1	64) SIGRTMAX	
```

[root@localhost ~]

\#

- 查找进程，并用kill杀掉

```javascript
[root@localhost ~]# ps
  PID TTY          TIME CMD
25357 pts/1    00:00:00 bash
25572 pts/1    00:00:00 ps
```

[root@localhost ~]

\# kill -9 25337

[root@localhost ~]

\#

#### 15.4 killall

功能： 使用进程名称来杀死一组进程

参数：

```javascript
killall [参数]
```

语法：

```javascript
-e	对长名称进行精确匹配
-l	打印所有已知信号列表
-p	杀死进程所属的进程组
-i	交互式杀死进程，杀死进程前需要进行确认
-r	使用正规表达式匹配要杀死的进程名称
-s	用指定的进程号代替默认信号“SIGTERM”
-u	杀死指定用户的进程
```

实例：

- 杀死httpd所有进程

```javascript
[root@localhost ~]# killall -9 httpd
```

- 使用-l参数打印所有已知信号列表

```javascript
[root@localhost ~]# killall -l
HUP INT QUIT ILL TRAP ABRT IOT BUS FPE KILL USR1 SEGV USR2 PIPE ALRM TERM
STKFLT CHLD CONT STOP TSTP TTIN TTOU URG XCPU XFSZ VTALRM PROF WINCH IO PWR SYS
UNUSED
```

[root@localhost ~]

\#

#### 15.5 pkill

功能：pkill命令可以按照进程名杀死进程。pkill和killall应用方法差不多，也是直接杀死运行中的程序。如果想杀掉单个进程，请用kill来杀掉。

语法：

```javascript
pkill [参数]
```

参数：

```javascript
-o	仅向找到的最小（起始）进程号发送信号
-n	仅向找到的最大（结束）进程号发送信号
-P	指定父进程号发送信号
-g	指定进程组
-t	指定开启进程的终端
```

实例：

- 仅向找到的最小（起始）进程号发送信号

```javascript
[root@localhost ~]# pkill -o
```

- 仅向找到的最大（结束）进程号发送信号

```javascript
[root@localhost ~]# pkill -n
```

- 杀死Apache进程

```javascript
[root@localhost ~]# pkill httpd
```

#### 15.6 ps

功能：显示进程状态

语法：

```javascript
ps [参数]
```

参数：

```javascript
-a	显示所有终端机下执行的程序，除了阶段作业领导者之外
a	显示现行终端机下的所有程序，包括其他用户的程序
-A	显示所有程序
-c	显示CLS和PRI栏位
c	列出程序时，显示每个程序真正的指令名称，而不包含路径，选项或常驻服务的标示
-C <指令名称>	指定执行指令的名称，并列出该指令的程序的状况
-d	显示所有程序，但不包括阶段作业领导者的程序
-e	此选项的效果和指定”A”选项相同
e	列出程序时，显示每个程序所使用的环境变量
-f	显示UID,PPIP,C与STIME栏位
f	用ASCII字符显示树状结构，表达程序间的相互关系
-g <群组名称>	此选项的效果和指定”-G”选项相同，当亦能使用阶段作业领导者的名称来指定
g	显示现行终端机下的所有程序，包括群组领导者的程序
-G <群组识别码>	列出属于该群组的程序的状况，也可使用群组名称来指定
h	不显示标题列
-H	显示树状结构，表示程序间的相互关系
-j或j	采用工作控制的格式显示程序状况
-l或l	采用详细的格式来显示程序状况
L	列出栏位的相关信息
-m或m	显示所有的执行绪
n	以数字来表示USER和WCHAN栏位
-N	显示所有的程序，除了执行ps指令终端机下的程序之外
-p <程序识别码>	指定程序识别码，并列出该程序的状况
p <程序识别码>	此选项的效果和指定”-p”选项相同，只在列表格式方面稍有差异
r	只列出现行终端机正在执行中的程序
-s <阶段作业>	指定阶段作业的程序识别码，并列出隶属该阶段作业的程序的状况
s	采用程序信号的格式显示程序状况
S	列出程序时，包括已中断的子程序资料
-t <终端机编号>	指定终端机编号，并列出属于该终端机的程序的状况
t <终端机编号>	此选项的效果和指定”-t”选项相同，只在列表格式方面稍有差异
-T	显示现行终端机下的所有程序
-u <用户识别码>	此选项的效果和指定”-U”选项相同
u	以用户为主的格式来显示程序状况
-U <用户识别码>	列出属于该用户的程序的状况，也可使用用户名称来指定
U <用户名称>	列出属于该用户的程序的状况
v	采用虚拟内存的格式显示程序状况
-V或V	显示版本信息
-w或w	采用宽阔的格式来显示程序状况
x	显示所有程序，不以终端机来区分
X	采用旧式的Linux i386登陆格式显示程序状况
-y	配合选项”-l”使用时，不显示F(flag)栏位，并以RSS栏位取代ADDR栏位
-- <程序识别码>	此选项的效果和指定”p”选项相同
--cols <每列字符数>	设置每列的最大字符数
--columns <每列字符数>	此选项的效果和指定”--cols”选项相同
--cumulative	此选项的效果和指定”S”选项相同
--deselect	此选项的效果和指定”-N”选项相同
--forest	此选项的效果和指定”f”选项相同
--headers	重复显示标题列
--help	在线帮助
--info	显示排错信息
--lines <显示列数>	设置显示画面的列数
--no-headers	此选项的效果和指定”h”选项相同，只在列表格式方面稍有差异
--group <群组名称>	此选项的效果和指定”-G”选项相同
--Group <群组识别码>	此选项的效果和指定”-G”选项相同
--pid <程序识别码>	此选项的效果和指定”-p”选项相同
--rows <显示列数>	此选项的效果和指定”--lines”选项相同
--sid <阶段作业>	此选项的效果和指定”-s”选项相同
-tty <终端机编号>	此选项的效果和指定”-t”选项相同
--user <用户名称>	此选项的效果和指定”-U”选项相同
--User <用户识别码>	此选项的效果和指定”-U”选项相同
--version	此选项的效果和指定”-V”选项相同
--widty <每列字符数>	此选项的效果和指定”-cols”选项相同
```

实例：

- 把所有进程显示出来

```javascript
[root@localhost ~]# ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1  43164  3636 ?        Ss   2月11   0:45 /usr/lib/systemd/systemd --system --deserialize 13
root         2  0.0  0.0      0     0 ?        S    2月11   0:00 [kthreadd]
root         3  0.0  0.0      0     0 ?        S    2月11   0:01 [ksoftirqd/0]
root         5  0.0  0.0      0     0 ?        S<   2月11   0:00 [kworker/0:0H]
root         7  0.0  0.0      0     0 ?        S    2月11   0:00 [migration/0]
root         8  0.0  0.0      0     0 ?        S    2月11   0:00 [rcu_bh]
[root@localhost ~]# ps -A
  PID TTY          TIME CMD
    1 ?        00:00:45 systemd
    2 ?        00:00:00 kthreadd
    3 ?        00:00:01 ksoftirqd/0
    5 ?        00:00:00 kworker/0:0H
    7 ?        00:00:00 migration/0
```

- 查找特定进程信息

```javascript
[root@localhost ~]# ps -ef | grep ssh
root      1784     1  0 2月11 ?       00:00:00 /usr/sbin/sshd
root     23126  1784  0 08:19 ?        00:00:00 sshd: root@pts/0
root     23325  1784  0 08:26 ?        00:00:00 sshd: root@pts/2
root     25351  1784  0 11:10 ?        00:00:00 sshd: root@pts/1
root     25413  1784  0 11:12 ?        00:00:00 sshd: root@pts/3
root     25728 25357  0 11:34 pts/1    00:00:00 grep --color=auto ssh
```

[root@localhost ~]

\#

- 显示指定用户信息

```javascript
[root@localhost ~]#  ps -u root
  PID TTY          TIME CMD
    1 ?        00:00:45 systemd
    2 ?        00:00:00 kthreadd
    3 ?        00:00:01 ksoftirqd/0
    5 ?        00:00:00 kworker/0:0H
    7 ?        00:00:00 migration/0
```

- 按 CPU 资源的使用量对进程进行排序

```javascript
[root@localhost ~]# ps aux | sort -nk 3
avahi      767  0.0  0.1  57752  2256 ?        Ss   2月11   0:02 avahi-daemon: running [linux.local]
avahi      790  0.0  0.0  57624   384 ?        S    2月11   0:00 avahi-daemon: chroot helper
```

- 按内存资源的使用量对进程进行排序

```javascript
[root@localhost ~]# ps aux | sort -rnk 4 
gdm       3348  0.0  6.1 1509084 115020 ?      Sl   2月11  10:54 gnome-shell --mode=gdm
```

#### 15.7 crontab

功能：定时任务命令

语法：

```javascript
crontab [参数]
```

参数：

```javascript
-e	编辑该用户的计时器设置
-l	列出该用户的计时器设置
-r	删除该用户的计时器设置
-u	指定要设定计时器的用户名称
--help	显示帮助信息
```

实例：

- 创建、编辑计划任务

```javascript
[root@localhost ~]# crontab -e  
no crontab for root - using an empty one
crontab: no changes made to crontab
```

- 查看当前计划任务

```javascript
[root@192 ~]# crontab -l  
*/2 * * * * /usr/sbin/ntpdate 58.220.207.226 &> /dev/null  
00 02 * * * sh auto_backup.sh &> /dev/null  
```

- 删除某条计划任务

```javascript
[root@localhost ~]# crontab -r 
```

#### 15.8 nohup

功能：后端运行程序

nohup命令的全称为“no hang up”，该命令可以将程序以忽略挂起信号的方式运行起来，被运行的程序的输出信息将不会显示到终端。

无论是否将 nohup 命令的输出重定向到终端，输出都将附加到当前目录的 nohup.out 文件中。如果当前目录的 nohup.out 文件不可写，输出重定向到$HOME/nohup.out文件中。如果没有文件能创建或打开以用于追加，那么 command 参数指定的命令不可调用。如果标准错误是一个终端，那么把指定的命令写给标准错误的所有输出作为标准输出重定向到相同的文件描述符。

语法：

```javascript
nohup [参数]
```

参数：

```javascript
--help	在线帮助
--version	显示版本信息
```

实例：

- 使用nohup命令在后台运行程序

```javascript
[root@localhost ~]# nohup bash test.sh&
```