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

现在我们再在唱片集中添加一个作曲家和他们所使用的乐曲之间的Map。

```java
private Map<String,String> musicians = new HashMap<String, String>();
public Map<String, String> getMusicians() {
        return Collections.unmodifiableMap(musicians);
}
public void addMusician(String key, String value){
        musicians.put(key, value);
}
```

在`main`方法中添加：

```java
album.addMusician("Miles Davis", "Trumpet, Band leader");
album.addMusician("Julian Adderley", "Alto Saxophone");
album.addMusician("Paul Chambers", "double bass");
```

json结构如下：

```
{
  "title" : "Kind Of Blue",
  "links" : [ "link1", "link2" ],
  "songs" : [ "So What", "Flamenco Sketches", "Freddie Freeloader" ],
  "artist" : {
    "name" : "Miles Davis",
    "birthDate" : -1376027600000
  },
  "musicians" : {
    "Julian Adderley" : "Alto Saxophone",
    "Paul Chambers" : "double bass",
    "Miles Davis" : "Trumpet, Band leader"
  }
}
```

我们还可以给这个转换过程添加更多的特性，先告诉mapper按照Map中keys的自然顺序排序：

```java
mapper.configure(SerializationFeature.ORDER_MAP_ENTRIES_BY_KEYS, true);
```

这里有一个问题，细心的读者可能已经发现`birthDate`字段已经被格式化成新纪元时间，这不是我们想要到，因此还应当将时间格式化为人类更可读的形式。

```java
SimpleDateFormat outputFormat = new SimpleDateFormat("dd MMM yyyy");
mapper.setDateFormat(outputFormat);
```

默认情况下，Jackson会使用Java成员属性名作为Json字段名。你可以像本教程一样用Jackson Annotations来改变默认的命名策略。然而有时你可能无法直接访问Java Bean对象，比如那是一个第三方库，你无法在其类的源码上添加Annotations,又或者你不想Java bean和Jackson Annotation混在一起。对于这些情况，Jackson也提供了一种优雅的方法来改变默认的字段命名策略。可以在`mapper`上使用`setPropertyNamingStrategy`方法来为`field`设置命名策略。如果在`bean`中有`public`域，可以重写`nameForField`方法;如果`bean`中有`getter`方法，那么可以重写`nameForGetter`方法。在下面的example中，我们将albem的`title`域改为json的`Album-Title`字段，artist的`name`域改为json的`Artist-Name`。实现这些，只需在`main`中做如下改变：

```java

mapper.setPropertyNamingStrategy(new PropertyNamingStrategy() {
@Override
public String nameForField(MapperConfig<?> config, AnnotatedField field, String defaultName) {
   if (field.getFullName().equals("com.studytrails.json.jackson.Artist#name"))
        return "Artist-Name";
        return super.nameForField(config, field, defaultName);
}
 
@Override
public String nameForGetterMethod(MapperConfig<?> config, AnnotatedMethod method, String defaultName) {
  if (method.getAnnotated().getDeclaringClass().equals(Album.class) && defaultName.equals("title"))
        return "Album-Title";
        return super.nameForGetterMethod(config, method, defaultName);
  }
});
```

json结构如下：

```
{
  "Album-Title" : "Kind Of Blue",
  "links" : [ "link1", "link2" ],
  "songs" : [ "So What", "Flamenco Sketches", "Freddie Freeloader" ],
  "artist" : {
    "Artist-Name" : "Miles Davis",
    "birthDate" : "26 May 1926"
  },
  "musicians" : {
    "Julian Adderley" : "Alto Saxophone",
    "Miles Davis" : "Trumpet, Band leader",
    "Paul Chambers" : "double bass"
  }
}
```

现在来看一下Jackson如何处理null 域。我们为`Artist`类新增三个属性，并且不赋值初始化。新增的属性如下：

```java
public int age;
public String homeTown;
public List<String> awardsWon = new ArrayList<String>();
```

转换后的json结构如下：

```
{
"age" : 0,
 "homeTown" : null,
 "awardsWon" : [ ]
}
```

可以通过配置项来忽略空属性。

```java
mapper.setSerializationInclusion(Include.NON_EMPTY);
```

以下是本节内容的完整代码：

```java
 
import java.io.IOException;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Collections;
import java.util.Date;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
 
import com.fasterxml.jackson.annotation.JsonInclude.Include;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.PropertyNamingStrategy;
import com.fasterxml.jackson.databind.SerializationFeature;
import com.fasterxml.jackson.databind.cfg.MapperConfig;
import com.fasterxml.jackson.databind.introspect.AnnotatedField;
import com.fasterxml.jackson.databind.introspect.AnnotatedMethod;
 
public class SerializationExample {
 
    public static void main(String[] args) throws IOException, ParseException {
        ObjectMapper mapper = new ObjectMapper();
        Album album = new Album("Kind Of Blue");
        album.setLinks(new String[] { "link1", "link2" });
        List<String> songs = new ArrayList<String>();
        songs.add("So What");
        songs.add("Flamenco Sketches");
        songs.add("Freddie Freeloader");
        album.setSongs(songs);
        Artist artist = new Artist();
        artist.name = "Miles Davis";
        SimpleDateFormat format = new SimpleDateFormat("dd-MM-yyyy");
        artist.birthDate = format.parse("26-05-1926");
        album.setArtist(artist);
        album.addMusician("Miles Davis", "Trumpet, Band leader");
        album.addMusician("Julian Adderley", "Alto Saxophone");
        album.addMusician("Paul Chambers", "double bass");
        mapper.configure(SerializationFeature.INDENT_OUTPUT, true);
        mapper.configure(SerializationFeature.ORDER_MAP_ENTRIES_BY_KEYS, true);
        SimpleDateFormat outputFormat = new SimpleDateFormat("dd MMM yyyy");
        mapper.setDateFormat(outputFormat);
        mapper.setPropertyNamingStrategy(new PropertyNamingStrategy() {
            @Override
            public String nameForField(MapperConfig<?> config, AnnotatedField field, String defaultName) {
                if (field.getFullName().equals("com.studytrails.json.jackson.Artist#name"))
                    return "Artist-Name";
                return super.nameForField(config, field, defaultName);
            }
 
            @Override
            public String nameForGetterMethod(MapperConfig<?> config, AnnotatedMethod method, String defaultName) {
                if (method.getAnnotated().getDeclaringClass().equals(Album.class) && defaultName.equals("title"))
                    return "Album-Title";
                return super.nameForGetterMethod(config, method, defaultName);
            }
        });
        mapper.setSerializationInclusion(Include.NON_EMPTY);
        mapper.writeValue(System.out, album);
    }
}
 
class Album {
    private String title;
    private String[] links;
    private List<String> songs = new ArrayList<String>();
    private Artist artist;
    private Map<String , String> musicians = new HashMap<String , String>();
 
    public Album(String title) {
        this.title = title;
    }
 
    public String getTitle() {
        return title;
    }
 
    public void setLinks(String[] links) {
        this.links = links;
    }
 
    public String[] getLinks() {
        return links;
    }
 
    public void setSongs(List<String> songs) {
        this.songs = songs;
    }
 
    public List<String> getSongs() {
        return songs;
    }
 
    public void setArtist(Artist artist) {
        this.artist = artist;
    }
 
    public Artist getArtist() {
        return artist;
    }
 
    public Map<String , String> getMusicians() {
        return Collections.unmodifiableMap(musicians);
    }
 
    public void addMusician(String key, String value) {
        musicians.put(key, value);
    }
}
 
class Artist {
    public String name;
    public Date birthDate;
    public int age;
    public String homeTown;
    public List<String> awardsWon = new ArrayList<String>();
}

```

本小节我们看到了如何将Java对象序列化成json的用法，下一小节，我们将讲解如何用Jackson的tree方法构建json.
