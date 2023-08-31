| mkdir  | 创建一个目录         | mkdir dirname    |
| ------ | -------------------- | ---------------- |
| rmdir  | 删除一个目录         | rmdir dirname    |
| mvdir  | 移动或重命名一个目录 | mvdir dir1 dir2  |
| cd     | 改变当前目录         | cd dirname       |
| pwd    | 显示当前目录的路径名 | pwd              |
| ls     | 显示当前目录的内容   | ls -la           |
| dircmp | 比较两个目录的内容   | dircmp dir1 dir2 |

| cat  | 显示或连接文件         | cat filename                          |
| ---- | ---------------------- | ------------------------------------- |
| pg   | 分页格式化显示文件内容 | pg filename                           |
| more | 分屏显示文件内容       | more filename                         |
| od   | 显示非文本文件的内容   | od -c filename                        |
| cp   | 复制文件或目录         | cp file1 file2                        |
| rm   | 删除文件或目录         | rm filename                           |
| mv   | 改变文件名或所在目录   | mv file1 file2                        |
| ln   | 联接文件               | ln -s file1 file2                     |
| find | 使用匹配表达式查找文件 | find . -name “*.c” -print             |
| file | 显示文件类型           | file filename                         |
| open | 使用默认的程序打开文件 | open filename （open . 打开当前目录） |

| head  | 显示文件的最初几行             | head -20 filename            |
| ----- | ------------------------------ | ---------------------------- |
| tail  | 显示文件的最后几行             | tail -15 filename            |
| cut   | 显示文件每行中的某些域         | cut -f1,7 -d: /etc/passwd    |
| colrm | 从标准输入中删除若干列         | colrm 8 20 file2             |
| paste | 横向连接文件                   | paste file1 file2            |
| diff  | 比较并显示两个文件的差异       | diff file1 file2             |
| sed   | 非交互方式流编辑器             | sed “s/red/green/g” filename |
| grep  | 在文件中按模式查找             | grep “^[a-zA-Z]” filename    |
| awk   | 在文件中查找并处理模式         | awk ‘{print 111}’ filename   |
| sort  | 排序或归并文件                 | sort -d -f -u file1          |
| uniq  | 去掉文件中的重复行             | uniq file1 file2             |
| comm  | 显示两有序文件的公共和非公共行 | comm file1 file2             |
| wc    | 统计文件的字符数、词数和行数   | wc filename                  |
| nl    | 给文件加上行号                 | nl file1 >file2              |

mac 快捷键

command + shift + 。 显示隐藏文件夹