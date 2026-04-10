---
title: GitHub+PicGo+jsDelivr搭建高速稳定免费图床
category: 图床
tags:
  - github
  - picgo
  - jsDelivr
published: 2026-04-09
---

# 配置 Github 图床

首先，我们需要在 Github 上创建一个用于存储图片的仓库。

## **创建新仓库**：

<img src="https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260409173954509.png" alt="image-20260408095335247" style="zoom: 67%;" />

> 要把添加README打开，不然还要自己初始化仓库

## 生成Personal access tokens

• 打开 Github，点击头像，选择 Settings。
• 进入 Developer settings，选择 Personal access tokens。
• 点击 Tokens (classic)，然后选择 Generate new token。
• 填写 Token 名称、选择过期时间并勾选所需权限。
• 点击 Generate token 并保存生成的 Token。

<img src="https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260409173954664.png" alt="Snipaste_2026-04-08_09-37-42" style="zoom:67%;" />

# 下载并配置 PicGo

PicGo 是一个用于上传图片的工具，Typora 和 Obsidian 都通过调用 PicGo 的 API 来实现图片上传功能。

## 下载PicGo

<img src="https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260408095855965.png" alt="img" style="zoom:50%;" />

##  配置 PicGo

<img src="https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260408095942789.png" alt="image-20260408095942729" style="zoom:50%;" />

<img src="https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260408100042874.png" alt="image-20260408100042813" style="zoom:50%;" />

# 配置 Typora

> 在 Typora 中，设置图片上传时调用 PicGo。

![image-20260408100141933](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260408100142068.png)

# jsDelivr

jsDelivr是一个致力于为开发者提供数千种Javascript、CSS等超过1650多种 Libraries 加速的免费CDN服务，该平台是首个「打通中国大陆与海外的免费CDN服务。 

jsDelivr平台将服务重心放在更快速的网路连线，利用CDN技术来确保每个地区的使用者都能获得最好的连接速度。

此外jsDelivr 可将不同的JavaScript 或CSS libraries 整合在一起，通过一段链结来载入网站，非常方便。 

具体使用方法：`https://cdn.jsdelivr.net/gh/你的用户名/你的仓库名@发布的版本号/文件路径`。

 其中：版本号不是必需的，是为了区分新旧资源，如果不使用版本号，将会直接引用最新资源。 例如： `https://lihao1991.gitee.io/images-bed/iblog-bg-8922321312932423345.jpg`

加载任何Github发布、提交或分支 

`https://cdn.jsdelivr.net/gh/user/repo@version/file`

 加载 jQuery v3.2.1 

`https://cdn.jsdelivr.net/gh/jquery/jquery@3.2.1/dist/jquery.min.js` 

完全省略该版本以获取最新版本

 `https://cdn.jsdelivr.net/gh/jquery/jquery/dist/jquery.min.js` 

在末尾添加/以获取资源目录列表 

`https://cdn.jsdelivr.net/gh/jquery/jquery/`

 至此，所有搭建工作已完成，可以高效的开发编写文档了。

![image-20260409174241093](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260409174241144.png)
