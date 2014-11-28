---
layout: post
title: 利用JSFiddle为博客内嵌javascript-Demo演示
category: 技术
tags: Python
description: 利用JSFiddle在博客中嵌入JS demo，读者可以方便的在result，html,css,js选项卡自由切换以查看相应内容，也可以修改代码并实时看到改变后的结果
---

&emsp;&emsp;在写javascript及其框架相关的博客时，光贴代码不能让读者看到运行结果，虽然贴图能让读者清晰的看到结果,但是经常会出现有读者抱怨示例代码没法运行，这很有可能是在代码粘贴时格式上出了问题，亦或是截图和上传的代码不同步。

# JSFiddle
　　如果你是一名前端博主，你希望通过**代码**+**演示**来透彻的讲解一些技巧，并且无缝嵌入你的博客,那么[jsFiddle](http://jsfiddle.net/)是一个很好的选择。

先给大家看一个本文嵌入Extjs Demo 演示：

<iframe width="100%" height="300" src="http://jsfiddle.net/timvasil/2gVUh/1/embedded/result,js,html,css,resources" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

&emsp;&emsp;Demo右上角有个云状图标，就是jsFiddle的logo，面板上方有*Javascript*，*Resources*,*HTML*,*CSS*,*Result* 5个选项卡，
点击不同选项卡，可以查看相应内容。将demo嵌入当前文章很简单，代码如下：

    先给大家看一个本文嵌入Extjs Demo 演示：  
    <iframe width="100%" height="300" src="http://jsfiddle.net/timvasil/2gVUh/1/embedded/result,js,html,css,resources" allowfullscreen="allowfullscreen" frameborder="0"></iframe>
    Demo右上角有个云状图标，就是jsFiddle的logo，
    
　　从代码中可以看出，其实就是在文章页面html中插入`<iframe>`标签，*width*、*height*、*allowfullscreen*、*frameborder*
属性分别是demo演示区域的*宽度*，*高度*，*是否允许全屏显示demo*，*demo区域边框像素值*，以上属性对不同demo都通用，
要引用不同demo关键在src，下面分析src值URL的语法。

    http://{url_of_the_fiddle}/embedded/[{tabs}/[{style}]]/

- **url_of_the_fiddle**   jsFiddle的完整地址[ http://jsfiddle.net]( http://jsfiddle.net)  
- **tabs**    要显示的标签面板及其排列顺序，默认为*js*，*resources*，*html*，*css*，*result*,  
当没有引用外部资源时，resources选项卡将不显示
- **skin**    demo区域使用的皮肤，默认：*light*  

## 如何得到demo的URL
  ![jsfiddle-iframe.PNG](http://sandbox.runjs.cn/uploads/rs/404/h6qnek27/jsfiddle-iframe.PNG)

　　访问[jsFiddle](http://jsfiddle.net),在*Share*->*Embed on your page*菜单可以找到当前页面显示的js,html,css默认的`<iframe>`代码,将其插入到自己的.html或.md页面中即可。这样你就可以在自己的博客中引用他人的js,或是自己的作为demo用了演示 。

 


