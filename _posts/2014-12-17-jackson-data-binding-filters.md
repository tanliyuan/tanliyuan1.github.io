---
layout: post
title: Json data binding filters
category: 技术
tags: Jackson Java Json Data-Binding 
description: Jackson - Data Binding序列化和反序列化Java对象中filters的使用实例
---

Jackson提供了一个高效的方法将json和Java POJOs对象绑定起来。然而，有时，在将json转换成Java对象或是将Java对象转成json时，我们并不需要POJOs中的所有属性，而是想忽略其中某些属性。Jackson提供了三种方式来过滤属性。

1. @JsonIgnoreProperties —— 这个注解作用在类上来忽略json属性。在下面的example中，我们忽视albums中dataset的`tags`属性。
2. @JsonIgnore —— 这个注解作用在属性上来忽视某些属性。
3. 出来上面的注解方式，你还可以自定义过滤方式。

下面的example演示了方法1和方法2的用法。注意`@JsonAutoDetect`注解的使用。

Data Binding Filters Example

```java
import java.io.IOException;
import java.net.MalformedURLException;
import java.net.URL;
 
import com.fasterxml.jackson.core.JsonParseException;
import com.fasterxml.jackson.databind.DeserializationFeature;
import com.fasterxml.jackson.databind.JsonMappingException;
import com.fasterxml.jackson.databind.ObjectMapper;
 
public class DataBindingFilter {
    public static void main(String[] args) throws JsonParseException, JsonMappingException, MalformedURLException, IOException {
        String url = "http://freemusicarchive.org/api/get/albums.json?api_key=60BLHNQCAOUFPIBZ&limit=2";
        ObjectMapper mapper = new ObjectMapper();
        mapper.disable(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);
        AlbumsFilter albums = mapper.readValue(new URL(url), AlbumsFilter.class);
        System.out.println(albums.getTotal_pages());
        System.out.println(albums.getTitle());
        for (DatasetFilter dataset : albums.getDatasetFilter()) {
            System.out.println(dataset.getAlbum_comments());
            System.out.println(dataset.get("album_images"));
            System.out.println(dataset.get("tags"));
            System.out.println(dataset.get("album_listens"));
            break;
        }
    }
 
}
```

The AlbumsFilter class

```java
 
import com.fasterxml.jackson.annotation.JsonAutoDetect;
import com.fasterxml.jackson.annotation.JsonAutoDetect.Visibility;
import com.fasterxml.jackson.annotation.JsonProperty;
 
// Do not use fields to autodetect. use the public getter methods to autodetect properties
@JsonAutoDetect(fieldVisibility = Visibility.NONE, getterVisibility = Visibility.PUBLIC_ONLY)
public class AlbumsFilter {
 
    private String title;
    private DatasetFilter[] datasetFilter;
    public String total_pages;
 
    protected String getTotal_pages() {
        return total_pages;
    }
 
    public String getTitle() {
        return title;
    }
 
    // this getter method is for the 'dataset' property
    @JsonProperty("dataset")
    public DatasetFilter[] getDatasetFilter() {
        return datasetFilter;
    }
}
```

DatasetFilter class

```java
import java.util.HashMap;
import java.util.Map;
 
import com.fasterxml.jackson.annotation.JsonAnyGetter;
import com.fasterxml.jackson.annotation.JsonAnySetter;
import com.fasterxml.jackson.annotation.JsonCreator;
import com.fasterxml.jackson.annotation.JsonIgnore;
import com.fasterxml.jackson.annotation.JsonIgnoreProperties;
import com.fasterxml.jackson.annotation.JsonProperty;
 
// ignore the property with name 'tags'.
@JsonIgnoreProperties({ "tags" })
public class DatasetFilter {
    private String album_id;
    private String album_title;
    private Map<String , Object> otherProperties = new HashMap<String , Object>();
    private String album_comments;
 
    @JsonCreator
    public DatasetFilter(@JsonProperty("album_id") String album_id, @JsonProperty("album_title") String album_title) {
        this.album_id = album_id;
        this.album_title = album_title;
    }
 
    // ignore the property specified by this getter.
    @JsonIgnore
    public String getAlbum_comments() {
        return album_comments;
    }
 
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
 
    // this method is used to get all properties not specified earlier.
    @JsonAnyGetter
    public Map<String , Object> any() {
        return otherProperties;
    }
 
    @JsonAnySetter
    public void set(String name, Object value) {
        otherProperties.put(name, value);
    }
}
```
