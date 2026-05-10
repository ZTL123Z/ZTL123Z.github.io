---
title: shell详解
published: 2026-05-09
updated: 2026-05-09
description: shell详解
image: "api"   
tags: [shell]
category: Linux
draft: false
---

#shell变量

## 变量的定义与赋值

> 注意变量与值之间不能有空格

```sh
name="TALEN"

变量名
变量类型，bash默认把所有变量都认为是字符串

bash变量是弱类型，无需事先声明类型
```

##变量替换/引用

```sh
[root@localhost ~]# music="蔡徐坤"
[root@localhost ~]# echo $music
蔡徐坤
[root@localhost ~]# echo ${music} # 可以省略花括号
蔡徐坤
```

## 变量名规则

- 只能包含数字、字母、下划线
- 不能以数字开头
- 严格区分大小写

## 变量作用域

- 环境变量：也是全局变量，所以进程都可以访问
- 局部变量：在shell函数里面或者脚本里面
- 位置参数变量：在==shell脚本==里面传递参数
- 特殊参数：
  - $?
    - 0:表示成功
    - 1-255:错误码

- 自定义变量
  - `双引号，变量名为替换为变量值`
  - `单引号，变量名识别为字符串`

```bash
[root@localhost ~]# n1=1
[root@localhost ~]# n2=2
[root@localhost ~]# n3="$n1"
[root@localhost ~]# echo $n3
1
[root@localhost ~]# n3='$n2'
[root@localhost ~]# echo $n3
$n2
```

- 每次调用bash/sh解释器执行脚本，都会开启一个子shell，因此不保留当前的shell变量，通过pstree命令查看进程树
- 调用source或者点`.`符号，在当前shell环境加载脚本，因此保留变量

![image-20260509112616588](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260509112706007.png)

- 反引号可以加载命令保留结果，如ls，pwd，whoami

# 环境变量概述

环境变量一般指的是用 `export` 内置命令导出的变量，用于定义 shell 的运行环境、保证 shell 命令的正确执行。

shell 通过环境变量确定登录的用户名、`PATH` 路径、文件系统等各种应用。

## 临时与永久生效

- **临时创建**：可以在命令行中临时创建，但用户退出 shell 终端后变量即丢失。
- **永久生效**：需要修改环境变量配置文件。

### 配置文件分类

- **用户个人配置文件**（仅对当前用户生效）
  - `~/.bash_profile`
  - `~/.bashrc`（远程登录用户特有文件）

- **全局配置文件**（对所有登录系统的用户生效）
  - `/etc/profile`
  - `/etc/bashrc`
  - **建议**：最好在 `/etc/profile.d/` 目录下创建自定义配置文件，而非直接修改主文件。

## 常用命令

| 命令      | 说明                                   |
| --------- | -------------------------------------- |
| `set`     | 输出所有变量（包括全局变量、局部变量） |
| `env`     | 只显示全局变量                         |
| `declare` | 输出所有变量，同 `set`                 |
| `export`  | 显示和设置环境变量值                   |

## 撤销环境变量

```bash
unset 变量名
```

## 设置只读变量

- `readonly`：只有 shell 结束时，只读变量才会失效。

### 使用示例

1. 直接输入 `readonly` 可显示当前系统的所有只读变量。

2. 设置只读变量：

   bash

   ```
   [root@chaogelinux ~]# readonly name="超哥"
   ```

3. 尝试修改只读变量会报错：

   bash

   ```
   [root@chaogelinux ~]# name="chaochao"
   -bash: name: 只读变量
   ```

# 特殊变量

## 位置参数变量

```sh
$0		获取shell脚本文件名，以及脚本路径
$n		获得shell脚本的第n个参数，n在1~9之间，如$1,$2,$9,大于9则需要，${10},参数大括号隔开
$#		获得shell脚本后面参数的总个数
$*		获得shell脚本所以参数，加上“”的作用是接收所以参数作为一个单个字符串
$@		加上“”，所有参数作为独立字符串，如"$1" "$2" "$3" ..., 空格是保留的，变成数组
```

## 特殊状态变量

```sh
$?		上一次命令执行状态返回值，0是正确，非0是失败
$$		当前shell脚本的进程号
$!		上一次后台进程的PID
$_		上一次执行的命令的最后一个参数
```

# shell子串

## bash一些内置的基础命令

```sh
echo
eval
exec
export
read
shift
```

> echo命令

```sh
-n 不换行输出
-e 解析字符串中的特殊符号

\n	换行 
\r	回车
\t	制表符 4个空格
\b	退格

[root@localhost code]# echo 你好; echo 你好啊
你好
你好啊
[root@localhost code]# echo -n 你好; echo 你好啊
你好你好啊
[root@localhost code]# echo -n 你好; echo -n 你好啊
你好你好啊[root@localhost code]# 

[root@localhost code]# echo "我看你挺\n好看的"
我看你挺\n好看的
[root@localhost code]# echo -e "我看你挺\n好看的"
我看你挺
好看的
```

> eval 	执行多个命令

```sh
[root@localhost code]# eval ls; cd /opt
test.sh
[root@localhost opt]#  
```

> exec 不创建子进程，执行后续命令，且执行完毕后，自动exit

```
[root@localhost opt]#  su talen
[talen@localhost opt]$ exec date
2026年 05月 09日 星期六 15:34:47 CST
[root@localhost opt]# 
```

## shell子串的用法

```sh
${变量}						放回变量值
${#name}					 放回变量字符长度
${变量:start}					放回变量start数值之后的字符
${变量:start:length}			提取start之后的length现在的字符
${变量#word}					从变量开头删除最短匹配的word子串
${变量##word}					从变量开头，删除最长匹配的word
${变量%word}					从变量结尾删除最短word
${变量%%word}					从变量结尾删除最长word
${变量/pattern/string}		用string代替第一个匹配的pattern
${变量//pattern/string}		用string代替所有的pattern
```

### 小案例，批量改变文件名

```sh
[root@localhost code]# touch chao_{1..10}_finished.jpg
[root@localhost code]# ls
chao_10_finished.jpg  chao_3_finished.jpg  chao_6_finished.jpg  chao_9_finished.jpg
chao_1_finished.jpg   chao_4_finished.jpg  chao_7_finished.jpg
chao_2_finished.jpg   chao_5_finished.jpg  chao_8_finished.jpg
// 本身不会打印出来${file_name//_finished/} 所以需要echo
[root@localhost code]# for file_name in `ls *fin*jpg`; do mv $file_name `echo ${file_name//_finished/}`; done
[root@localhost code]# ls
chao_10.jpg  chao_2.jpg  chao_4.jpg  chao_6.jpg  chao_8.jpg
chao_1.jpg   chao_3.jpg  chao_5.jpg  chao_7.jpg  chao_9.jpg
```

## 拓展变量

```
如果parameter变量值为空，返回word字符串，赋值给result变量
result=${parameter:-word}
如果parameter变量值为空，则word替代变量值，以及parameter本身
result=${parameter:=word}
如果parameter变量值为空，word当作stderr输出，抛出错误信息
result=${parameter:?word}
如果parameter变量值为空，什么都不做，否则word返回
result=${parameter:+word}
```

# 内置命令和外置命令

>内置命令：在系统启动的时候就加载如内存，常驻内存，执行效率更高，但是占用资源，如cd
>
>外置命令：系统需要从硬盘中读取程序文件，再读入内存加载

##外置命令，也称为，自己单独下载的文件系统命令，处于bash shell之外的程序

```sh
/bin
/usr/bin
/sbin
/usr/bin
```

比如ps命令

- 用type命令验证

```sh
[root@localhost ~]# type ps
ps 是 /usr/bin/ps
```

- 外置命令的特点是：一定会开启子进程执行

![image-20260510091535360](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260510091552476.png)

## 内置命令

>内置命令不会产生子进程区执行
>
>内置命令和shell是为一体的，是shell的一部分，不需要单独去读取某个文件，系统启动后，就会执行在内存中

type命令验证，如cd

```sh
[root@localhost ~]# type cd
cd 是 shell 内嵌
```

- 使用 compgen -b 查看内置命令

```sh
[root@localhost ~]# compgen -b
.
:
[
alias
bg
bind
break
builtin
caller
cd
command
compgen
complete
compopt
continue
declare
dirs
disown
echo
enable
eval
exec
exit
export
false
fc
fg
getopts
hash
help
history
jobs
kill
let
local
logout
mapfile
popd
printf
pushd
pwd
read
readarray
readonly
return
set
shift
shopt
source
suspend
test
times
trap
true
type
typeset
ulimit
umask
unalias
unset
wait
```

# 四种执行方式的区别

| 方式           | 执行 Shell | 影响父 Shell | 需要执行权限 | 解释器来源  | 脚本含 `exit` 的影响 |
| :------------- | :--------- | :----------- | :----------- | :---------- | :------------------- |
| `source` / `.` | 当前 Shell | ✅ 是         | ❌ 只需读     | 当前 Shell  | 退出当前 Shell       |
| `bash`         | 子 Shell   | ❌ 否         | ❌ 只需读     | 显式 `bash` | 仅退出子 Shell       |
| `./`           | 子 Shell   | ❌ 否         | ✅ 是         | shebang 行  | 仅退出子 Shell       |

## 快速记忆

- **要脚本改动留在当前 Shell** → 用 `source` 或 `.`
- **只想临时运行，不污染环境** → 用 `bash` 或 `./`
- **用特定解释器（如 Python）** → 用 `./` + shebang
- **脚本没有可执行权限** → 用 `bash` 或 `source`（但不能用 `./`）

# shell数值计算

![image-20260510094850252](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260510094850390.png)

![image-20260510095127955](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260510095128091.png)

shell的一些命令，只支持整数的运算，也可以用于小数的运算

##双小括号(())

![image-20260510095613344](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260510095613390.png)

## let命令运算

>let命令的执行，效果等同于双小括号(())
>
>但是 ，双括号效率更高

```sh
[root@localhost code]# let a=5+6
[root@localhost code]# echo $a
11
```

## expr命令

### 基本用法

```sh
# expr 命令不是很好用，基于空格传入参数，但是在shell里面一些元字符都会有一些特殊含义
# 需要用到\转义符
[root@localhost code]# expr 5 + 3
8
[root@localhost code]# expr 5 - 3
2
[root@localhost code]# expr 5 \* 3
15
[root@localhost code]# expr 5 \/ 3
1
```

### expr模式匹配

>expr命令也支持模式匹配功能
>
>2个特殊符号
>
>：冒号，计算字符串的字符数量
>
>.* 任意字符串重复0次或者多次
>
>语法
>
>expr 字符串  ":" ".*"

## bc命令

### 基本用法

```sh
[root@localhost code]# bc
bc 1.06.95
Copyright 1991-1994, 1997, 1998, 2000, 2004, 2006 Free Software Foundation, Inc.
This is free software with ABSOLUTELY NO WARRANTY.
For details type `warranty'. 
2+2
4
2-2
0
2*2
4
2/2
1
1.1*2
2.2
2.2-1
1.2
```

### 和管道符结合使用

```sh
[root@localhost code]# echo "4*4" | bc
16
[root@localhost code]# echo "4.2*4" | bc
16.8
[root@localhost code]# num=5
[root@localhost code]# result=`echo $num*4|bc`
[root@localhost code]# echo $result
20
```

### bc案例

> 题目计算出1~1000的总和
>
> 数学公式
>
> 1+2+3+4+....+1000

- `tr` **只处理单个字符**，无法替换字符串。例如想将 `"bad"` 替换成 `"good"` 要用 `sed` 或 `awk`。
- 字符集合的表示方式：
  - 连续范围：`a-z`、`A-Z`、`0-9`。
  - 预定义类：`[:alnum:]`, `[:alpha:]`, `[:digit:]`, `[:lower:]`, `[:upper:]`, `[:space:]` 等（使用时通常加上外层 `[]`，如 `[[:digit:]]`）。
  - 转义字符：`\n`（换行）、`\t`（制表符）、`\\`（反斜线）等。
- `tr` 默认不处理多字节字符（如中文），某些实现可能有问题。

```sh
# 方案一 tr
[root@localhost code]# echo {1..100} | tr " " "+"
1+2+3+4+5+6+7+8+9+10+11+12+13+14+15+16+17+18+19+20+21+22+23+24+25+26+27+28+29+30+31+32+33+34+35+36+37+38+39+40+41+42+43+44+45+46+47+48+49+50+51+52+53+54+55+56+57+58+59+60+61+62+63+64+65+66+67+68+69+70+71+72+73+74+75+76+77+78+79+80+81+82+83+84+85+86+87+88+89+90+91+92+93+94+95+96+97+98+99+100
# 方案二 seq
seq -s "+" 100
seq 专门用于输出数字序列，-s "+" 指定用加号作为分隔符，直接得到相同表达式。
[root@localhost code]# seq -s "+" 100
1+2+3+4+5+6+7+8+9+10+11+12+13+14+15+16+17+18+19+20+21+22+23+24+25+26+27+28+29+30+31+32+33+34+35+36+37+38+39+40+41+42+43+44+45+46+47+48+49+50+51+52+53+54+55+56+57+58+59+60+61+62+63+64+65+66+67+68+69+70+71+72+73+74+75+76+77+78+79+80+81+82+83+84+85+86+87+88+89+90+91+92+93+94+95+96+97+98+99+100

# 方案三
xargs 将上一步输出的字符串作为一份参数传递给 expr 命令。
关键在于：xargs 默认会去除首尾空白，并按空格、换行、制表符分割参数。但由于这里整个字符串中包含空格和加号，xargs 会将每个由空白分隔的“单词”当作单独的参数，即：
[root@localhost code]# seq -s " + " 100 | xargs expr 
5050

[root@localhost code]# echo {1..100} | tr " " "+" | bc
5050
[root@localhost code]# seq -s "+" 100 | bc
5050
```





# 开发网站存活检测脚本

```sh
CheckURL(){

    timeout=5
    # 计数器
    fails=0
    success=0

    while true
        do
            wget --timeout=${timeout} --tries=1 http://ztluu.cn/ -q -O /dev/null
            # 判读执行是否成功 $? 判断上一条命令是否执行成功，成功为0，不成功为1-255
            if [ $? -ne 0 ]; then
                    let fails=fails+1
            else
                    let success=success+1
            fi
            # 成功
            if [ ${success} -ge 1 ]; then
                echo "该网站正在健康运行"
                exit 0
            fi
            # 失败
            if [ ${fails} -ge 2 ]; then
                echo "该网站可能挂了"           
                exit 2
            fi

        done
}
```

# 条件测试

![image-20260510143737266](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260510143737345.png)

## test条件测试

test命令评估一个表达式，他的结果是真，还是假，如果条件为真，那么命令执行状态码结果就为0，否则就是不为0，通过`$?`取值

> test命令参数
>
> -e 	判断该文件是否存在，（普通文件，目录），存在就为真，否则为假

###test 的语法参数大全

```sh
# 1.关于文件名的类型侦测（存在与否）

 -e：该文件名是否存在？（常用）
 -f：该文件名是否为文件（file）？（常用）
 -d：该文件名是否为目录（directory）？（常用）
 -b：该文件名是否为一个 block device 装置？
 -c：该文件名是否为一个 character device 装置？
 -s：该文件名是否为一个 Socket 文件？
 -p：该文件名是否为一个 FIFO（pipe）文件？
 -L：该文件名是否为一个连结档？
# 2.关于于文件的权限侦测
 -r：侦测该文件名是否具有可读的属性？
 -w：侦测该文件名是否具有可写的属性？
 -x：侦测该文件名是否具有可执行的属性？
 -u：侦测该文件名是否具有 SUID 的属性？
 -g：侦测该文件名是否具有 SGID 的属性？
 -k：侦测该文件名是否具有 Sticky bit 的属性？
 -s：侦测该文件名是否为非空白文件？
 # 3. 两个文件之间的比较

 `-nt`：判断 file1 是否比 file2 新（newer than）  
 示例：`test file1 -nt file2`
 `-ot`：判断 file1 是否比 file2 旧（older than）  
 示例：`test file1 -ot file2`
 `-ef`：判断 file1 与 file2 是否为同一文件（指向同一个 inode），常用于判断硬链接  
 示例：`test file1 -ef file2`
# 4. 两个整数之间的判定
 `-eq`：两数值相等（equal）
 `-ne`：两数值不等（not equal）
 `-gt`：n1 大于 n2（greater than）
 `-lt`：n1 小于 n2（less than）
 `-ge`：n1 大于等于 n2（greater than or equal）
 `-le`：n1 小于等于 n2（less than or equal）
例：`test n1 -eq n2`
# 5. 字符串的判定
 `test -z string`：判定字符串是否为 0（空字符串）？若为空，则为 true。
 `test -n string`：判定字符串是否非 0？若为空字符串，则为 false。  
 **注**：`-n` 通常可以省略。
 `test str1 = str2`：判定 str1 是否等于 str2，若相等则返回 true。
 `test str1 != str2`：判定 str1 是否不等于 str2，若相等则返回 false。
# 6. 多重条件判定
 `-a`（and）：两状况同时成立才返回 true。  
 示例：`test -r filename -a -x filename`（文件同时具有 r 和 x 权限时成立）
 `-o`（or）：两状况任何一个成立就返回 true。  
 示例：`test -r filename -o -x filename`（文件具有 r 或 x 权限时成立）
 `!`：反相状态。  
  示例：`test ! -x file`（当 file 不具有 x 权限时返回 true）
```

###实践

 -e：该文件名是否存在？（常用）

```sh
[root@localhost code]# ls
check_nginx_status.sh
[root@localhost code]# test -e check_nginx_status.sh 
[root@localhost code]# echo $?
0
[root@localhost code]# test -e check.sh 
[root@localhost code]# echo $?
1
```

 -f：该文件名是否为文件（file）？（常用）

```sh
[root@localhost code]# test -f check_nginx_status.sh 
[root@localhost code]# echo $?
0
[root@localhost code]# ls
check  check_nginx_status.sh
[root@localhost code]# test -f check 
[root@localhost code]# echo $?
1
```

 -d：该文件名是否为目录（directory）？（常用）

```sh
[root@localhost code]# test -d check 
[root@localhost code]# echo $?
0
```

## 中括号条件测试[ ]

脚本中经常进行条件测试，用的最多的都是中括号[]

test 和 [   ]的作用是一样的

注意：中括号，前后的空格必须有

> 注意 ！！！
>
> 在条件测试中使用变量，必须添加双引号
>
> [ -n "$filename" ]

```sh
[root@localhost code]# ls
hello.py
[root@localhost code]# [ -f hello.py ] && echo ok || echo no
ok
[root@localhost code]# rm -f hello.py 
[root@localhost code]# [ -f hello.py ] && echo ok || echo no
no
[root@localhost code]# filename=hello.py
[root@localhost code]# [ -f "$filename" ] && echo ok || echo no
no
```

## 字符串比较测试

![image-20260510153040889](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260510153040966.png)

注意

- 对应字符串变量的比较
- 一定要记得给变量添加双引号
- 使用等于号的值判断，左右两边也必须有空格

## 数值比较测试 -a 逻辑判断符号

![image-20260510153820854](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260510153820905.png)

### 在中括号中，以及test中数值测试的用法

> 在中括号中，使用数学比较符号，请添加转移符号，不然请使用[[   ]]
>
> `\>`

```sh
yes
[root@localhost code]# [ 1 \> 2 ] && echo yes || echo no
no
```

### 符号实战

```sh
[root@localhost code]# n1=98;n2=99
[root@localhost code]# test "$n1" -eq "$n2" && echo yes || echo no
no
[root@localhost code]# test "$n1" -ge "$n2" && echo yes || echo no
no
[root@localhost code]# test "$n1" -gt "$n2" && echo yes || echo no
no
[root@localhost code]# test "$n1" -le "$n2" && echo yes || echo no
yes
[root@localhost code]# test "$n1" -lt "$n2" && echo yes || echo no
yes 
```

### 双中括号

对单中括号的补充，双中括号还支持正则处理

在双中括号中，就不需要转义符号了

![image-20260510161022464](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260510161022541.png)

在工作中，用的最多的就是单中括号，双括号属于特殊场景的扩展语法

并且 双中括号也是支持`-eq -lt < > =`

# 函数是什么

linux别名功能

alias

```sh
[root@localhost ~]# alias
alias cp='cp -i'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias ls='ls --color=auto'
alias mv='mv -i'
alias rm='rm -i'
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
```

>shell函数开发
>
>函数的特点，类似于alias别名一样，能够简化linux命令的操作，让整个命令更易读，更易用

## shell函数定义的语法

```sh
# 标准的
function 函数名(){
	函数体
	你想执行的linux命令
	return 返回值
}

# 偷懒写法
# 当使用function关键字时候，可以省略括号
function 函数名{
    函数体
	你想执行的linux命令
	return 返回值
}

# 老司机写法
# 必须有括号
函数名(){
	函数体
	你想执行的linux命令
	return 返回值
}

# 执行函数
函数名
```

## 执行函数的基本概念

有关函数执行的基础概念

- 执行shell函数，直接写函数名字即可，无需添加其他内容
- 函数必须先定义，再执行，shell脚本自上而下加载
- 函数体内定义的变量，称为局部变量
- 函数体内需要添加return语句，作用是退出函数，且赋予返回值给调用该函数的程序，也就是shell脚本
- return语句和exit不同
  - return是结束函数的执行，返回一个（返回值）
  - exit是解释shell环境，返回一个（退出值）给当前的shell
- 函数如果单独写入一个文件里，需要用source读取
- 函数内，使用local关键字，定义局部变量

## 函数脚本传参

![image-20260510200306423](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260510200323437.png)

只定义了三个参数，最多只接受三个参数，再多就没用了

