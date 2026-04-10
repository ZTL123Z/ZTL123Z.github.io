---
title: firefly写作模版
published: 2026-04-07
description: 这是文章的简短描述
image: ./images/firefly1.avif
tags: [Firefly, 布局, 博客, 使用指南]
category: 博客指南
draft: false
---
# 写作模版
```markdown
title:        string	     # 是	文章标题
published:	  date	         # 是	发布日期
description:  string	     # 否	文章简短描述，显示在首页文章卡片上
image:	      ./cover.jpg	 # 否	封面图片路径 可以写 "api" 
tags:	      string[]	     # 否	文章标签
category:	  string	     # 否	文章分类
draft:	      boolean	     # 否	是否为草稿，草稿不会对读者可见

updated:	  date	         # 否	更新日期，未设置则默认使用发布日期
comment:	  boolean	     # 否	是否启用评论，默认 true
pinned:	      boolean	     # 否	是否置顶在文章列表顶部
licenseName:  string	     # 否	自定义许可证名称
licenseUrl:	  string	     # 否	自定义许可证链接
sourceLink:	  string	     # 否	文章来源链接
password:	  string	     # 否	文章密码，设置后文章将被加密保护，详见 文章加密
passwordHint: string	     # 否	密码提示，显示在密码输入框上方
slug:	      string	     # 否	自定义 URL 路径
lang:	      string	     # 否    文章语言代码（如 zh-CN），仅当与站点默认语言不同时设置
author:	      string	     # 否	文章作者
```