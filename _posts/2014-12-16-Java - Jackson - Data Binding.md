---
layout: post
title: Jackson - Data Binding
category: 技术
tags: Jackson Java Json Data-Binding
description: Jackson - Data Binding序列化和反序列化Java对象方法
---



让JSON字符串从一端进入，然后Java POJOs从另一端出，或者反过来，这就是Jackson Data Binding做的事。通过例子可以很好理解，我们从一个免费的music archive网站获取json。网站提供了一个API接口以json格式返回最新的[albums列表](http://freemusicarchive.org/api/get/albums.json?api_key=60BLHNQCAOUFPIBZ&limit=2)(点击链接可查看返回的json)。我们将解析这个json字符串成一个Java对象。

下面是json转化成Java对象的步骤：

1. 第一个是创建一个Java类来存储json数据。这可以通过查看 json做到。我们创建一个`Albums`对象来持有整个json数据。json包含`dataset`元素的Array。我们创建一个`DataSet`类型的Java对象，在`Albums`对象中我们创建一个`dataset`属性，它是一个`DataSet`类型的Array。
2. 创建一个`com.fasterxml.jackson.databind.ObjectMapper`类实例。这个类将json字符串map成Java对象。

```java
com.fasterxml.jackson.databind.ObjectMapper
```

3. 我们用`ObjectMapper`的`readValue`方法来读取json。`readValue`方法有多个版本，可以有多种类型的参数，例如从一个`file`,`inputstream`，`String`或者一个`ByteArray`中读取，还可以从网络中读取，也就是我们使用的传递URL作为参数的版本。
4. `ObjectMapper`缓存序列化和反序列化器，以便多次转换可以重用一个`ObjectMapper`实例。
5. 如果你给`readValue`传递的参数是一个`InputStream`,处于性能考虑，不要用`InputStreamReader`包裹`InputStream`。

## Data Binding Example

```java
import java.io.IOException;
import java.net.MalformedURLException;
import java.net.URL;
 
import com.fasterxml.jackson.core.JsonParseException;
import com.fasterxml.jackson.databind.DeserializationFeature;
import com.fasterxml.jackson.databind.JsonMappingException;
import com.fasterxml.jackson.databind.ObjectMapper;
 
public class DataBinding1 {
    public static void main(String[] args) throws JsonParseException, JsonMappingException, MalformedURLException, IOException {
        String url = "http://freemusicarchive.org/api/get/albums.json?api_key=60BLHNQCAOUFPIBZ&limit=2";
        ObjectMapper mapper = new ObjectMapper();
        mapper.disable(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);
        Albums albums = mapper.readValue(new URL(url), Albums.class);
        Dataset[] datasets = albums.getDataset();
        for (Dataset dataset : datasets) {
            System.out.println(dataset.getAlbum_title());
        }
    }
 
}
```

这里我们禁用了如果遇到未知属性，会引起mapper中断解析的特性。这样的话，如果json中有10个属性，而我们在Java Bean中仅仅定义了2个，那么其余的8个将被忽略。

The Albums class

```java
public class Albums {
 
    private String title;
    private Dataset[] dataset;
 
    public void setTitle(String title) {
        this.title = title;
    }
 
    public void setDataset(Dataset[] dataset) {
        this.dataset = dataset;
    }
 
    public String getTitle() {
        return title;
    }
 
    public Dataset[] getDataset() {
        return dataset;
    }
}
```

Dataset class

```java
import java.util.HashMap;
import java.util.Map;
 
public class Dataset {
    private String album_id;
    private String album_title;
    private Map<String , Object> otherProperties = new HashMap<String , Object>();
 
    public String getAlbum_id() {
        return album_id;
    }
 
    public void setAlbum_id(String album_id) {
        this.album_id = album_id;
    }
 
    public String getAlbum_title() {
        return album_title;
    }
 
    public void setAlbum_title(String album_title) {
        this.album_title = album_title;
    }
 
    public Object get(String name) {
        return otherProperties.get(name);
    }
 
}
```
