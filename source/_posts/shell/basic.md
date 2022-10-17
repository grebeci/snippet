---
title: shell basic
date: 2022-10-15 22:55:06
tags: SHELL
top: 1
---
# Bash Scripting Basics

### Assignment and Substitution

```bash
a=123
b=$a
```

### Variables

1. Butil-in Variables 

	`$HOME $PWD $PATH` for more info, see [environ(7)](https://link.zhihu.com/?target=https%3A//man7.org/linux/man-pages/man7/environ.7.html)

2. Positional Parameters

	`$0 $1 $2 $3 $4 $@ $* $#`

3. Special Parameters
	$? # exit status of a command, function, or the script itself

	$$ # 当前进程的pid

	$! # 后台运行的最后一个进程pid

4.env

​	defined :`export var='123'`

​    print:  env | grep 'HOME'

### array

##### 1. indexed array

```bash
myArray=(item1 item2 item3) # create 

${myArray[0]}  # array in index
${myArray[@]}  # arrray all
${myArray[*]}  # arrray all "${myArray[*]}" 加双引号看成整体


myArray[0]=value # array set element

length=${#myArray[@]} # array length

${!array[@]}  or ${!array[*]} # 输出有值的index
${array[@]:position:length}  # slice
 arr=(a b);arr+=(c d) # add  (a b c d) 
 
# array iteration
for item in ${myArray[@]}; do
    echo "$item"
done


```

##### 2. Associative arrays 

Bash 的新版本支持关联数组。关联数组使用字符串而不是整数作为数组索引。

`declare -A`可以声明关联数组。

```bash

decalre -A wordcount
# Assignment  
wordcount['aaa']=1
wordcount['bbb']=2
 # or
wordcount=(['aaa']=1 ['bbb']=2)


#reference
echo ${wordcount['name']}
```

### String
##### 1. substr

```bash
${varname:offset:length} 
# 返回变量$varname的子字符串，从位置offset开始（从0开始计算），长度为length.
# 注 ： offset为负值，表示从字符串的末尾开始算起。注意，负数前面必须有一个空格
$str: -5：2}

```

##### 2. remove substr

删除匹配的部分，返回剩下的部分

```bash
#head pattern
${variable#pattern} # 非贪婪
${variable##pattern} # 贪婪

#tail pattern 
${variable%pattern}
${variable%pattern}
```

##### 3.replace

```
${variable/pattern/string} # 贪婪匹配，只替换第一个
${variable//pattern/string}# 贪婪匹配，替换所有
```

##### 4. lenth

```bash
# 这些操作返回结果，不会改变字符串
${#varname} # string length
```

##### 5. upper & lower

```
# 转为大写
${varname^^}
# 转为小写
${varname,,}
```

### IO

STDIN : 标准输入  0  /dev/stdin

STDOUT: 标准输出  1  /dev/stdout

STDERR: 标准错误  2  /dev/stderr

黑洞：  /dev/null

```shell
command < input-file > output-file # rewrite  

command  > output-file  2>&1  <=>  command &> output-file  # 合并标准输入和
command >output-file 2> err-output-file 

command >> output-file 			# appending



```

##### heredoc

[Heredoc 入门 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/93993398)

### Branches

##### if

```bash
if [ condition1 ];then
    command_series1
elif [ condition2 ];then
    command_series2
else
    default_command_series3
fi
```

```bash
if [ condition1 ];then command_series1 ; fi
```

##### case  command

```bash
case $x in
	pattern1)
  		;;
  	pattern2)
  		;;
	*)
     statementn
esac
```

##### 短路

```bash
[[  ]] && command  # if true then
[[  ]] || command  # if false then
```

### Conditional Expressions

详见 [Bash Conditional Expressions (Bash Reference Manual) ~ Bash条件表达式(Bash参考手册) (gnu.org)](https://www.gnu.org/software/bash/manual/html_node/Bash-Conditional-Expressions.html#Bash-Conditional-Expressions)

###### 运算符

`[ ]` `[[  ]]` `test`

>[ ... ] : shell的命令，和`test`等价 
>
>[[ ...  ]] : shell 的关键字，支持字符串比较（包括正则
>
>1. [] : shell 命令，其中的表达式是他的命令行参数，所以 `< > &&` 必须转移
>
>	[[]] : 关键字，不进行命令行扩展，（在[[和]]之间的所有的字符都不会被文件扩展或是标记分割，但是会有参数引用和命令替换

##### 1. file

file exsit

    [ -d /bin]  # dir exist
    [ -e filename ] # file exist
    [ -f filename ]  #是否存在，且为普通文件
    [ -s filename ]  #file 非空未真

file privilege

```bash
[ -r filename ] # read
[ -w filename ] # write
[ -x filename ] # run
```

file modified time

```bash
[ file1 -nt file2 ] # file1 new time
[ file1 -ot file2 ] # file1 old time
[ file1 -ef file2 ] # inode equal => softlink -> file
```

##### 2. number & string

整数比较

-eq  -ne -gt -lt -ge -le 

字符串

```bash
-z -n == != > <
```

 注： `<` `>` 只用于字符串字典序比较，且在 `[[  ]]` 无需转义 ， 在 `[ ]` 需要转义

`==`,`!=` : 只能用于字符串

##### 3.正则

```
[[ string =~ regex ]]
```

##### 4.and or

```bash
[ condition1 -a condition2 ]
[ condition1 -o condition2 ]

[[ condition1 && condition2 ]]
[[ condition1 || condition2 ]]
```

### Loops

#### vars-like for

```bash
for arg in arg1 arg2 arg3 ; do
  echo $arg
done
```

```bash
for arg in arg1 arg2 arg3; do echo $arg; done
```

#### range for

```bash
for arg in `seq 10`; do
  echo $arg
done


seq 1 3 10   #generate sequence  ， 使用空格分开的序列字符串
echo {1..10..1} #  generate sequence  
array=($(seq 10)) # 转成 array
```

#### for in C-like syntax

```bash
LIMIT=10
for ((a=1; a<=LIMIT; a++)); do
  echo "$a "
done
```

#### **while**

```bash
LIMIT=10
a=1
while ((a<=LIMIT)); do
    echo "$a "
    ((a += 1))
done
```

#### **until**

```bash
LIMIT=10
a=1
util ((a > LIMIT)); do
    echo "$a "
    ((a += 1))
done
```

### Function

```bash
# define a function
function fun_name(){
    command...
}
## or
fun_name(){ # arg1 arg2 arg3
    command...
}


# apply a function
fun_name $arg1 $arg2 $arg3


# dereference
fun_name(){ # arg1
    eval "$1=hello"
}
fun_name arg1
## the above code block is equivalent to 
arg1=hello
```

### Fork

子进程 `command` (command)

注：子shell exit失效，管道也会开启子shell

### etc

##### 1. 连续执行n条命令  {} ()

```bash
（command1;command2) # 开启子shell 执行

{ command1;command2;} # 在当前shell 执行，开始空格，结尾分号
```

##### 2. 后台执行

```bash
nohup command > log 2>&1 
```

### Debugging

1. take good use of sh(1)
	for example:
	sh -n script: checks for syntax
	sh -v script: echo each command before executing it
	sh -x script: echo the result of each command in an abbreviated manner
2. use echo
3. use trap

### Parallel

use GNU parallel

管道会开启子shell

### Script with Style

1. Comment your code
2. Avoid using magic number
3. Use exit codes in a systematic and meaningful way
4. Use standardized parameter flags for script invocation

#### 数值运算

```
var=$(expr 1 + 2) # 加号必须有空格
let var=1+2 # 加号不能有空格
ff=$(( 1+2 )) # 双括号必须有空格
ff=$[ 1+2 ]
```

#### Bash 常用的快捷键













