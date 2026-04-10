---
title: vi/vim快速入门
category: linux
tags:
  - linux
  - vim
  - vi
published: 2026-04-09 
---

## vim的三种模式

![image-20260409135653658](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260409135654064.png)

## vim的快捷键

1. 正常模式: **yy**, 复制, **5yy**向下复制5行; **p** 粘贴
2. 正常模式: **dd**, 删除一行，**5dd**删除下面5行
3. 在文件中查找某个单词 [命令行下 `/关键字`，回车 查找，输入 `n` 就是查找下一个
4. 设置文件的行号，取消文件的行号.[命令行下 `:set nu` 和 `:set nonu`]
5. 编辑 `/etc/profile` 文件，使用快捷键到该文档的最末行 [`G`] 和最首行 [`gg`]，或者`{` ， `}`
6. 命令模式下**:n**,就是到第n行
7. 在一个文件中输入 `"hello"`，然后又撤销这个动作 `u`

![image-20260409141536785](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260409141537099.png)
