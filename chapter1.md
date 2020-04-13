# 01- Shell脚本学习--入门
标签： Shell

---
[TOC]
## 简介
>Shell是一种脚本语言，那么，就必须有解释器来执行这些脚本。

Unix/Linux上常见的Shell脚本解释器有bash、sh、csh、ksh等，习惯上把它们称作一种Shell。我们常说有多少种Shell，其实说的是Shell脚本解释器。

## Hello World
打开文本编辑器，新建一个文件`test.sh`，扩展名为`.sh`（sh代表shell）。

输入一些代码：
```bash
#!/bin/bash
echo "Hello World !"
```

在命令行运行：
```bash
chmod +x test.sh
./test.sh
```

运行结果：
```bash
Hello World !
```

`#!` 是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行，即**使用哪一种Shell**。`echo`命令用于向窗口输出文本。

## 注释
以`#`开头的行就是注释，会被解释器忽略。sh里没有多行注释，只能每一行加一个#号。
```bash
# --------
# 这是注释块
# --------
```

## 打印输出

**echo**： 是Shell的一个内部指令，用于在屏幕上打印出指定的字符串。
```bash
echo arg 
echo -e arg #执行arg里的转义字符。echo加了-e默认会换行
echo arg > myfile #显示结果重定向至文件，会生成myfile文件
```
注意，echo后单引号和双引号作用是不同的。单引号不能转义里面的字符。双引号可有可无，单引号主要用在原样输出中。

**printf**：格式化输出语句。
`printf` 命令用于格式化输出， 是`echo`命令的增强版。它是C语言`printf()`库函数的一个有限的变形，并且在语法上有些不同。

如同 `echo` 命令，`printf` 命令也可以输出简单的字符串:
```bash
printf "hello\n"
```
`printf` 不像 `echo` 那样会自动换行，必须显式添加换行符(\n)。

注意：`printf` 由 POSIX 标准所定义，移植性要比 `echo` 好。


printf 命令的语法：
```bash
printf  format-string  [arguments...]

#format-string 为格式控制字符串，arguments 为参数列表。功能和用法与c语言的 printf 命令类似。
```

这里仅说明与C语言printf()函数的不同：

* printf 命令不用加括号
* format-string 可以没有引号，但最好加上，单引号双引号均可。
* 参数比格式控制符(%)多时，格式控制符可以重用，可以将所有参数都转换。
* arguments 使用空格分隔，不用逗号。

```bash
# 双引号
printf "%d %s\n" 10 "abc"
10 abc
# 单引号与双引号效果一样 
printf '%d %s\n' 10 "abc" 
10 abc

# 没有引号也可以输出
printf %s abc
abc

# 但是下面的会出错：
printf %d %s 10 abc 
#因为系统分不清楚哪个是参数，这时候最好加引号了。


# 格式只指定了一个参数，但多出的参数仍然会按照该格式输出，format-string 被重用
$ printf %s a b c
abc
$ printf "%s\n" a b c
a
b
c

# 如果没有 arguments，那么 %s 用NULL代替，%d 用 0 代替
$ printf "%s and %d \n" 
and 0

# 如果以 %d 的格式来显示字符串，那么会有警告，提示无效的数字，此时默认置为 0
$ printf "The first program always prints'%s,%d\n'" Hello Shell
-bash: printf: Shell: invalid number
The first program always prints 'Hello,0'
$
```

**read**： 命令行从输入设备读入内容
```bash
#!/bin/bash

# Author : lalal

echo "What is your name?"
read NAME #输入
echo "Hello, $NAME"
```

运行脚本：
```bash
chmod +x test.sh
./test.sh

What is your name?
lalal

Hello, lalal
```


## 变量定义
Shell支持自定义变量。
### 定义变量
定义变量时，变量名不加美元符号（$），如：
```bash
variableName="value"
```
注意，**变量名和等号之间不能有空格**，这可能和你熟悉的所有编程语言都不一样。有空格会出错。

同时，变量名的命名须遵循如下规则：
>* 首个字符必须为字母（a-z，A-Z）。
* 中间不能有空格，可以使用下划线（_）。
* 不能使用标点符号。
* 不能使用bash里的关键字（可用help命令查看保留关键字）。

变量定义举例：
```bash
myUrl="lalal"
myNum=100
```

> 注意：变量中间不能有空格，如果手误写错(例如 `var = test`)，刚好要使用`rm -rf $var/`删除这个目录，实际删除的是`/`！

### 使用变量

使用一个定义过的变量，只要在变量名前面加美元符号（$）即可，如：
```bash
your_name="lalal"
echo $your_name
echo ${your_name}
```
**变量名外面的花括号是可选的**，加不加都行，加花括号是为了帮助解释器识别变量的边界，比如下面这种情况：
```bash
for skill in C PHP Python Java 
do
    echo "I am good at ${skill}Script"
done
```
如果不给skill变量加花括号，写成`echo "I am good at $skillScript"`，解释器就会把`$skillScript`当成一个变量（其值为空），代码执行结果就不是我们期望的样子了。

**推荐给所有变量加上花括号，这是个好的编程习惯。**

>已定义的变量，可以被重新定义。

在变量前面加`readonly` 命令可以将变量定义为只读变量，只读变量的值不能被改变。
```bash
url="http://www.baidu.com"
readonly url
url="http://www.baidu.com"
```

使用 `unset` 命令可以删除变量。语法：
```bash
unset variable_name
```
变量被删除后不能再次使用；unset 命令不能删除只读变量。

### 变量类型
运行shell时，会同时存在三种变量：
**1) 局部变量**
局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。

**2) 环境变量**
所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。

**3) shell变量**
shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行。

## 特殊变量
前面已经讲到，**变量名只能包含数字、字母和下划线**，因为某些包含其他字符的变量有特殊含义，这样的变量被称为**特殊变量**。

|变量	|含义 |
|-----  |----|
|`$0`	|当前脚本的文件名|
|`$n`	|传递给脚本或函数的参数。n 是一个数字，表示第几个参数。例如，第一个参数是`$1`，第二个参数是`$2`。|
|`$#`	|传递给脚本或函数的参数个数。|
|`$*`	|传递给脚本或函数的所有参数。|
|`$@`	|传递给脚本或函数的所有参数。被双引号(" ")包含时，与 `$*` 稍有不同|
|`$?`	|上个命令的退出状态，或函数的返回值。|
|`$$`	|当前Shell进程ID。对于 Shell 脚本，就是这些脚本所在的进程ID。|

示例：
```bash
#!/bin/bash
echo "File Name: $0"
echo "First Parameter : $1"
echo "Second Parameter : $2"
echo "Quoted Values: $@"
echo "Quoted Values: $*"
echo "Total Number of Parameters : $#"
```

运行结果：
```bash
$./test.sh Zara Ali
File Name : ./test.sh
First Parameter : Zara
Second Parameter : Ali
Quoted Values: Zara Ali
Quoted Values: Zara Ali
Total Number of Parameters : 2
```

### `$*` 和 `$@` 的区别
`$*` 和 `$@` 都表示传递给函数或脚本的所有参数，不被双引号(" ")包含时，都以`"$1" "$2" … "$n"` 的形式输出所有参数。

但是当它们被双引号(" ")包含时，"`$*`" 会将所有的参数作为一个整体，以"`$1 $2 … $n`"的形式输出所有参数；"`$@`" 会将各个参数分开，以`"$1" "$2" … "$n"` 的形式输出所有参数。

示例：
```bash
#!/bin/bash
echo "\$*=" $*
echo "\"\$*\"=" "$*"
echo "\$@=" $@
echo "\"\$@\"=" "$@"
echo "print each param from \$*"
for var in $*
do
    echo "$var"
done
echo "print each param from \$@"
for var in $@
do
    echo "$var"
done
echo "print each param from \"\$*\""
for var in "$*"
do
    echo "$var"
done
echo "print each param from \"\$@\""
for var in "$@"
do
    echo "$var"
done
```

执行 `./test.sh "a" "b" "c" "d"`，看到下面的结果：
```bash
$*=  a b c d
"$*"= a b c d
$@=  a b c d
"$@"= a b c d
print each param from $*
a
b
c
d
print each param from $@
a
b
c
d
print each param from "$*"
a b c d
print each param from "$@"
a
b
c
d
```

### 退出状态

`$?` 可以获取上一个命令的退出状态。所谓退出状态，就是上一个命令执行后的返回结果。

示例：
```bash
if [[ $? != 0 ]];then
  echo "error"
  exit 1;
fi
```

退出状态是一个数字，一般情况下，大部分命令执行成功会返回 0，失败返回 1。

不过，也有一些命令返回其他值，表示不同类型的错误。

## 转义字符

```bash
转义字符	含义
\\	反斜杠
\a	警报，响铃
\b	退格（删除键）
\f	换页(FF)，将当前位置移到下页开头
\n	换行
\r	回车
\t	水平制表符（tab键） 
\v	垂直制表符
```

**shell默认是不转义上面的字符的。需要加`-e`选项。**

举个例子：
```bash
#!/bin/bash
a=11
echo -e "a is $a \n"
```

运行结果：
```bash
a is 11
```
这里 `-e` 表示对转义字符进行替换。如果不使用 `-e` 选项，将会原样输出：
```bash
a is 11\n
```

可以使用 echo 命令的 `-E` 选项禁止转义，默认也是不转义的；使用 `-n` 选项可以禁止插入换行符。

## 命令替换

命令替换是指Shell可以先执行命令，将输出结果暂时保存，在适当的地方输出。

语法：
```bash
`command`
```
**注意是反引号，不是单引号，这个键位于 Esc 键下方。**

下面的例子中，将命令执行结果保存在变量中：
```bash
#!/bin/bash
DATE=`date`
echo "Date is $DATE"
```

## 变量替换

变量替换可以根据变量的状态（是否为空、是否定义等）来改变它的值。

可以使用的变量替换形式：

|形式|	说明|
|-----  |----|
|`${var}`|	变量本来的值|
|`${var:-word}`|	如果变量 `var` 为空或已被删除(unset)，那么返回 word，但不改变 `var` 的值。|
|`${var:=word}`|	如果变量 `var` 为空或已被删除(unset)，那么返回 word，并将 `var` 的值设置为 word。|
|`${var:?message}`|	如果变量 `var` 为空或已被删除(unset)，那么将消息 message 送到标准错误输出，可以用来检测变量 `var` 是否可以被正常赋值。若此替换出现在Shell脚本中，那么脚本将停止运行。|
|`${var:+word}`|	如果变量 `var` 被定义，那么返回 word，但不改变 var 的值。|


## 一个完整的shell示例

下面的脚本用于php安装过程中安装zip扩展。

`php_zip_ins.sh`
```bash
#!/bin/bash
#zip install

if [ -d php-5.4.25/ext/zip ];then
	cd php-5.4.25/ext/zip
else
	tar zxvf php-5.4.25.tar.gz
	cd php-5.4.25/ext/zip
fi
/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config
make
[ $? != 0 ] && exit
make install
echo 
grep 'no-debug-zts-20100525' /usr/local/php/etc/php.ini
if [ $? != 0 ];then
        echo '' >> /usr/local/php/etc/php.ini
        echo 'extension_dir=/usr/local/php/lib/php/extensions/no-debug-zts-20100525' >> /usr/local/php/etc/php.ini
fi
grep 'zip.so' /usr/local/php/etc/php.ini
if [ $? != 0 ];then
	echo 'extension=zip.so' >> /usr/local/php/etc/php.ini
fi
echo "zip install is OK"


/usr/local/apache2/bin/apachectl restart
cd -
rm -rf php-5.4.25
echo "all ok!"
ls /usr/local/php/lib/php/extensions/no-debug-zts-20100525/

```
