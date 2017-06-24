---
title: struts2中返回json数据
date: 2017-05-10 22:01:46
categories:
- javaee
tags:
- struts2
- javaee
---
## 导入struts2-json-pligin
```xml
<dependency>
			<groupId>org.apache.struts</groupId>
			<artifactId>struts2-json-plugin</artifactId>
			<version>2.3.14</version>
</dependency>
```
<!-- more -->
## 在Action中
```java
public class Login extends ActionSupport{

	private static final long serialVersionUID = 6792184906021258229L;
	private Map<String, Object> data;

	@Override
	public String execute() throws Exception {
		// TODO Auto-generated method stub
		Map<String, Object> map = new HashMap<String, Object>();
		map.put("li", "liu");
		map.put("dong", "yue");
		this.setData(map);
		return SUCCESS;
	}

	public Map<String, Object> getData() {
		return data;
	}

	public void setData(Map<String, Object> data) {
		this.data = data;
	}

}
```
## 在struts.xml中 继承自json-default
```xml
<package name="default" extends="json-default">
		<action name="login" class="eason.test.maven.Login">
			<result type="json"> 返回值json
				<param name="root">data</param> 不写这个有问题 data为json最外围map的名称
			</result>
		</action>
</package>
```
