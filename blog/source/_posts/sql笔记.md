---
title: sql笔记
date: 2017-06-24 15:55:11
categories:
- javaee
tags:
- sql
- javaee
---
## sql笔记

### Select---想选择什么，以及从什么地方选
#### 简单例子
* 检索单个列
  ```sql
   SELECT RealName FROM t_user
  ```

* 检索多个列
  ```sql
     SELECT RealName,Gender FROM t_user
  //在选择多个列时，一定要在列名之间加上逗号，但最后一个列名后不加。如果在最后一个列名后加了逗号，将出现错误。
  ```
* 检索所有列
  ```sql
  SELECT * FROM t_user
  ```

* 检索返回不同的值
  ```sql
  SELECT DISTINCT Ctype,Gender FROM t_user  //DISTINCT会将这个字段不重复的值返回,DISTINCT关键字作用于所有的列，不仅仅是跟在其后的那一列。例如，你指定SELECT DISTINCT Ctype, Gender，除非指定的两列完全相同，否则所有的行都会被检索出来
  ```

* 检索限制结果
  ```sql
   SELECT Gender FROM t_user LIMIT 5  OFFSET 5; //各个数据库方式不同mysql中使用方法 限制从第五个开始 长度为5 SELECT Gender FROM t_user LIMIT 5,5;
  ```

#### 排序---关系数据库设计理论认为，如果不明确规定排序顺序，则不应该假定检索出的数据的顺序有任何意义。

* 单列排序
  ```sql
   SELECT * FROM t_user ORDER BY RealName
  //在指定一条ORDER BY子句时，应该保证它是SELECT语句中最后一条子句。如果它不是最后的子句，将会出现错误消息。
  ```
* 多列排序
  ```sql
   SELECT * FROM t_user ORDER BY RealName,Gender //只有在realname有相同值之后才会比较Gender
  ```
* 升降序
  ```sql
   SELECT * FROM t_user ORDER BY RealName DESC,Gender ASC//升降序默认只针对前面的列成立 DESC降序 ASC默认升序可不写
  ```

#### where

* is Null ---在创建表时，表设计人员可以指定其中的列能否不包含值。在一个列不包含值时，称其包含空值NULL。

  ```sql
  SELECT * FROM t_user WHERE t_user.Tel IS NULL //通过过滤选择不包含指定值的所有行时，你可能希望返回含NULL值的行。但是这做不到。因为未知（unknown）有特殊的含义，数据库不知道它们是否匹配，所以在进行匹配过滤或非匹配过滤时，不会返回这些结果。
  ```

* And
  ```sql
  SELECT * FROM t_user WHERE Tel > 15504409406 AND ID < 91000 AND Gender= '男' //并列条件可以使用And连接
  ```

* or or并列时一般满足第一个条件就会返回不需在做其他操作
  ```sql
  SELECT prod_name, prod_price FROM Products WHERE (vend_id = 'DLL01' OR vend_id = 'BRS01') AND prod_price >= 10;//用圆括号括起来不会产生歧义 否则 And 会比 or优先导致结果出错
  ```
* IN 与or具有相同功能 更好的观察性 更好的速度 可以包含select语句
  ```sql
  SELECT prod_name, prod_price FROM Products WHERE vend_id IN('DLL01','BRS01') AND prod_price >= 10
  ```

#### 通配符 LIKE
  * 通配符搜索一般比前面讨论的其他搜索要耗费更长的处理时
  * 不要过度使用通配符。如果其他操作符能达到相同的目的，应该使用其他操作符。
  * 在确实需要使用通配符时，也尽量不要把它们用在搜索模式的开始处。把通配符置于开始处，搜索起来是最慢的。


* % 匹配字符类型 可以在任何位置 可以多个同时使用 但是 通配符%看起来像是可以匹配任何东西，但有个例外，这就是NULL。子句WHERE prod_name LIKE '%'不会匹配产品名称为NULL的行。
  ```sql
  SELECT * FROM t_user WHERE Tel LIKE '1550440%'
  ```

* _ 下划线 一个下划线只能匹配一个字符
  ```sql
  SELECT * FROM t_user WHERE Tel LIKE '1550440940_'
  ```

#### 计算字段

* 拼接字段
```sql
SELECT  CONCAT(Gender,'(',RealName,')') AS GR FROM t_user WHERE Tel = 15504409406
//别名 用as来引用导致输出结果可以直接用此字段使用
```

* 计算字段
```sql
SELECT LoginNum*CType AS la,LoginNum,CType FROM t_user WHERE Tel=15504409406
```

#### 函数

* 文本处理函数

  * 某字段左起几个字符
```sql
SELECT LEFT(RealName,1) AS left_name from t_user WHERE Tel = 18629863729;
```
  * 某个字段的长度
```sql
SELECT LENGTH(Tel) AS left_name from t_user
```
  * 大写变小写
```sql
SELECT LOWER(w_wk.KeyWord) from w_wk
```
  * 小写变大写
```sql
SELECT UPPER(w_wk.KeyWord) from w_wk
```
