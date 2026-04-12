---
title: Linux-shell编程
published: 2026-04-12          
description: shell编程     
image: "api"   
tags: [linux, shell]      
category: linux      
draft: false       
---

## shell脚本的执行方法

1. 脚本以`#!/bin/bash`开头
2. 脚本须有可执行权限

- 第一个shell脚本

```shell
#!/bin/bash
echo "Hello,World!"
```

- 脚本执行的两个方法

1. 有x执行权限，直接`./hello.sh`
2. 没有x执行权限，用`sh hello.sh`

## shell变量

### shell变量介绍

1. Linux Shell中的变量分为，系统变量和用户自定义变量
2. 系统变量：`$HOME`、`$PWD`、`$SHELL`、`$USER`等等
3. 显示当前shell中所有的变量：`set`

### shell变量的定义

1. 定义变量：变量名=值
2. 撤销变量：unset 变量
3. 声明静态变了：`readonly`变量，注意不能unset

### 规范命名

1. 变量名称可以由字母、数字和下划线组成，但是不能以数字开头。
2. 等号两侧不能有空号
3. 变量一般大写

### 将命令返回给变量

```shell
1. A=`date`反引号，运行里面的命令，并把结果返回给变量A
2. A=$(date) 等价于反引号
```

```shell
#!/bin/bash
A=$(date)
echo A=$A
------------------------------------------------------------------
[root@localhost shcode]# sh var.sh 
A=2026年 04月 12日 星期日 10:11:41 CST
```

## 设置环境变量

### 基本语法

- export 变量名=值 （将shell变量输出为环境变量）
- source 配置文件   （让修改过后的配置信息理解生效）
- echo $变量名        （输出环境变量的值） 

> /etc/profile

![image-20260412103100988](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260412103101174.png)

```sh
source /etc/profile // 快速生效
```

### 多行注释

```shell
:<<!
内容
!
```

## 位置参数变量 & 预定义变量

位置参数变量

![image-20260412104353518](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260412104353709.png)

预定义变量

![image-20260412105243269](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260412105243425.png)

## 运算符

基本语法。建议使用`$[运算式]`，最简洁

![image-20260412110012146](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260412110012239.png)

## 条件判断

### 基本语法

- 判断语句

1. `=` 字符串比较
2. 两个整数的比较

| 符号 | 功能                   |
| :--: | ---------------------- |
|  lt  | 小于 less than         |
|  le  | 小于等于 less equel    |
|  eq  | 等于 equel             |
|  gt  | 大于 greater than      |
|  ge  | 大于等于 greater equel |
|  ne  | 不等于 not equal       |

![image-20260412111808685](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260412111808726.png)

​	3. 按照文件权限比较

| 符号 | 作用         |
| :--: | ------------ |
|  -r  | 有读的权限   |
|  -w  | 有写的权限   |
|  -x  | 有执行的权限 |

```shell
if [ -w /root/shcode/aaa.txt]
then 
		echo "存在"
if
```

4. 按照文件类型进程判断

| 符号 | 作用                         |
| :--: | ---------------------------- |
|  -f  | 文件存在并且是一个常规的文件 |
|  -e  | 文件存在                     |
|  -d  | 文件存在并且是一个目录       |

```shell
if [ -f /root/shcode/aaa.txt]
then 
		echo "存在"
if
```

### 多分支

- 写法一

```
if [ 条件判断 ]
then
    代码
elif [ 条件判断 ]
then
    代码
else
    上面所有条件都不满足时执行的代码
fi
```

- 写法二

```shell
if [ 条件判断 ]; then
    代码
elif [ 条件判断 ]; then
    代码
else
    上面所有条件都不满足时执行的代码
fi
```

## 流程控制case

![image-20260412122825586](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260412122906242.png)

```shell
case $1 in    
"1")
	echo "周一" ;;
"2")
        echo "周二" ;;
 *)
        echo "other..." ;;
esac
```

## 循环流程

### for 变量 in 值1 值2 值3

感觉这个是foreach

```sh
for 变量 in 值1 值2 值3
do
程序
done
```

```sh
for 变量 in "$@"  # $@是分开变量的，$*是当做一个整体
do
	程序
done
```



### for (( 初始值;循环条件;变量变化 ))

```sh
SUM=0
for(( i=1; i<=100; i++ ))
do
	SUM=$[$SUM+$i]
done
```

### while循环

- 语法

```sh
while [ 条件判断式 ]
do
	程序
done
```

- 案例

```sh
SUM=0
i=0
while [ $i -le 100 ]
do
	SUM=$[$SUM+$i]
	i=$i+1
done
```

## read获得输入

- 基本语法

> read (选项) (参数)

- -p: 指定读取值的提示符
- -t :指定读取值时候等待的时间，超时就不再等待

```shell
read -p "输入一个数NUM1" NUM1
read -t 10 -p "输入一个数NUM2" NUM2
```

## 函数

### 系统函数

- basename [pathname] [suffix]        //去掉前缀和后缀

```bash
[root@localhost shcode]# basename /home/talen/test.txt
test.txt
[root@localhost shcode]# basename /home/talen/test.txt .txt
test
```

- dirname ：返回完整路径最后/的前面部分，就是路径

```bash
[root@localhost shcode]# dirname /home/talen/test.txt 
/home/talen
```

### 自定义函数

```sh
#!/bin/bash
# 定义求和函数 getSum
# 参数： $1 (第一个数), $2 (第二个数)
function getSum() {
    # 判断参数个数是否正确
    if [ $# -ne 2 ]; then
        echo "错误：函数需要传入两个数字参数！"
        return 1
    fi

    local num1=$1
    local num2=$2
    
    # 检查输入是否为数字
    if ! [[ "$num1" =~ ^[0-9]+$ && "$num2" =~ ^[0-9]+$ ]]; then
        echo "错误：输入必须为整数！"
        return 1
    fi

    # 计算并输出结果
    local sum=$((num1 + num2))
    echo "和是 = $sum"
}

# 主逻辑：输入两个值
read -p "请输入一个数 n1=" n1
read -p "请输入一个数 n2=" n2

# 调用自定义函数
getSum "$n1" "$n2"
```

