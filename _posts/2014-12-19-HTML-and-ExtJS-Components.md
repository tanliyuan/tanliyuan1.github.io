---
layout: post
title: Json data binding filters
category: 技术
tags: Jackson Java Json Data-Binding 
description: Jackson - Data Binding序列化和反序列化Java对象中filters的使用实例
---

## renderTpl and renderData

http://skirtlesden.com/articles/html-and-extjs-components
Extjs的config选项`renderTpl`、`renderData`和`tpl`、`data`有些类似，但是前两个只在组件初始化渲染的时候运行一次。`renderTpl`和`tpl`都是用来创建组件内在标签的，但是`renderTpl`创建的元素会作为组件自己标签的一部分，而不是组件内容部分。

例如，panel中使用`tpl`，`tpl`负责渲染面板的动态内容，Panel类也有一个`renderTpl`，这个`renderTpl`用来创建面板元素的脚手架，含有header,toolbars和body。

`tpl`可能出现在类定义或是实例化的时候，然而`renderTpl`只可能出现在类定义中。