---
title: java反射笔记
date: 2017-04-19 09:28:04
tags:
- 笔记
- java
categories:
- 笔记
- java
---
    仓廪实而知礼节，衣食足而知荣辱 --管仲
## Class
### 获取Class 对象
*  
```java
    Class clazz = a.class;
  ```
* String 要放全路径包名 可能会报ClassNotFoundException
  ```java
try {
    Class class1 = Class.forName("refletc.a");
  } catch (ClassNotFoundException e) {
    e.printStackTrace();
  }
```

### Class 方法
#### 获取名称
```java
String className = aClass.getName();  //全限定类名
String simpleClassName = aClass.getSimpleName(); //类名（不包含包名）
String packageName = aClass.getPackage().getName();// 包名
```
#### 获得类的修饰符 判断类的修饰符
```java
int modifiers = aClass.getModifiers();
Modifier.isAbstract(int modifiers);
Modifier.isFinal(int modifiers);
Modifier.isInterface(int modifiers);
Modifier.isNative(int modifiers);
Modifier.isPrivate(int modifiers);
Modifier.isProtected(int modifiers);
Modifier.isPublic(int modifiers);
Modifier.isStatic(int modifiers);
Modifier.isStrict(int modifiers);
Modifier.isSynchronized(int modifiers);
Modifier.isTransient(int modifiers);
Modifier.isVolatile(int modifiers);
```
#### 更多方法
```java
Class superclass = aClass.getSuperclass(); //获取父类
Class[] interfaces = aClass.getInterfaces(); //获取实现的接口
//下面的方法不能获取到私有的方法 变量 构造器
Constructor[] constructors = aClass.getConstructors(); //获取构造器
Method[] method = aClass.getMethods(); //获取方法
Field[] method = aClass.getFields(); //获取变量
```
## Constructor
通过已知构造参数 获取构造方法 new新对象
```java
      try {
            Constructor constructor = clazz.getConstructor(String.class);
            A r = (A) constructor.newInstance("test");
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        }
```
## Field变量
```java
      try {
            //获取所有的变量 私有的不行
            Field[] fields = clazz.getFields();
            //获取指定的成员变量
            Field field = clazz.getField("name");
            System.out.println(field.getName()); //name 变量名
            System.out.println(field.getType());  //class java.lang.String
            HelloWorld obj = new HelloWorld("123");
            s.set(obj,"222");                 //设置 成员变量值
            System.out.println(obj.getField(obj)); //得到成员变量值

            Field s1 = clazz.getField("password"); //password为静态变量   
            System.out.println(s1.get(null));   //静态变量获取时 变量只需要传null
            s1.set(null,"rrr");               
            System.out.println(s1.get(null));   //静态变量设置后 其他的实例中静态变量也都相应改变
            System.out.println(obj.s1);
        } catch (NoSuchFieldException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
```
