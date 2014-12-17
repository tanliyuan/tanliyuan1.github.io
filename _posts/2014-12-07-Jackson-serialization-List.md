---
layout: post
title: Jackson 序列化Java List 对象
category: 技术
tags: Jackson Java-Json
description: Jackson 序列化Java List 对象是保留List中对象的类型信息，以便正确的反序列化。
---

　　本节讲解如何将Java List序列化成JSON，序列化和反序列化`lists`时，默认是不会保存类型信息的。下面演示两个例子，第一个example中，我们序列化一个包含Java List属性的对象；第二个例子中我们直接序列化Java List，这两个example中我们用Jackson `Annotation`在序列化时保留了类型信息。

## Example 1: 序列化包含List的对象

　　这个example转换`Zoo`类成json。 `Zoo`类包含动物园的name,所在city,和圈养的animals List。List中的数据类型是`Animal`，例如，list中包含的动物是抽象类`Animal`的子类。先看下序列化Zoo类时的结果。首先我们创建`Zoo`类实例，注意构造器的编码。当我们尝试从Json反序列化得到`Zoo`对象时，Jackson必须知道构建`Zoo`对象时应该用接收`name`和`city`属性的构造器方法。

```java

class Zoo {
    public String name;
    public String city;
 
     
    @JsonCreator
    public Zoo(@JsonProperty("name") String name,@JsonProperty("city") String city) {
        this.name = name;
        this.city = city;
    }
 
    public List<Animal> animals = new ArrayList<Animal>();
 
    public List<Animal> addAnimal(Animal animal) {
        animals.add(animal);
        return animals;
    }
 
}

```

　　`Animal` 是一个抽象类，用于扩展创建`Elephant`和`Lion`类。

```java


class Elephant extends Animal {
 
    @JsonCreator
    public Elephant(@JsonProperty("name") String name) {
        super.name = name;
    }
 
    @Override
    public String toString() {
        return "Elephant : " + name;
    }
}
 
class Lion extends Animal {
    @JsonCreator
    public Lion(@JsonProperty("name") String name) {
        this.name = name;
    }
 
    @Override
    public String toString() {
        return "Lion: " + name;
    }
}
```

　　我们用`ObjectMapper`类执行序列化并将转换得到的json打印到控制台。当然你要可以用
`mapper.writeValue(new File('zoo.json'), zoo)`输出到zoo.json文件中。

```java
package com.test.jackson;
 
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
 
import com.fasterxml.jackson.core.JsonGenerationException;
import com.fasterxml.jackson.databind.JsonMappingException;
import com.fasterxml.jackson.databind.ObjectMapper;
 
public class SerializeZoo {
    public static void main(String[] args) throws JsonGenerationException, JsonMappingException, IOException {
         Zoo zoo = new Zoo("London Zoo", "London");
         Lion lion = new Lion("Simba");
         Elephant elephant = new Elephant("Manny");
         zoo.addAnimal(elephant).add(lion);
         ObjectMapper mapper = new ObjectMapper();
         mapper.writeValue(System.out, zoo);
    }
}
```

### Output

```
{"name":"London Zoo","city":"London","animals":[{"name":"Manny"},{"name":"Simba"}]}
```

　　现在试试用得到的json字符串反序列化。

```java

import java.io.IOException;
 
import com.fasterxml.jackson.core.JsonParseException;
import com.fasterxml.jackson.databind.JsonMappingException;
import com.fasterxml.jackson.databind.ObjectMapper;
 
public class DeserializeZoo {
 
    public static void main(String[] args) throws JsonParseException, JsonMappingException, IOException {
        String json = "{\"name\":\"London Zoo\",\"city\":\"London\"," + "\"animals\":[{\"name\":\"Manny\"},{\"name\":\"Simba\"}]}";
        ObjectMapper mapper = new ObjectMapper();
        mapper.readValue(json, Zoo.class);
    }
}
```

### Output

```
Can not construct instance of com.studytrails.json.jackson.Animal, problem: abstract types either need to be mapped to concrete types, have custom deserializer, or be instantiated with additional type information
```

　　我们得到一个error，这也是我们所期待的，因为序列化得到的json中没有保存List中对象的类型信息。想想如何解决，我们需要将对象的类型信息存储起来，我们只需做两件事。

1. 我们需要告诉Jackson保留类信息

```
@JsonTypeInfo(use = JsonTypeInfo.Id.CLASS, include = As.PROPERTY, property = "@class")
```
2. 告诉Jackson，Animal有子类Elephant和Lion类

```
@JsonSubTypes({ @Type(value = Lion.class, name = "lion"), @Type(value = Elephant.class, name = "elephant") })
```

### Output

```
{"name":"London Zoo","city":"London",
"animals":[{"@class":"com.studytrails.json.jackson.Elephant","name":"Manny"},
           {"@class":"com.studytrails.json.jackson.Lion","name":"Simba"}]}
```

　　现在看起来好多了，你也可以用Zoo类来数据绑定json,将其反序列化成Zoo对象，一切都表现的很好，Zoo对象有一个动物列表。

## Example 2： 直接序列化List

　　在上面的example中，我们展示了如何序列化一个有List属性的类，这个例子中，我们将演示直接序列化List对象。继续沿用上面的类，看看当我们尝试直接序列化List对象时的结果，此时我们已经添加了同上个example的Jackson类型信息注解。

```java
public class SerializeAnimals {
    public static void main(String[] args) throws JsonGenerationException, JsonMappingException, IOException {
        List<animal> animals = new ArrayList<animal>();
        Lion lion = new Lion("Samba");
        Elephant elephant = new Elephant("Manny");
        animals.add(lion);
        animals.add(elephant);
        ObjectMapper mapper = new ObjectMapper();
 
        mapper.writeValue(System.out, animals);
    }
}  
```

### Output

```
[{"name":"Samba"},{"name":"Manny"}]
```

　　尽然没有包含类型信息！为了在直接序列化List时保留类型信息，你需要以这样配置mapper:

```
mapper.writerWithType(new TypeReference<List
        <Animal>>() {
        }).writeValue(System.out, animals);
```
　　现在就能输出期待的json:


```
[{"@class":"com.studytrails.json.jackson.Lion","name":"Samba"},
{"@class":"com.studytrails.json.jackson.Elephant","name":"Manny"}]
```
