# 05- Shell脚本学习--函数
标签： Shell

---
[TOC]

函数可以让我们将一个复杂功能划分成若干模块，让程序结构更加清晰，代码重复利用率更高。像其他编程语言一样，Shell 也支持函数。Shell 函数必须先定义后使用。

## 函数定义

Shell 函数的定义格式如下：
```bash
function function_name () {
    list of commands
    [ return value ]
}
```
其中`function`关键字是可选的。

```bash
#!/bin/bash

hello(){
	echo 'hello world';
}

hello
```
运行结果：
```
hello world
```

调用函数只需要给出函数名，不需要加括号。

函数返回值，可以显式增加return语句；如果不加，会将最后一条命令运行结果作为返回值。

Shell 函数返回值只能是整数，一般用来表示函数执行成功与否，0表示成功，其他值表示失败。如果 return 其他数据，比如一个字符串，往往会得到错误提示：`numeric argument required`。

```bash
#!/bin/bash

function hello(){
	return 'hello world';
}

hello
```
运行结果：
```bash
line 4: return: hello: numeric argument required
```

如果一定要让函数返回字符串，那么可以先定义一个变量，用来接收函数的计算结果，脚本在需要的时候访问这个变量来获得函数返回值。
```bash
#!/bin/bash

function hello(){
	echo "hello world";
}

str=$(hello)

echo $str
echo $str
```

运行结果：
```
hello world
hello world
```

像删除变量一样，删除函数也可以使用 `unset` 命令，不过要加上 `.f` 选项，如下所示：
```bash
$unset .f function_name
```
如果你希望直接从终端调用函数，可以将函数定义在主目录下的 .profile 文件，这样每次登录后，在命令提示符后面输入函数名字就可以立即调用。

## 函数参数

在Shell中，调用函数时可以向其传递参数。在函数体内部，通过 `$n` 的形式来获取参数的值，例如，`$1`表示第一个参数，`$2`表示第二个参数...这就是前面讲的特殊变量。

```bash
#!/bin/bash

function sum(){
	case $# in 
		0) echo "no param";;
		1) echo $1;;
		2) echo `expr $1 + $2`;;
		3) echo `expr $1 + $2 + $3`;;
		*) echo "$# params! It's too much!";;
	esac
}

sum 1 3 5 6
```

运行结果：
```
4 params! It's too much!
```


注意，`$10` 不能获取第十个参数，获取第十个参数需要`${10}`。当`n>=10`时，需要使用`${n}`来获取参数。

另外，还有几个特殊变量用来处理参数，前面已经提到：
```bash
特殊变量	说明
$#	传递给函数的参数个数。
$*	显示所有传递给函数的参数。
$@	与$*相同，但是略有区别，请查看Shell特殊变量。
$?	函数的返回值。
```

## 如何获取函数返回值

后面的变量需要使用函数的返回值，怎么实现？  

示例：
``` bash
#!/bin/bash

function sum()
{
	echo `expr 1+2+3`
}

num=$(sum)

```

这样就可以取到返回值了。
