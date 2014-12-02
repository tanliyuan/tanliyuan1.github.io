---
layout: post
title: jsDuck生成的docs文档加载慢的解决方法
category: 技术
tags: jsDuck,javascript docs,extjs docs
description: 通过修改JSDuck模板文件，让生成的Extjs docs不再引用Google Font，加快页面加载速度
---

####现象:
　　无论是打开ExtJs Docs的官网，还是下载下来的本地docs,亦或是用jsDuck生成的docs，打开界面都要费半天的劲，打开开发者工具，可以看到在请求一个css文件时卡住了，费时21.00s,再细看请求头，可以发现request URL在访问Google字体，大陆没有Google服务器，所有这就是原因所在。  

![loadfontcss.png](../../../public/img/loadfontcss.png)

![request-font](../../../public/img/request-googleFont.PNG)

####解决办法:
　　这个是解决jsDuck生成的docs打开慢的解决办法。对于下载的Extjs包中的docs打开慢的问题可以找到对应引用css代码注释掉即可。
打开`%Ruby_installed_path%\Ruby193\lib\ruby\gems\1.9.1\gems\jsduck-5.3.4\template-min\`,可以看到如下文件结构：

![jsduck-template.png](../../../public/img/JSDuck-template.PNG)

　　将其中的template.html 、print-template.html、index-template.html  文件中对Google字体的引用注释或删除保存即可。下面这段对Google字体引用的代码均在以上文件`body`中的`<script>`标签内

```javascript
<script type="text/javascript">
  	(function(){
    		var protocol = (document.location.protocol === "https:") ? "https:" : "http:";
    		document.write("<link href='"+protocol+"//fonts.googleapis.com/css?family=Exo' rel='stylesheet' type='text/css' />");
  	})();
 </script> 

```