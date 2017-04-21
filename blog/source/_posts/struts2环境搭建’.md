---
title: '''struts2环境搭建’'
date: 2017-04-21 15:55:11
categories:
- javaee
tags:
- struts2
- javaee
---
## Struts2 环境搭建
### 下载jar包
[Struts2官网](https://struts.apache.org/)中下载最新jar包 当前版本2.5.10
其中关键性jar包为
* asm-5.1.jar 操作字节码 基于子类的动态代理
* asm-commons-5.1.jar
* asm-tree-5.1.jar
* commons-fileupload-1.3.2.jar  io操作的jar
* commons-io-2.4.jar
* commons-lang3-3.4.jar   对java.lang的扩展
* commons-logging-1.1.3.jar 对日志的扩展
* freemaker-2.3.23.jar 对页面模板的扩展
* javassist-3.20.0-GA.jar  对字节码的支持
* log4j-api-2.7.jar 对日志的扩展
* ognl-3.1.12.jar OGNL表达式
* struts2-core-2.5.10.1.jar 核心库（新版包含xwork无需添加xwork）

### 创建工程
* 将上文中的jar包拷入WEB-INFO中的lib，加到builPath
* 在WEB-INFO中创建web.xml 添加 注意StrutsPrepareAndExecuteFilter类的位置在2.5中全限定名为这个
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://java.sun.com/xml/ns/javaee"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
	id="WebApp_ID" version="3.0">
	 <filter>
        <filter-name>struts2</filter-name>
        <filter-class>org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>struts2</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```
* 在src中创建struts.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>    
<!DOCTYPE struts PUBLIC    
            "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"    
    "http://struts.apache.org/dtds/struts-2.5.dtd">
<struts>
	<constant name="struts.devMode" value="true"/> 开启这个之后可动态修改配置
	<constant name="struts.action.extension" value="li"/> 修改action后缀
	<package name="s" extends="struts-default">
		<action name="hell" class="com.eason.hello.HelloWorld">
			<result>/hello.jsp</result>
		</action>
	</package>
</struts>
```
* 创建类 创建方法 运行...
