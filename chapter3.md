# 03- Shell脚本学习--字符串和数组
标签： Shell

---
[TOC]

## 字符串

字符串是shell编程中最常用最有用的数据类型（除了数字和字符串，也没啥其它类型好用了），字符串可以用单引号，也可以用双引号，也可以不用引号。单双引号的区别跟PHP类似：

单双引号的区别：

* 双引号里可以有变量，单引号则原样输出；
* 双引号里可以出现转义字符，单引号则原样输出；
* 单引号字串中不能出现单引号。

### 拼接字符串

```bash
#!/bin/bash

str1='i'
str2='love'
str3='you'

echo $str1 $str2 $str3
echo $str1$str2$str3
echo $str1,$str2,$str3
```

输出：
```
i love you
iloveyou
i,love,you
```

### 获取字符串长度
```bash
#!/bin/bash/

str='i love you'

echo ${#str}

# 输出：10
```

### 截取字符串
```bash
#!/bin/bash/

str='i love you'

echo ${str:1} # 从第1个截取到末尾。注意从0开始。
echo ${str:2:2} # 从第2个截取2个。
echo ${str:0} # 全部截取。
echo ${str:-3} # 负数无效，视为0。
```
输出：
```
love you
lo
i love you
i love you
```

### 查找字符串

```bash
#!/bin/bash/

str="i love you"

echo `expr index "$str" l`
echo `expr index "$str" you` #最后一个参数是字符，会对后面字符串每一个单独查找，返回最靠前的index
echo `expr index "$str" o`
echo `expr length "$str"` #字符串长度
echo `expr substr "$str" 1 6` #从字符串中位置1开始截取6个字符。索引是从0开始的。
```

输出:
```
3
4
4
10
i love
```

注意字符串变量需要加双引号。第2个例子里`you`虽然`y`的index是8,但是`o`在前面已经出现过,index是4，最终取所有字符里最靠前的index。

*拓展：`expr`更多关于字符串用法：
```bash
STRING : REGEXP   anchored pattern match of REGEXP in STRING

match STRING REGEXP        same as STRING : REGEXP

substr STRING POS LENGTH   #从STRING中POS位置开始截取LENGTH个字符。POS索引是从1开始的。

index STRING CHARS         #在STRING中查找字符CHARS首次出现的位置，没有找到返回0

length STRING              #字符串长度
```

## 数组
bash支持一维数组（不支持多维数组），并且没有限定数组的大小。类似与C语言，数组元素的下标由0开始编号。获取数组中的元素要利用下标，下标可以是整数或算术表达式，其值应大于或等于0。

在Shell中，用括号来表示数组，数组元素用`空格`符号分割开。定义数组的一般形式为：
```bash
array_name=(value1 value2 ... valuen)
```

例如：
```bash
array_name=(value0 value1 value2 value3)
```
或者
```bash
array_name=(
    value0
    value1
    value2
    value3
)
```

还可以单独定义数组的各个分量：
```bash
array_name[0]=value0
array_name[1]=value1
array_name[2]=value2
```
可以不使用连续的下标，而且下标的范围没有限制。

下面来读取数组：
```bash
echo ${array_name[2]} #读取下标为2的元素
echo ${array_name[*]} #读取所有元素
echo ${array_name[@]} #读取所有元素


echo ${#array_name[*]} #获取数组长度
echo ${#array_name[@]} #获取数组长度
echo ${#array_name[1]} #获取数组中单个元素的长度
```

输出：
```bash
value2
value0 value1 value2 value3
value0 value1 value2 value3
4
4
6
```


## 总结
对比shell里字符串和数组，我们发现：

字符串
```bash
str="hello"
${#str} # 读取字符串长度
echo ${str} # 读取字符串全部
echo ${str:1} # 截取字符串
```

数组：
```bash
arr=(a1 a2 a3)
echo ${#arr[*]} # 读取数组长度
echo ${#arr[1]} # 读取数组某个元素长度

echo ${arr[*]} # 读取数组全部
echo ${arr[1]} # 读取数组某个元素
```

`${#ele*}`用来读取ele元素长度属性
`${ele*}`用来读取或操作ele元素





