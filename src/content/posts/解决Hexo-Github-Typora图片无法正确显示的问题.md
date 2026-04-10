---
title: 解决Hexo+Github+Typora图片无法正确显示的问题
published: 2026-04-08 
category: markdown
tags: 
  - Hexo
  - 博客
  - github
  - typora
  - 图床
---

## 安装插件

首先我们需要安装 `hexo-asset-image` 插件

```text
npm install hexo-asset-image --save
```

## 修改文件代码

但是安装后还是没有解决问题哈

然后找到 `/node_modules/hexo-asset-image/index.js` 这个文件，将它的内容替换为如下内容

```text
'use strict';
var cheerio = require('cheerio');
     
// http://stackoverflow.com/questions/14480345/how-to-get-the-nth-occurrence-in-a-string
function getPosition(str, m, i) {
  return str.split(m, i).join(m).length;
}
     
var version = String(hexo.version).split('.');
hexo.extend.filter.register('after_post_render', function(data){
  var config = hexo.config;
  if(config.post_asset_folder){
       var link = data.permalink;
   if(version.length > 0 && Number(version[0]) == 3)
      var beginPos = getPosition(link, '/', 1) + 1;
   else
      var beginPos = getPosition(link, '/', 3) + 1;
   // In hexo 3.1.1, the permalink of "about" page is like ".../about/index.html".
   var endPos = link.lastIndexOf('/') + 1;
    link = link.substring(beginPos, endPos);
     
    var toprocess = ['excerpt', 'more', 'content'];
    for(var i = 0; i < toprocess.length; i++){
      var key = toprocess[i];
      
      var $ = cheerio.load(data[key], {
        ignoreWhitespace: false,
        xmlMode: false,
        lowerCaseTags: false,
        decodeEntities: false
      });
     
      $('img').each(function(){
       if ($(this).attr('src')){
           // For windows style path, we replace '\' to '/'.
           var src = $(this).attr('src').replace('\\', '/');
           if(!/http[s]*.*|\/\/.*/.test(src) &&
              !/^\s*\//.test(src)) {
             // For "about" page, the first part of "src" can't be removed.
             // In addition, to support multi-level local directory.
             var linkArray = link.split('/').filter(function(elem){
               return elem != '';
             });
             var srcArray = src.split('/').filter(function(elem){
               return elem != '' && elem != '.';
             });
             if(srcArray.length > 1)
               srcArray.shift();
             src = srcArray.join('/');
             $(this).attr('src', config.root + link + src);
             console.info&&console.info("update link as:-->"+config.root + link + src);
           }
       }else{
           console.info&&console.info("no src attr, skipped...");
           console.info&&console.info($(this));
       }
      });
      data[key] = $.html();
    }
  }
});
```

> 然后正常显示

![image-20260408104125001](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260408104125105.png)
