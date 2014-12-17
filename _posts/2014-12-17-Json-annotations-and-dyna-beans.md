---
layout: post
title: Json data binding filters
category: 技术
tags: Jackson Java Json Data-Binding 
description: Jackson - Data Binding序列化和反序列化Java对象中filters的使用实例
---

Json字符串中有时会有很多属性，构建一个具有所有那些属性的Java POJO对象又感觉很费时。如果能够获取其中能够读的属性到一个map中会很不错。Jackson有相关的注解可以达到这个目的。在下面的example中，我们再bean中设置了两个属性，将其他属性读进一个map中。那些example也使用了Jackson中的一些常用注解，下面是部分注解的简介：

1. @JsonProperty —— 达到类似给属性添加getter或setter方法效果。换句话说，它关联一个json字段和一个Java属性。