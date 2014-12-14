---
layout: post
title: Jackson - Java序列化
category: 技术
tags: Jackson Java-Json
description: SwitchySharp和自由门结合使用，轻轻松松实现翻墙，告别繁琐配置。
---

本文讲述如何创建一个JSON结构字符串。我们有三种方式创建JSON：

1. Java - 最常用的一种方式是将Java对象序列化成JSON。
2. Tree representation - 我们先创建一个 `com.fasterxml.jackson.databind.JsonNode`（Jackson 2.x版本用这个包）对象树，然后将这棵树转换成JSON。
3. Stream generator - 这种方法我们要用`Stream generator`构建一个json流然后将其转换成一个JSON结构。

## Java对象转JSON

Jackson提供了Java对象序列化成JSON和JSON反序列化成Java对象的类。本示例将演示将Java对象序列化成JSON结构。我们将从一个简单的类开始，然后逐步增加复杂性。假设我们有一个音乐公司，想发布一个API供用户来查询唱片集。我们先建一个只有`title`属性的`Album`类。

```java
class Album {
    private String title;
 
    public Album(String title) {
        this.title = title;
    }
     
    public String getTitle() {
        return title;
    }
}
```

我们用`ObjectMapper`类将对象转换成JSON。默认情况下，Jackson会用`BeanSerializer` 类序列化POJO（简单Java对象）。
>    记住Java Bean对象中`private`修饰的属性要有`getter`方法，或者该属性被`public`修饰，可以没有`getter`方法。如果对象中只有`private`的属性且没有对应的`getter`方法，Jackson将报异常，但是如果对象中既有`private`、`public`修饰的属性，那么`private`属性没有`getter`方法也不会报异常，但是如果没有其他Jackson注解，该属性序列化时将不可见，也即JSON中将没有该字段。

转换的JSON结构如下：

```
{"title":"Kind Of Blue"}
```

让我们给这个唱片集增加一组链接，当然还有相应的`getter`、`setter`方法，方便链接到唱片发布会或是评论。

```java
private String[] links;
```

在`main`方法中增加如下语句：

```java
album.setLinks(new String[] { "link1", "link2" });
```

现在的JSON结构如下，注意Java `Array`被转换成被`[`、`]`包围JSON数组。

```
{"title":"Kind Of Blue","links":["link1","link2"]}
```

现在我们给Album增加一个歌曲`List`.

```java
List<String> Songs = new ArrayList<String>();
```

在`main`方法中给`Album`对象增加歌曲`List`.

```java
List<String> songs = new ArrayList<String>();
songs.add("So What");
songs.add("Flamenco Sketches");
songs.add("Freddie Freeloader");
album.setSongs(songs);
```

转换的结构如下，注意`List`对象和上面的`Array`对象一样也被转换成json 数组。

```
{"title":"Kind Of Blue","links":["link1","link2"],
"songs":["So What","Flamenco Sketches","Freddie Freeloader"]}
```

接下来，我们将给唱片集增加一个艺术家属性。艺术家类是一个包含姓名和`Date`实例表示的出生日期的类。

```java
class Artist {
    public String name;
    public Date birthDate;
}
```

在`main`方法中给唱片集增加一个艺术家。

```java
Artist artist = new Artist();
artist.name = "Miles Davis";
SimpleDateFormat format = new SimpleDateFormat("dd-MM-yyyy");
artist.birthDate = format.parse("26-05-1926");
album.setArtist(artist);
```

Json结构如下：

```
{"title":"Kind Of Blue","links":["link1","link2"],
"songs":["So What","Flamenco Sketches","Freddie Freeloader"],
"artist":{"name":"Miles Davis","birthDate":-1376027600000}}
```

为了使Jackson转换出来的JSON字符串更具可读性，我们增加了一行代码。

```java
mapper.configure(SerializationFeature.INDENT_OUTPUT, true);
```

格式化后的JSON结构如下：

```
{
  "title" : "Kind Of Blue",
  "links" : [ "link1" , "link2" ],
  "songs" : [ "So What", "Flamenco Sketches", "Freddie Freeloader" ],
  "artist" : {
    "name" : "Miles Davis",
    "birthDate" : -1376027600000
  }
}
```

>   注意这个不应该用于实际发布的时候，仅仅用于开发和测试阶段，因为为了达到换行和缩进的效果，会在每行的头尾部增加`\r\n`、`\t`等字符，这会显著增加数据传输量。

