---
title: struts获取正文
date: 2017-04-24 10:28:18
categories:
- javaee
tags:
- struts2
- javaee
---
## 静态获取正文(写死)
```java
  private String name;
	private int age;

	public String addUser() {
		System.out.println(name + "---" + age);
		return SUCCESS;
	}
  //get and set..
```
<!-- more -->
在配置action中添加
```xml
<action name="adduser" class="com.eason.DynamicTest" method="addUser">
			<param name="name">张三</param> 名称必须相同
			<param name="age">13</param>
			<result>/success.jsp</result>
</action>
```
## 动态获取正文
* java类同上时 属性为成员变量时 直接访问即可,属性名称要相同http://localhost:8080/0424/adduser1?name=li&age=1
* javabean 分出时 生成单独的domain包 访问
  ```java
  public class User {
  	private String name;
  	private int age;
    //set and get
  }
  ```

  ```java
  private User user;

	public User getUser() {
		return user;
	}

	public void setUser(User user) {
		this.user = user;
	}
  ```
访问时需要这样http://localhost:8080/0424/adduser2?user.name=li&user.age=1
* javabean 分出时 实现ModelDriven 接口 此时javabean必须初始化
```java
  private User user=new User();
  @Override
	public User getModel() {
		// TODO Auto-generated method stub
		return user;
	}
```
访问时http://localhost:8080/0424/adduser3?name=13&age=1即可
