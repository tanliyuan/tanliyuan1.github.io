---
layout: post
title: JSDuck实现ExtJs docs代码实时预览
category: 技术
tags: JSDuck,ExtJs docs,live preview
description: 用JSDuck给自己的ExtJs项目增加ExtJs官网中docs一样的实时预览功能
---

对于演示一些可视化组件，live preview是很有用的，代码是相对独立的，并能渲染这个组件到document body 中。

先来看一下ExtJs docs官网的代码实时预览效果：

![live_preview.gif]()

## 如何用JSDuck生成代码live preview的效果：

- 要在docs中生成语法高亮的代码块，必须在“/**  */”中代码缩进4个空格，如果还想具有live preview的效果 ，那么得在代码块前面加上@example标签，同时该标签也要缩进4个空格。
```javascript
/**
* See the example:
*
*     @example
*     Ext.create('Ext.Button', {
*         text: 'Click me',
*         renderTo: Ext.getBody()
*     });
*/

```
上述操作后只是有live preview的界面出现，但是点击live preview的时候并不能预览。

- 要想让内联的example代码在文档框架中工作，你必须在JSDuck的输出目录中建立一个extjs-build/的目录，在运行JSDuck生成docs文档后，只需拷贝ExtJs SDK（也就是我们创建extjs应用时所要引用的目录和文件，如extjs-4.2.1/resource目录和ext-all.js,ext-lang-zh_CN.js文件）进extjs-build目录即可。我的输出目录为docs,就会有如下docs/extjs-build/resource目录和docs/extjs-build/ext-all.js等文件。
若果输出目录中没有extjs-build目录时，点击live preview按钮会出现 “ReferenceError: Ext is not defined”。

- 需将文档部署在web 服务器（如tomcat等）下访问，live preview才能正常预览，否则控制台会出现如下错误：
`“Uncaught SecurityError: Blocked a frame with origin "null" from accessing a frame with origin "null". Protocols, domains, and ports must match.”`
