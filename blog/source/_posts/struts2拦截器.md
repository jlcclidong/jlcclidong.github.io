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

### 在自定义拦截器流程
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
