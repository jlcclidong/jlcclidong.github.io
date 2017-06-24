---
title: struts2拦截器
date: 2017-05-11 19:33:14
categories:
- javaee
tags:
- struts2
- javaee
---
## struts2的工作流程

![struts2的工作方式](http://omy391n59.bkt.clouddn.com/image/struts2/Struts2-Architecture_%E6%96%B0%E7%89%88%E6%9C%AC.png)
<!-- more -->
### 在自定义拦截器流程
如果配置了自定义拦截器会导致struts2本身的拦截器失效 需要加配置
```xml
<interceptor-ref name="defaultStack"></interceptor-ref>
```
1-2-3-action-result-3-2-1
#### 自定义拦截器使用方式
* 新建一个类继承自AbstractInterceptor

```java

public class MyFirstInterceptor extends AbstractInterceptor {

	private static final long serialVersionUID = 4027957068302300754L;

	@Override
	public String intercept(ActionInvocation invocation) throws Exception {
		System.out.println("拦截器1");//进入action之前执行
		invocation.invoke(); //代表进行下一步，这里不再拦截
		System.out.println("拦截器1返回");//返回之后执行
    //可以再此拦截
    //return “inter”
		return null;
	}

}
```

* 配置struts.xml

```xml
<package name="default" extends="json-default">
		<interceptors>
			<interceptor name="first" class="eason.test.maven.MyFirstInterceptor"></interceptor>
		</interceptors>
		<action name="login" class="eason.test.maven.Login">
			<interceptor-ref name="first"></interceptor-ref>
			<interceptor-ref name="second"></interceptor-ref>
			<result type="json">
				<param name="root">data</param>
			</result>
			<result name="inter">/index.jsp</result>
		</action>
	</package>
```
#### 为了对拦截进行统一处理 使用MethodFilterInterceptor排除不需要的方法

MethodFilterInterceptor中需要重写doIntercept（）

其中两个属性配置
* excludeMethods 不要被拦截的方法 注意方法名不是action的命名而是在类中实际的名称（execute..）
* includeMethods 需要被拦截的方法

#### example配置一个简单登录效果的demo
* 编写登录的Action	 给每个登录过的人分配session

```java
public class LoginAction extends ActionSupport {

	private static final long serialVersionUID = 5890861426406772110L;
	private String username;
	private String password;

	//getter and setter

	@Override
	public String execute() throws Exception {
		HttpSession session = ServletActionContext.getRequest().getSession();
		session.setAttribute("username", username);
		return SUCCESS;
	}
}
```
* 编写进入登录页的Action 直接结果进入主页

```java
public class IndexAction extends ActionSupport{
	private static final long serialVersionUID = -5982176418945798657L;
	@Override
	public String execute() throws Exception {
		return SUCCESS;
	}
}
```
* 编写Interceptor在没有登录之前访问其他页面返回登录页面 在没有session之前都返回login

```java
public class LoginInterceptor extends MethodFilterInterceptor {


	private static final long serialVersionUID = -9195077688930132383L;

	@Override
	protected String doIntercept(ActionInvocation invocation) throws Exception {

		HttpSession session = ServletActionContext.getRequest().getSession();
		Object username = session.getAttribute("username");
		if (username==null) {
			return "login";
		}
		String invoke = invocation.invoke();
		return invoke;
	}

}
```
* 编写login.jsp

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Login Page</title>
</head>
<body>
	<form action="${pageContext.request.contextPath}/login.action">
		用户名<input type="text" name="username"/><br/>
		密码<input type="password" name="password"/><br/>
		<input type="submit" value="登录">
	</form>
</body>
</html>
```
* 配置struts.xml

```xml
<package name="s" extends="struts-default" namespace="/">
	<interceptors>
			<interceptor name="check" class="com.eason.interceptor.LoginInterceptor" /> //注册自定义的interceptor
			<interceptor-stack name="myInter">																					//将struts2默认的defaultStack填入
				<interceptor-ref name="defaultStack"></interceptor-ref>
				<interceptor-ref name="check"></interceptor-ref>
			</interceptor-stack>
		</interceptors>
		<default-interceptor-ref name="myInter"></default-interceptor-ref>  //配置myInter为全局的拦截器
		<global-results>
			<result name="login" type="redirect">/login.jsp</result>     //统一处理结果 返回这个结果的都重定向为login.jsp
		</global-results>
		<action name="login" class="com.eason.action.LoginAction">
			<interceptor-ref name="myInter">
				<param name="check.excludeMethods">execute</param>     //排除不需要拦截的方法 注意此方法为类中的方法名
			</interceptor-ref>
			<result type="redirectAction">index</result>
		</action>
		<action name="index" class="com.eason.action.IndexAction">
			<result type="redirect">/index.jsp</result>
		</action>
	</package>
```
### demo
[demo.zip](http://omy391n59.bkt.clouddn.com/zip/struts2/demologin.rar)
