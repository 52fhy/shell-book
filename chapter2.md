# 02- Shell脚本学习--运算符
标签： Shell

---
[TOC]

## Shell运算符

Bash 支持很多运算符，包括算数运算符、关系运算符、布尔运算符、字符串运算符和文件测试运算符。

### 算术运算符
原生bash不支持简单的数学运算，但是可以通过其他命令来实现，例如 awk 和 expr，expr 最常用。

`expr` 是一款表达式计算工具，使用它能完成表达式的求值操作。
```bash
# 命令行直接计算
expr 2 + 2   #4
expr 3 - 2   #1
expr 3 / 2   #1
expr 3 \* 2   #6

# 使用表达式
a=10
b=20
val=`expr $a + $b`
echo "a + b : $val"
```

注意：

* **表达式和运算符之间要有空格**，例如 `2+2` 是不对的，必须写成 `2 + 2`，这与我们熟悉的大多数编程语言不一样。
* 乘号(*)前边必须加反斜杠(\\)才能实现乘法运算
* 完整的表达式要被 \` ` 包含，注意这个字符不是常用的单引号，在 Esc 键下边。


**算术运算符列表**
```bash
运算符	说明	    举例
+	    加法	`expr $a + $b` 结果为 30。
-   	减法	`expr $a - $b` 结果为 10。
*	    乘法	`expr $a \* $b` 结果为  200。
/	    除法	`expr $b / $a` 结果为 2。
%	    取余	`expr $b % $a` 结果为 0。
=	    赋值	a=$b 将把变量 b 的值赋给 a。
==	    相等。用于比较两个数字，相同则返回 true。	[ $a == $b ] 返回 false。
!=	    不相等。用于比较两个数字，不相同则返回 true。	[ $a != $b ] 返回 true。
```

### 关系运算符
关系运算符只支持数字，不支持字符串，除非字符串的值是数字。

```bash
#!/bin/sh
a=10
b=20
if [ $a -eq $b ]
then
   echo "$a -eq $b : a is equal to b"
else
   echo "$a -eq $b: a is not equal to b"
fi
```

缩成一行可以这样：
```bash
a=10;b=20;if [ $a -eq $b ];then echo "$a -eq $b : a is equal to b"; else echo "$a -eq $b: a is not equal to b"; fi
```
这里缩写，主要是为了让大家注意：

* if后面直到then前面的分号结束，都是有空格的: `if [ $a -eq $b ]`

**关系运算符列表**
```bash
运算符	说明
-eq	检测两个数是否相等，相等返回 true。同算数运算符`==`
-ne	检测两个数是否相等，不相等返回 true
-gt	检测左边的数是否大于右边的，如果是，则返回 true。
-lt	检测左边的数是否小于右边的，如果是，则返回 true。
-ge	检测左边的数是否大等于右边的，如果是，则返回 true。
-le	检测左边的数是否小于等于右边的，如果是，则返回 true。

```

## 布尔运算符
**布尔运算符列表**
```bash
运算符	说明
!	非运算，表达式为 true 则返回 false，否则返回 true。
-o	或运算(or)，有一个表达式为 true 则返回 true。
-a	与运算(and)，两个表达式都为 true 才返回 true。
```
```bash
if [ 3 -eq 3 -a 3 -lt 5 ]
then
    echo 'ok'
fi;
```

## 字符串运算符

**字符串运算符列表**
```bash
运算符	说明	举例
=	检测两个字符串是否相等，相等返回 true。	[ $a = $b ] 返回 false。
!=	检测两个字符串是否相等，不相等返回 true。	[ $a != $b ] 返回 true。
-z	检测字符串长度是否为0，为0返回 true。	[ -z $a ] 返回 false。
-n	检测字符串长度是否为0，不为0返回 true。	[ -n $a ] 返回 true。
str	检测字符串是否为空，不为空返回 true。	[ $a ] 返回 true。
```

## 文件测试运算符

文件测试运算符用于检测 Unix 文件的各种属性。

```bash
#!/bin/sh
file="/tmp/test.sh"

if [ -e $file ]
then
   echo "File exists"
else
   echo "File does not exist"
fi
```

文件测试运算符列表
```bash
操作符	说明	举例

-b file	检测文件是否是块设备文件，如果是，则返回 true。	[ -b $file ] 返回 false。

-c file	检测文件是否是字符设备文件，如果是，则返回 true。	[ -c $file ] 返回 false。

-d file	检测文件是否是目录，如果是，则返回 true。	[ -d $file ] 返回 false。

-f file	检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。	[ -f $file ] 返回 true。

-g file	检测文件是否设置了 SGID 位，如果是，则返回 true。	[ -g $file ] 返回 false。

-k file	检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。	[ -k $file ] 返回 false。

-p file	检测文件是否是具名管道，如果是，则返回 true。	[ -p $file ] 返回 false。

-u file	检测文件是否设置了 SUID 位，如果是，则返回 true。	[ -u $file ] 返回 false。

-r file	检测文件是否可读，如果是，则返回 true。	[ -r $file ] 返回 true。

-w file	检测文件是否可写，如果是，则返回 true。	[ -w $file ] 返回 true。

-x file	检测文件是否可执行，如果是，则返回 true。	[ -x $file ] 返回 true。

-s file	检测文件是否为空（文件大小是否大于0），不为空返回 true。	[ -s $file ] 返回 true。

-e file	检测文件（包括目录）是否存在，如果是，则返回 true。	[ -e $file ] 返回 true。
```
