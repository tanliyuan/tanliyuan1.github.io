---
layout: post
title: Jackson转换Java对象中首字母大写的成员变量成json字符串时出现重复
category: 技术
tags: Jackson Java-Json
description: Jackson可以将Java对象转换成json字符串，然后用于前后台数据交互，在使用过程中发现当Java成员变量首字母是大写时，例如Name="me",json中会出现name和Name两个字段，造成数据冗余
---

Jackson是Java语言处理数据的一个工具集，包括一流的JSON解析功能，另外还有模块支持`Avro`,`CBOR`,`CSV`,`Smile`,`XML`或者`YAML`格式数据的处理,所支持的数据格式还在不断增加。Jackson 2.x版本官方主页[https://github.com/FasterXML/jackson](https://github.com/FasterXML/jackson)。
目前比较流行的json解析工具类库有Jackson,JSON-lib,Gson,在性能方面[Jackson](https://github.com/FasterXML/jackson)>[Gson](http://code.google.com/p/google-gson/)>[Json-lib](http://json-lib.sourceforge.net/).
测试结果参考：

- [两款JSON类库Jackson与JSON-lib的性能对比(新增第三款测试)](http://wangym.iteye.com/blog/738933)
- [java 三大json解析性能对比(json-lib，gson，jackson)](http://blog.chinaunix.net/uid-26209648-id-3889935.html)

```java
    class Person {
        String name;
        int age;
    }
```

会报异常，因为没有getter,setter方法，之后分别测试有无@JsonProperty("Three")的情况

```java
    class Person {
        String name;
        String FirstName;
        public firstName;
        String LastName;
        public lastName;
        public IDCard;
    }
```

有public声明的成员变量，没有getter，setter方法也不会报异常

```java

    static class test {    
    	String one;    	
    	String two;    	
    	@JsonProperty("Three")
        public	String Three;
        public String FOur;
    	public String FOIuErParties; 
    	   	
		public String getThree() {
			return Three;
		}
		
		public void setThree(String three) {
			Three = three;
		}
		
		public String getFOIuErParties() {
			return FOIuErParties;
		}
		
		public void setFOIuErParties(String fOIuErParties) {
			FOIuErParties = fOIuErParties;
		}

		public String getOne() {
			return one;
		}

		public void setOne(String one) {
			this.one = one;
		}

		public String getTwo() {
			return two;
		}

		public void setTwo(String two) {
			this.two = two;
		}

        /*		
        public String getThree() {
			return Three;
		}

		public void setThree(String three) {
			Three = three;
		}
        */
		test(String one,String two, String Three) {
    		this.one = one;
    		this.two = two;
    		this.Three = Three;
    	}
    }
```

　　可以使用`PropertyNamingStrategy.LowerCaseWithUnderscoresStrategy`类来定义Java对象成员变量名转换成json字段名的转换规则。
使用起来很简单，代码如下：

<pre>

    ObjectMapper mapper = new ObjectMapper();
    	mapper.setPropertyNamingStrategy(new PropertyNamingStrategy.LowerCaseWithUnderscoresStrategy());
		try {
			json = mapper.writeValueAsString(formDataTpls);
			//增加total，success,msg,code字段
			
		} catch(JsonProcessingException e) {
			e.printStackTrace();
		}


    ObjectMapper mapper = new ObjectMapper();
        	//mapper.setPropertyNamingStrategy(new PropertyNamingStrategy.LowerCaseWithUnderscoresStrategy());
        	mapper.setVisibility(PropertyAccessor.GETTER, JsonAutoDetect.Visibility.NONE); // but only public getters
        	//mapper.setVisibility(JsonMethod.FIELD, Visibility.ANY);  JsonMethod枚举值已经废弃，改用PropertyAccessor
        	//mapper.setVisibilityChecker(mapper.getVisibilityChecker().with(JsonAutoDetect.Visibility.NONE));
    		try {
    			json = mapper.writeValueAsString(formDataTpls);
    			//增加total，success,msg,code字段
    			
    		} catch(JsonProcessingException e) {
    			e.printStackTrace();
    		}
        	return json;
</pre>

 User user = new User();
 //...set user data
 
 ObjectMapper mapper = new ObjectMapper();
 System.out.println(mapper.writeValueAsString(user));

## output
 {"age":29,"messages":["msg 1","msg 2","msg 3"],"name":"mkyong"}
 
 ObjectMapper mapper = new ObjectMapper();
   System.out.println(mapper.writerWithDefaultPrettyPrinter().writeValueAsString(user));
   
## Output
   
   {
     "age" : 29,
     "messages" : [ "msg 1", "msg 2", "msg 3" ],
     "name" : "mkyong"
   }
   
   	//mapper.setPropertyNamingStrategy(new PropertyNamingStrategy.LowerCaseWithUnderscoresStrategy());
   	
   	下划线格式命名