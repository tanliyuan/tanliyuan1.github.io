---
layout: post
title: Jackson转换Java对象中首字母大写的成员变量成json字符串时出现重复
category: 技术
tags: Jackson,json
description: Jackson可以将Java对象转换成json字符串，然后用于前后台数据交互，在使用过程中发现当Java成员变量首字母是大写时，例如Name="me",json中会出现name和Name两个字段，造成数据冗余
---

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

</pre>