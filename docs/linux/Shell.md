# Shell

## define

```
Shell 是一个用 C 语言编写的程序，通过 Shell 用户可以访问操作系统内核服务
Shell 编程跟 java、 php 编程一样，只要有一个能编写代码的文本编辑器和一个能解释执行的脚本解释器就可以了。
Linux 的 Shell 种类众多， 一个系统可以存在多个 shell，可以通过 cat /etc/shells 命令查看系统中安装的 shell
Bash 由于易用和免费，在日常工作中被广泛使用。同时， Bash 也是大多数Linux 系统默认的 Shell
```

```
#!是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行，即使用哪一种 Shell
```

## echo

```
echo '' > xx.txt 往文件写数据
echo '' >> xx.txt 追加
```

```
直接写 hello.sh，linux系统会去PATH里寻找有没有叫 hello.sh的
用 ./hello.sh 告诉系统说，就在当前目录找
还可以作为解释器参数运行。 直接运行解释器，其参数就是 shell 脚本的文件名 :/bin/sh /root/hello.sh
```

## 变量

```
变量 = 值  eg: xx="xx"
【变量名和等号之间不能有空格】
【变量名命名】
1.首字母必须为字母
2.中间不能有空格，可以使用下划线
3.不能使用标点符号
4.不能使用 bash 里面的关键字 bash -c help  cat builtin commands
使用定义过的变量，在变量名前加 $
eg:
	name="aladdin"
	echo $name
	echo ${name}
```

### 变量类型

- 局部变量 

  ``仅在当前 shell 实例中有效``

- 环境变量

  ``所有程序都能访问环境变量，使用 set 命令查看当前环境变量``

- Shell 变量

  ``shell 程序设置的特殊变量，一部分是环境变量，一部分是局部变量``

### 参数传递

```
脚本内获取参数的格式 【 $n 】 n 代表数字，1 为执行脚本第一个参数，2 为执行脚本的第二个参数，$0 表示当前脚本名称
```

#### 特殊字符

| $#   | 传递到脚本的参数个数                                         |
| ---- | ------------------------------------------------------------ |
| $*   | 以一个单字符串显示所有向脚本传递的参数                       |
| $$   | 脚本运行的当前进程的 ID 号                                   |
| $!   | 后台运行的最后一个进程的 ID 号                               |
| $@   | 与 $* 相同，使用的时候加引号，并在引号中返回多个参数作为一个参数 这个在循环里面可以用 |
| $?   | 显示最后命令的退出状态， 0 表示没有错误，其他任何值表示有错误 |

### 运算符

``shell 支持 算术、关系、布尔、字符串``

```
原生 bash 不支持简单的数学运算，但是可以通过 expr 实现
【表达式和运算符之间要有空格 2 + 2 ;完整的表达式要被 `` 包含】
【`expr 3 + 4`】
(()) $[] 也可以进行算术运算

```

## 流程控制

### if  else

```shell
if condition1
then 
command1
elif condition2
then
command2
else 
command3
fi
```



### 条件表达式

```
EQ EQUAL 								 ==
NE NOT EQUAL 		 			   !=
GT GERATER THAN 			   >
LT LESS THAN 						 <
GE GERATER THAN OR EQUAL >=
LE LESS THAN OR EQUAL    <=
```

```

```

