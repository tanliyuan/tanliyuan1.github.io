---
layout: post
title: 一分钟轻松实现Chrome浏览器下翻墙
category: 技术
tags: 自由门 SwitchySharp 翻墙
description: SwitchySharp和自由门结合使用，轻轻松松实现翻墙，告别繁琐配置。
---

　　网上看到很多关于翻墙的教程，但是配置过程及其费时繁琐，而且有些还不稳定，使用起来也不方便。下面的是本人一直在使用的翻墙方法，，小白式操作，分分钟就能配置好。

准备材料：

- [SwitchySharp.crx](http://pan.baidu.com/s/1hqtdgfa)   Chrome浏览器下的SwitchySharp插件
- [SwitchyOptions.bak](http://pan.baidu.com/s/1qWnxhLU)     SwitchySharp的代理规则配置文件，里面已经有很多常用的网站配置
- [自由门](http://pan.baidu.com/s/1qW9HPFY)        也可以单独使用自由门而不安装SwitchySharp实现翻墙，但是效果不好

材料准备就绪后，按照下面步骤，可以快速实现翻墙梦。

1. 打开Chrome浏览器，点击`设置`->`扩展程序`,将下载的`SwitchySharp.crx`拖拽到`扩展程序`面板放开及完成SwitchySharp插件的安装，效果如下：
   ![plugins.gif](../../../public/img/plugins.gif)
2. SwitchySharp插件的安装成功后浏览器地址栏右侧会有SwitchySharp地球状的图标。点击图标，选择`选项`进入设置界面，如下图。
    ![switchyIco.PNG](../../../public/img/switchyIco.PNG)  
    2.1 先将下载的SwitchySharp的配置文件`SwitchyOptions.bak`导入。    
    ![SwitchyOptions.bak](../../../public/img/bak.PNG)
    2.2 设置代理的端口号.   
   ![switch_port1.PNG](../../../public/img/switch_port1.PNG)
   ![switch_port2.PNG](../../../public/img/switch_port2.PNG) 
    2.3 自定义规则列表，刚开始无需修改，之后发现某个国外网站无法访问，在这里`新建规则`，记得`情景模式`选择`GoAgent`.    
   ![switch_rule.PNG](../../../public/img/switch_rule.PNG)
3. 配置完SwitchySharp，到`自由门`的解压目录，双击`fg742.exe`,等到服务器面板显示成功连接时，即可在浏览器中输入[https://www.google.com.hk/](https://www.google.com.hk/)进行测试。如果顺利的话，将能打开界面。
    ![freeDoor.PNG](../../../public/img/freeDoor.PNG)
    ![freeDoor_setting.PNG](../../../public/img/freeDoor_setting.PNG)
4. 之后遇到无法打开的网页，可以打开SwitchySharp的配置界面（点击浏览器右侧图标->`选项`），进入`切换规则`面板，点击`新建规则`，模仿已有的规则模式输入即可，例如要添加：`https://www.google.com.hk/`进入规则列表，匹配规则列表中的URL都将通过自由门代理访问，`URL模式`一栏填入`*://*.google.com.*/*`即可。最后记得点击`保存`，再次刷新界面即可访问。有时无法访问或者暂时不想使用自由门代理的时候，进入自由门的`内容`选项卡，点击`暂停使用`即可，再次点击即可恢复使用代理。

　　现在整个翻墙的配置就已经完成了，如果你经常要翻墙访问，那么把自由门设置成开机自动启动，并缩放到系统托盘是一个不错的选择，这样在上网过程中完全感觉不到在使用翻墙代理，可以得到很好的体验。