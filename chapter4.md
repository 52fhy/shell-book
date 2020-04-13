# 04- Shell脚本学习--条件控制
标签： Shell

---
[TOC]

## 条件判断：if语句
语法格式：
```bash
if [ expression ]
then
   Statement(s) to be executed if expression is true
fi
```

注意：`expression` 和方括号([ ])之间必须有空格，否则会有语法错误。

if 语句通过关系运算符判断表达式的真假来决定执行哪个分支。Shell 有三种 if ... else 语句：
```bash
if ... fi 语句
if ... else ... fi 语句
if ... elif ... else ... fi 语句
```

示例：
```bash
#!/bin/bash/

a=10
b=20
if [ $a == $b ]
then 
	echo "a is equal to b"
elif [ $a -gt $b ]
then
	echo "a is greater to b"
else
	echo "a is less to b"
fi
```

`if ... else` 语句也可以写成一行，以命令的方式来运行:
```bash
a=10;b=20;if [ $a == $b ];then echo "a is equal to b";else echo "a is not equal to b";fi;
```

`if ... else` 语句也经常与 `test` 命令结合使用，作用与上面一样：
```bash
#!/bin/bash/

a=10
b=20
if test $a == $b 
then 
	echo "a is equal to b"
else
	echo "a is not equal to b"
fi
```

## 分支控制：case语句
`case ... esac` 与其他语言中的 `switch ... case` 语句类似，是一种多分枝选择结构。

示例：
```bash
#!/bin/bash/

grade="B"

case $grade in 
	"A") echo "Very Good!";;
	"B") echo "Good!";;
	"C") echo "Come On!";;
	*) 
		echo "You Must Try!"
		echo "Sorry!";;
esac
```

转换成C语言是：
``` c
#include <stdio.h>
int main(){
    char grade = 'B';
    switch(grade){
        case 'A': printf("Very Good!");break;
        case 'B': printf("Very Good!");break;
        case 'C': printf("Very Good!");break;
        default: 
            printf("You Must Try!");
            printf("Sorry!");
            break;
    }
    return 0;
}

```

对比看就很容易理解了。很相似，只是格式不一样。

需要注意的是：
**取值后面必须为关键字 in，每一模式必须以右括号结束。取值可以为变量或常数。匹配发现取值符合某一模式后，其间所有命令开始执行直至 `;;`。**`;;` 与其他语言中的 `break` 类似，意思是跳到整个 `case` 语句的最后。

取值将检测匹配的每一个模式。一旦模式匹配，则执行完匹配模式相应命令后不再继续其他模式。如果无一匹配模式，使用星号 `*` 捕获该值，再执行后面的命令。

再举一个例子：
```bash
#!/bin/bash
option="${1}"
case ${option} in
   "-f") FILE="${2}"
      echo "File name is $FILE"
      ;;
   "-d") DIR="${2}"
      echo "Dir name is $DIR"
      ;;
   *) 
      echo "`basename ${0}`:usage: [-f file] | [-d directory]"
      exit 1 # Command to come out of the program with status 1
      ;;
esac
```
运行结果：
```bash
$./test.sh
test.sh: usage: [ -f filename ] | [ -d directory ]

./test.sh -f index.html
File name is index.html
```

这里用到了特殊变量`${1}`,指的是获取命令行的第一个参数。


下面结合`getopts`命令介绍下一个经典的例子：从命令行读取参数。

run.sh  
``` bash
#!/bin/sh

usage()
{
    echo "Usage: $0 -s [start|stop|reload|restart] -e [online|test]"
    exit 1
}

if [ -z $1 ]; then
    usage
fi

while getopts 's:e:h' OPT; do
    case $OPT in
        s) cmd="$OPTARG";;
        e) env="$OPTARG";;
        h) usage;;
        ?) usage;;
    esac
done

echo $cmd
echo $env
```
当我们直接运行`run.sh`的时候，会调用`usage`显示帮助；如果输入正确的参数，则会进入正确的流程。运行示例：
```bash
sh run.sh -s start -e test
```

## for循环
shell的for循环与c、php等语言不同，同Python很类似。下面是语法格式：
```bash
for 变量 in 列表
do
    command1
    command2
    ...
    commandN
done
```

示例：
```bash
#!/bin/bash/

for value in 1 2 3 4 5
do 
	echo "The value is $value"
done
```

输出：
```bash
The value is 1
The value is 2
The value is 3
The value is 4
The value is 5
```

顺序输出字符串中的字符：
```bash
for str in 'This is a string'
do
    echo $str
done
```

运行结果：
```
This is a string
```

遍历目录下的文件：
```bash
#!/bin/bash
for FILE in *
do
   echo $FILE
done
```

上面的代码将遍历当前目录下所有的文件。在Linux下，可以改为其他目录试试。

遍历文件内容：
city.txt
```
beijing
tianjin
shanghai
```

```bash
#!/bin/bash

citys=`cat city.txt`
for city in $citys
do
   echo $city
done
```

输出：
```
beijing
tianjin
shanghai
```


## while循环

只要while后面的条件满足，就一直执行do里面的代码块。

其格式为：
```bash
while command
do
   Statement(s) to be executed if command is true
done
```
命令执行完毕，控制返回循环顶部，从头开始直至测试条件为假。

示例：
```bash
#!/bin/bash

c=0;
while [ $c -lt 3 ]
do
	echo "Value c is $c"
	c=`expr $c + 1`
done
```

输出：
```
Value c is 0
Value c is 1
Value c is 2
```

这里由于shell本身不支持算数运算，所以使用`expr`命令进行自增。

## until循环

until 循环执行一系列命令直至条件为 true 时停止。until 循环与 while 循环在处理方式上刚好相反。一般while循环优于until循环，但在某些时候，也只是极少数情况下，until 循环更加有用。

将上面while循环的例子改改，就能达到一样的效果：
```bash
#!/bin/bash

c=0;
until [ $c -eq 3 ]
do
	echo "Value c is $c"
	c=`expr $c + 1`
done
```
首先do里面的语句块一直在运行，直到满足了until的条件就停止。

输出：
```
Value c is 0
Value c is 1
Value c is 2
```

## 跳出循环

在循环过程中，有时候需要在未达到循环结束条件时强制跳出循环，像大多数编程语言一样，Shell也使用 break 和 continue 来跳出循环。

### break

break命令允许跳出所有循环（终止执行后面的所有循环）。

```bash
#!/bin/bash

i=0
while [ $i -lt 5 ]
do
	i=`expr $i + 1`

	if [ $i == 3 ]
		then
			break
	fi
	echo -e $i
done
```

运行结果：
```
1
2
```

在嵌套循环中，break 命令后面还可以跟一个整数，表示跳出第几层循环。例如：
```bash
break n
```
表示跳出第 n 层循环。

## continue

continue命令与break命令类似，只有一点差别，它不会跳出所有循环，仅仅跳出当前循环。

```bash
#!/bin/bash

i=0
while [ $i -lt 5 ]
do
	i=`expr $i + 1`

	if [ $i == 3 ]
		then
			continue
	fi
	echo -e $i
	
done
```

运行结果：
```
1
2
4
5
```
