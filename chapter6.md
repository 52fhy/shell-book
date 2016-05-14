# 06- Shell脚本学习--其它
标签： Shell

---
[TOC]

## Shell输入输出重定向

Unix 命令默认从标准输入设备(stdin)获取输入，将结果输出到标准输出设备(stdout)显示。一般情况下，标准输入设备就是键盘，标准输出设备就是终端，即显示器。
输出重定向

命令的输出不仅可以是显示器，还可以很容易的转移向到文件，这被称为输出重定向。

命令输出重定向的语法为：
```
command > file
```
这样，输出到显示器的内容就可以被重定向到文件。

例如，下面的命令在显示器上不会看到任何输出：
```
who > users
```
打开 users 文件，可以看到下面的内容：
```
cat users

oko         tty01   Sep 12 07:30
ai          tty15   Sep 12 13:32
ruth        tty21   Sep 12 10:10
pat         tty24   Sep 12 13:07
steve       tty25   Sep 12 13:03
```
输出重定向会覆盖文件内容，请看下面的例子：
```
echo line 1 > users

cat users
line 1
```
如果不希望文件内容被覆盖，可以使用 `>>` 追加到文件末尾，例如：
```
echo line 2 >> users

cat users
line 1
line 2
```
输入重定向

和输出重定向一样，Unix 命令也可以从文件获取输入，语法为：
```
command < file
```
这样，本来需要从键盘获取输入的命令会转移到文件读取内容。

注意：输出重定向是大于号(>)，输入重定向是小于号(<)。

例如，计算 users 文件中的行数，可以使用下面的命令：
```
wc -l users
2 users
```
也可以将输入重定向到 users 文件：
```
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
```
command 2 > file
```
如果希望 stderr 追加到 file 文件末尾，可以这样写：
```
command 2 >> file
```
2 表示标准错误文件(stderr)。

如果希望将 stdout 和 stderr 合并后重定向到 file，可以这样写：
```
command > file 2>&1
```
如果希望对 stdin 和 stdout 都重定向，可以这样写：
```
command < file1 >file2
```
command 命令将 stdin 重定向到 file1，将 stdout 重定向到 file2。 

**全部可用的重定向命令列表：**
```
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
```
command << delimiter
    document
delimiter
```
它的作用是将两个 delimiter 之间的内容(document) 作为输入传递给 command。

注意：
**结尾的delimiter 一定要顶格写**，前面不能有任何字符，后面也不能有任何字符，包括空格和 tab 缩进。

开始的delimiter前后的空格会被忽略掉。

下面的例子，通过 `wc -l` 命令计算 document 的行数：
```
wc -l << EOF
    This is a simple lookup program
    for good (and bad) restaurants
    in Cape Town.
EOF
```
输出： 3

也可以 将 Here Document 用在脚本中，例如：
```
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
```
command > /dev/null
```
`/dev/null` 是一个特殊的文件，写入到它的内容都会被丢弃；如果尝试从该文件读取内容，那么什么也读不到。但是 `/dev/null` 文件非常有用，将命令的输出重定向到它，会起到`禁止输出`的效果。

如果希望屏蔽 stdout 和 stderr，可以这样写：
```
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
```
. ./sub.sh
echo $name
```

运行结果：
```
yjc
```

