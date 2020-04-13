# 06- Shell脚本学习--其它
标签： Shell

---
[TOC]

## Shell输入输出重定向

Unix 命令默认从标准输入设备(stdin)获取输入，将结果输出到标准输出设备(stdout)显示。一般情况下，标准输入设备就是键盘，标准输出设备就是终端，即显示器。
输出重定向

命令的输出不仅可以是显示器，还可以很容易的转移向到文件，这被称为输出重定向。

命令输出重定向的语法为：
```bash
command > file
```
这样，输出到显示器的内容就可以被重定向到文件。

例如，下面的命令在显示器上不会看到任何输出：
```bash
who > users
```
打开 users 文件，可以看到下面的内容：
```bash
cat users

oko         tty01   Sep 12 07:30
ai          tty15   Sep 12 13:32
ruth        tty21   Sep 12 10:10
pat         tty24   Sep 12 13:07
steve       tty25   Sep 12 13:03
```
输出重定向会覆盖文件内容，请看下面的例子：
```bash
echo line 1 > users

cat users
line 1
```
如果不希望文件内容被覆盖，可以使用 `>>` 追加到文件末尾，例如：
```bash
echo line 2 >> users

cat users
line 1
line 2
```
输入重定向

和输出重定向一样，Unix 命令也可以从文件获取输入，语法为：
```bash
command < file
```
这样，本来需要从键盘获取输入的命令会转移到文件读取内容。

注意：输出重定向是大于号(>)，输入重定向是小于号(<)。

例如，计算 users 文件中的行数，可以使用下面的命令：
```bash
wc -l users
2 users
```
也可以将输入重定向到 users 文件：
```bash
wc -l < users
2
```
注意：上面两个例子的结果不同：第一个例子，会输出文件名；第二个不会，因为它仅仅知道从标准输入读取内容。

## 重定向深入讲解

一般情况下，每个 Unix/Linux 命令运行时都会打开三个文件：

* **标准输入文件(stdin)**：stdin的文件描述符为0，Unix程序默认从stdin读取数据。
* **标准输出文件(stdout)**：stdout 的文件描述符为1，Unix程序默认向stdout输出数据。
* **标准错误文件(stderr)**：stderr的文件描述符为2，Unix程序会向stderr流中写入错误信息。

默认情况下，`command > file` 将 stdout 重定向到 file，`command < file` 将stdin 重定向到 file。

如果希望 stderr 重定向到 file，可以这样写：
```bash
command 2 > file
```
如果希望 stderr 追加到 file 文件末尾，可以这样写：
```bash
command 2 >> file
```
2 表示标准错误文件(stderr)。

如果希望将 stdout 和 stderr 合并后重定向到 file，可以这样写：
```bash
command > file 2>&1
```
如果希望对 stdin 和 stdout 都重定向，可以这样写：
```bash
command < file1 >file2
```
command 命令将 stdin 重定向到 file1，将 stdout 重定向到 file2。 

**全部可用的重定向命令列表：**
```bash
命令	说明
command > file	将输出重定向到 file。
command < file	将输入重定向到 file。
command >> file	将输出以追加的方式重定向到 file。
n > file	将文件描述符为 n 的文件重定向到 file。
n >> file	将文件描述符为 n 的文件以追加的方式重定向到 file。
n >& m	将输出文件 m 和 n 合并。
n <& m	将输入文件 m 和 n 合并。
<< tag	将开始标记 tag 和结束标记 tag 之间的内容作为输入。
```


## Here Document

Here Document 目前没有统一的翻译，这里暂译为`嵌入文档`。Here Document 是 Shell 中的一种特殊的重定向方式，它的基本的形式如下：
```bash
command << delimiter
    document
delimiter
```
它的作用是将两个 delimiter 之间的内容(document) 作为输入传递给 command。

注意：
**结尾的delimiter 一定要顶格写**，前面不能有任何字符，后面也不能有任何字符，包括空格和 tab 缩进。

开始的delimiter前后的空格会被忽略掉。

下面的例子，通过 `wc -l` 命令计算 document 的行数：
```bash
wc -l << EOF
    This is a simple lookup program
    for good (and bad) restaurants
    in Cape Town.
EOF
```
输出： 3

也可以 将 Here Document 用在脚本中，例如：
```bash
#!/bin/bash
cat << EOF
This is a simple lookup program
for good (and bad) restaurants
in Cape Town.
EOF
```
运行结果：
```
This is a simple lookup program
for good (and bad) restaurants
in Cape Town.
```

## /dev/null 文件

如果希望执行某个命令，但又不希望在屏幕上显示输出结果，那么可以将输出重定向到 `/dev/null`：
```bash
command > /dev/null
```
`/dev/null` 是一个特殊的文件，写入到它的内容都会被丢弃；如果尝试从该文件读取内容，那么什么也读不到。但是 `/dev/null` 文件非常有用，将命令的输出重定向到它，会起到`禁止输出`的效果。

如果希望屏蔽 stdout 和 stderr，可以这样写：
```bash
command > /dev/null 2>&1
```
这样不会在屏幕打印任何信息。


## Shell文件包含
像其他语言一样，Shell 也可以包含外部脚本，将外部脚本的内容合并到当前脚本。

Shell 中包含脚本可以使用 `. filename` 或 `source filename` 。

两种方式的效果相同，简单起见，一般使用点号(.)，但是注意点号(.)和文件名中间有一空格。

示例：
被包含文件：sub.sh
```
name="yjc"
```

主文件：test.sh
```bash
. ./sub.sh
echo $name
```

运行结果：
```
yjc
```

## 获取当前正在执行脚本的绝对路径

正确的命令是：
``` bash
basepath=$(cd `dirname $0`; pwd)
```

直接使用`pwd`或者`dirname $0`是不对的。

## 按特定字符串截取字符串

示例：截取`/www/html/php/myapp/`里的myapp。

方案：
```bash
str=/www/html/php/myapp/
b=($(echo $str|sed 's#/# #g'))
b_len=`expr ${#b[*]} - 1`
app_name=${b[$b_len]}
echo $app_name
```
这里利用`sed`将字符串按指定字符截成数组，然后取最后一个。  

计算数组长度：`${#arr[*]}`  
计算则需要使用`expr`命令

## awk

### awk简介


awk是一个强大的文本分析工具，相对于grep的查找，sed的编辑，awk在其对数据分析并生成报告时，显得尤为强大。简单来说awk就是把文件(或其他方式的输入流, 如重定向输入)逐行的读入（看作一个记录集）, 把每一行看作一条记录，以空格(或\t,或用户自己指定的分隔符)为默认分隔符将每行切片（类似字段），切开的部分再进行各种分析处理。

awk有3个不同版本: awk、nawk和gawk，未作特别说明，一般指gawk，gawk 是 AWK 的 GNU 版本。

Awk基本语法:　

``` shell
awk 'pattern1 {command1;command 2…; command 3}  pattern2 { command …}'
```

pattern表示用来过滤记录的模式,可是是正则表达式，关系运算表达式，也可以什么也没有(表示选中所有记录)。

每个pattern选中的行记录会被花括号括起来的命令command操作一遍, command之间用`;`分割。 花括号里面可以什么也没有, 则默认为print输出整行记录。 Comamnd可以是输出， 可以是算术运算，逻辑运算，循环控制等等。

### 示例

s.txt
```
zhangsan 1977 male computer 83
lisi 1989 male math 99
wanglijiang 1990 female chinese 78
xuliang 1977 male economic 89
xuxin 1986 female english 99
wangxuebing 1978 male math 89
lichang 1989 male math 99
wanglijiang 1990 female chinese 78
zhangsansan 1977 male computer 83 
langxuebing 1978 male math 89
lisibao 1989 male math 99
xiaobao 1990 female chinese 78
```
一行中的5个字段分别表示`姓名, 出生年, 性别,科目,分数`, 是一个很传统很典型的报表文件。

现在演示awk是如何查找的：

1)直接输出1990年出生的同学:
``` shell
$ awk '/1990/' s.txt

wanglijiang 1990 female chinese 78
wanglijiang 1990 female chinese 78
xiaobao 1990 female chinese 78
 
```
或者：
```
$ awk '/1990/{print $0}' s.txt
```
awk默认把输入的内容以空格拆分出每列。`$0`表示匹配所有列，`print $0`将输出所有列，每列分隔符是空格。

2）对chinese的课程的行输出"语文"：
``` shell
$ awk '/chinese/{print "语文"}' s.txt

语文
语文
语文

```

3）记录的头部和结尾加上一段说明：
``` shell
$ awk 'BEGIN{print "Result of the quiz:\n"}{print $0}END{print "------"}' s.txt
Result of the quiz:

zhangsan 1977 male computer 83
lisi 1989 male math 99
wanglijiang 1990 female chinese 78
xuliang 1977 male economic 89
xuxin 1986 female english 99
wangxuebing 1978 male math 89
lichang 1989 male math 99
wanglijiang 1990 female chinese 78
zhangsansan 1977 male computer 83
langxuebing 1978 male math 89
lisibao 1989 male math 99
xiaobao 1990 female chinese 78
------

```

AWK工作流程：**逐行扫描文件，从第一行到最后一行，寻找匹配特定模式的行，并在这些行上进行用户想要到的操作**。

BEGIN只会在最开始执行；END只会在扫描所有行数之后执行。BEGIN和END之间的花括号的内容每扫描一行都会执行。

4)查找女生的成绩且只输出姓名、学科、成绩：
``` shell
$ awk '$3=="female"{print $1,$4,$5}' s.txt
wanglijiang chinese 78
xuxin english 99
wanglijiang chinese 78
xiaobao chinese 78

```
`$1`表示第1列，`$n`类推。这里条件是表达式，而不是正则。print里`,`表示空格分隔符。

5)找出1990年出生的学生姓名，并要求匹配正则:
``` shell
$ awk '$2~/1990/{print $1}' s.txt
wanglijiang
wanglijiang
xiaobao

```

这里`~`表示匹配正则表达式。`!~`表示不匹配正则表达式。

如果需要多选，则改成：
``` shell
$ awk '$2~/(1990|1991)/{print $1}' s.txt
```

awk更多内容详见：https://www.cnblogs.com/52fhy/p/5836429.html#autoid-3-4-0

（完结）

