---
layout: post
title: JSDuck实现ExtJs docs代码实时预览
category: 技术
tags: JSDuck,ExtJs docs,live preview
description: 用JSDuck给自己的ExtJs项目增加ExtJs官网中docs一样的实时预览功能
---

　　对于演示一些可视化组件，live preview是很有用的，代码是相对独立的，并能渲染这个组件到document body 中。

　　先来看一下ExtJs docs官网的代码实时预览效果：

![doc-live-preview.gif](../../../public/img/doc-live-preview.gif)

## 如何用JSDuck生成代码live preview的效果：

- 要在docs中生成语法高亮的代码块，必须在“/**  */”中代码缩进4个空格，如果还想具有live preview的效果 ，那么得在代码块前面加上@example标签，同时该标签也要缩进4个空格。
  
　　下面这段代码是`ext-4.2.1.883\src\window\Window.js`中的一段文档标签注解，经过JSDuck的处理就可以生成上面那副截图的文档格式。

```javascript
/**
 * 
 * **As with all {@link Ext.container.Container Container}s, it is important to consider how you want the Window to size
 * and arrange any child Components. Choose an appropriate {@link #layout} configuration which lays out child Components
 * in the required manner.**
 *
 *     @example  
 *     Ext.create('Ext.window.Window', {  
 *         title: 'Hello',  
 *         height: 200,  
 *         width: 400,  
 *         layout: 'fit',
 *         items: {  // Let's put an empty grid in just to illustrate fit layout
 *             xtype: 'grid',
 *             border: false,
 *             columns: [{header: 'World'}],                 // One header just for show. There's no data,
 *             store: Ext.create('Ext.data.ArrayStore', {}) // A dummy empty data store
 *         }
 *     }).show();
 */
```

　　上述操作后只是有live preview的界面出现，但是点击live preview的时候并不能预览。

- 要想让内联的example代码在文档框架中工作，你必须在JSDuck的输出目录中建立一个`extjs-build/`的目录，在运行JSDuck生成docs文档后，只需拷贝ExtJs SDK（也就是我们创建extjs应用时所要引用的目录和文件，如`extjs-4.2.1/resource`目录和`ext-all.js,ext-lang-zh_CN.js`文件）进extjs-build目录即可。我的输出目录为docs,就会有如下`docs/extjs-build/resource`目录和`docs/extjs-build/ext-all.js`等文件。
若果输出目录中没有extjs-build目录时，点击live preview按钮会出现 `ReferenceError: Ext is not defined`。

- 需将文档部署在web 服务器（如tomcat等）下访问，live preview才能正常预览，否则控制台会出现如下错误，原因是浏览器禁止跨域访问，直接点击index.html文件时用的是file协议，而非http协议，从浏览器的地址栏可以看出。
`Uncaught SecurityError: Blocked a frame with origin "null" from accessing a frame with origin "null". Protocols, domains, and ports must match.`
