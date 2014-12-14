---
layout: post
title: Jackson Mix-In Annotations使用示例
category: 技术
tags: Jackson Java-Json
description: 利用Jackson Mix-In注解可以将注解与POJO类声明分离，适用于序列化无法在源码中加Annotation的第三方类库对象。
---

　　在Jackson中Annotations是一个很棒的的方法，用于管理序列化和反序列化。然而，如果你想注解一个第三方类，这个时候你没有源码，你无法在想序列化的类中添加Annotation，或者你不想你的POJOs与Jackson Annotations紧密结合，那么其他的注解方式就无能为力了，这时Mix-In 注解就派上用场了。你只需定义一个mix-in抽象类，它是你想添加注解的实际类的代理，这样注解就可以添加到这个代理类里面。

下面为大家演示Mix-In的用法：

## Example：

Bird.java

```java
package com.jackson.test;

public class Bird {
	private String name;
    private String sound;
    private String habitat;
 
    public Bird(String name) {
        this.name = name;
    }
 
    public String getName() {
        return name;
    }
 
    public String getSound() {
        return sound;
    }
 
    public String getHabitat() {
        return habitat;
    }
 
    public void setSound(String sound) {
        this.sound = sound;
    }
 
    public void setHabitat(String habitat) {
        this.habitat = habitat;
    }
 
    @Override
    public String toString() {
        return "Bird [name=" + name + ", sound=" + sound + ", habitat=" + habitat + "]";
    }
}

```

BirdMixIn.java

```java
package com.jackson.test;

import com.fasterxml.jackson.annotation.JsonIgnore;
import com.fasterxml.jackson.annotation.JsonIgnoreProperties;
import com.fasterxml.jackson.annotation.JsonProperty;
import com.fasterxml.jackson.annotation.JsonView;

//@JsonIgnoreProperties({"sound"})
public abstract class BirdMixIn {
	@JsonView(FilterView.OutputA.class)
	@JsonProperty("sound")
	 String sound;
//	abstract String getSound();
/*	BirdMixIn(@JsonProperty("name") String name){
		
	};
	
//	@JsonView(FilterView.OutputA.class)
	@JsonProperty("sound")
	abstract String getSound();
	
//	@JsonProperty("habitat")
//	abstract String getHabitat();
*/}

```

testMix.java

```java
package com.jackson.test;

import java.util.ArrayList;
import java.util.List;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.MapperFeature;
import com.fasterxml.jackson.databind.ObjectMapper;

public class testMix {

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		ObjectMapper mapper = new ObjectMapper();
		mapper.addMixInAnnotations(Bird.class, BirdMixIn.class);
		List<Bird> birds = new ArrayList<Bird>();
		Bird bird1 = new Bird("sdfd");
		bird1.setSound("dfsa");
		bird1.setHabitat("red");
		birds.add(bird1);
		
		Bird bird2 = new Bird("scarlet ibis");
		bird2.setSound("ee");
		bird2.setHabitat("water");
		birds.add(bird2);
		
		try {
		    mapper.configure(MapperFeature.DEFAULT_VIEW_INCLUSION, false);
		    String json =	mapper.writerWithView(FilterView.OutputA.class).writeValueAsString(birds);
		    System.out.println(json);
		} catch (JsonProcessingException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}

}

class FilterView {  
    static class OutputA {}  
    static class OutputB {}  
} 

```

### Output

```
[{"sound":"dfsa"},{"sound":"ee"}]
```

在`Bird`中我们定义了三个private的成员变量，但是序列化时只输出了`sound`属性,在Bird类中也没有加任何的Jackson Annotation。这常用于服务器端向前端传某个对象的概略信息，比如一个对象有好几十个属性，而且很多属性的值是一个文本类型且很长，但是前端只要其中的几个属性，比如ID，topic等等。那么把整个对象序列化成json，就增大的数据传输量。解决方案有两个。

- 方案一 —— 在POJOs中添加`JsonIgnore`忽视掉不需要的属性。
这里有两个问题：一、好几十个属性中我们只要四五个，那么我们得在其余几十个属性上加`JsonIgnore`，工作量太大。二、序列化对象是第三方类库的实例，我们无法在源码中添加Annotation，或者我们想保持代码简洁性，将Jackson Annotation与类声明分离。
针对第一个问题我们可以使用`JsonView`注解，它的作用与`JsonIgnore`相反，被`JsonView`注解注解的将被序列化，那么只需在少数几个需要的属性上添加。第二个问题其他Annotation就解决不了了。

- 方案二 —— 就是采用前面的 