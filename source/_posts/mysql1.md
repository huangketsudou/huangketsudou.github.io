---
title: mysql1
date: 2020-05-08 22:25:58
tags:
- linux
- 编程
categories:
- 程序人生
- mysql
declare: true
comments: true
---

![图片](http://api.mtyqx.cn/api/random.php?21)

mysql的初步学习博客，记录MySQL的一些基本命令以及遇到的相应问题
<!-- more -->

### 使用mysql
#### 选择数据库
```mysql
USE database;
```
每次选择数据库之前的必要操作
#### 了解数据库以及表
```mysql
SHOW DATABASES;
SHOW TABLES;
SHOW COLUMNS FROM customers;
DESCRIBE customers;
```
这三个命令分别返回当前所有的可用数据库，所选数据库中可用的表,以及对表的描述信息，第三个命令
与第四个命令在功能上是一样的
### 检索数据
#### 初步检索数据
```mysql
SELECT id FROM products;
SELECT id,v FROM products;
SELECT * FROM products;
```
上面的三个命令分别用于从表中选出单列，多列以及全部列的所有值，注意选出来的值并不存在排序
#### 对检索数据进行限制
```mysql
SELECT DISTINCT id FROM products;
SELECT DISTINCT id,v FROM products;
```
DISTINCT 参数表示从所选的列中仅返回不同的值，且该参数必须放在所选列名的最前面，否则错误，
且该参数会作用于所有的列，即对于第二个命令，仅当两列都相同时才会被除去，
例如\[[1,2],[1,2]]会只返回\[1,2],而\[[1,2],[1,3]]会全部返回
```mysql
SELECT id FROM products LIMIT 5;
SELECT id from products LIMIT 3,4;
SELECT if FROM products LIMIT 4 OFFSET 3;
```
LIMIT参数返回限制返回结果的数量,第二个命令中第一个表示从某一行开始，第二个表示数量上限,第三个与第二个等效
```mysql
SELECT products.id FROM products;
```
完全限制表名的语法，某些情形下使用

### 排序数据
select选出来的数据没有顺序性，所以为使其呈现一定的顺序，将使用order by 子句
<br>子句：SQL由子句构成，部分子句可选，例如FROM子句，子句是有顺序的，错误的顺序会返回错误消息</br>
```mysql
SELECT prod_name FROM products ORDER BY prod_name;
SELECT prod_name FROM products ORDER BY prod_price,prod_name;
SELECT prod_name FROM products ORDER BY prod_name DESC;
SELECT prod_name FROM products ORDER BY prod_price DESC ,prod_name;
```
单列排序以及多列排序,最后一个命令对排序结果递减表示，注意DESC关键字只作用于位于其前面的列名,如果想每个列
都按降序排列，必须为所有的列进行指定，与DESC相反的是ASC，是默认的排序方式
<br>注意在数据库管理系统中，字符是不区分大小写的，A与a在字典序上是一致的，如果需要做区分
，需要数据库管理员的协助</br>
### 过滤选择

```mysql
SELECT prod_name,prod_price FROM products WHERE prod_price = 2.5;
SELECT prod_name,prod_price FROM products WHERE prod_price < 2.5;
SELECT prod_name,prod_price FROM products WHERE prod_price <= 2.5;
SELECT prod_name,prod_price FROM products WHERE prod_name = 'tom';
SELECT prod_name,prod_price FROM products WHERE prod_price BETWEEN 2.5 AND 10;
SELECT cust_id FROM customers WHERE cust_email is NULL;
```
1. 找到两列，并返回prod_price=2.5的列
2.  =等于 <> 不等于 != 不等于 <小于 <=小于等于 >大于 >=大于等于 BETWEEN在指定的两个值之间
3. 字符串要用‘’括起来
4. 检查空值

### 子句组合

```mysql
SELECT id,price,name FROM products WHERE vendid=1003 AND price <= 10;
SELECT id,price FROM products WHERE id=1002 OR id=1003;
SELECT id,price FROM products WHERE (id=1000 OR id=1003) AND price>=10;
SELECT id,price FROM products WHERE id IN (1002,1003) ORDER BY id;
SELECT id,price FROM products WHERE id NOT IN (1002,1003) ORDER BY price;
```

AND操作符返回符合多个条件的行，每多一个条件就需要增加一个AND，OR返回满足一个条件的行
AND和OR在数据库中，AND的操作优先级高，即```id=1000 OR id=1003 AND price>=10```
会理解为id=1000或者id=1003且价格小于10的,IN用来指定多个允许匹配的条件，NOT用于否定
之后所有的条件
### 通配符过滤

```mysql
SELECT id ,price FROM products WHERE id LIKE 'jet%';
SELECT id,price FROM products WHERE id LIKE '%avail%';
SELECT id,price FROM products WHERE id LIKE 's%e';
SELECT id,price FROM products WHERE id LIKE '_ ton';
```
1. %通配符表示任意字符出现任意次数，命令表示以jet开头的所有词和模式中含有avail的
模式,以及以s,e开头的词，但%不会匹配NULL
2. _ 匹配单个任意字符，
3. LIKE匹配模式要求整个字符串完全匹配，即LIKE '1000'不会匹配到'jet 1000'

### 正则表达时搜索

```mysql
SELECT id FROM products WHERE id REGEXP '1000' ORDER BY id;
SELECT id FROM products WHERE id REGEXP BINARY 'Jet 1000';
SELECT id FROM products WHERE id REGEXP '1000|2000';
SELECT id FROM products WHERE id REGEXP '[123] Ton';
SELECT id FROM products WHERE id REGEXP '[1-3] Ton';
SELECT id FROM v WHERE id REGEXP '\\.' ORDER BY id;
SELECT id FROM v WHERE id REGEXP '\\([0-9] sticks?\\)';
SELECT id FROM v WHERE id REGEXP '^[0-9\\.';
```

1. mysql 匹配默认不会识别字母的大小写，可用BINARY关键字指定区分大小写，且只要在模式中出现就行，如'1000'可以匹配到'jet 1000'
2. | 表示OR选择，可以并列多个|
3. \[] 匹配括起来的任一个字符 ，相当于\[a|b|c\],但是\[]不可以缺省
4. \[1-9],\[a-z]匹配范围
5. \\来实现对特殊字符的转义
6. 匹配字符类，例如\[:alnum:\],相当于\[a-zA-Z0-9\]
7. 匹配多个实例 *:0或者多匹配，+：1或者多匹配，?：0或者1个匹配，{n}:指定匹配数目,
{n,}：指定不少于n的匹配数目,{n,m}：匹配数目范围
8. 定位符^在开头的位置进行匹配

### 创建计算字段

```mysql
SELECT CONCAT(id,'(',v,')') FROM vendors ORDER BY id;
SELECT CONCAT(RTRIM(id),'(',RTRIM(v),')') FROM vendors ORDER BY id;
SELECT CONCAT(id,'(',v,')') AS ventitle FROM vendors ORDER BY id;
SELECT id,quantity,itemprice,quantity*itemprice AS expandedprice FROM orderitems WHERE ordernum=20005;

```

1. CONCAT连接字段并构成新的值返回，
2. RTRIM去掉字符右边空格，LTRIM去掉左边空格，TRIM去掉两边空格
3. AS 创建别名
4. 执行算术计算，并给与别名，算数队列可执行+-*/以及括号运算


### 使用函数

```mysql
SELECT id,UPPER(id) AS id_upcase FROM vendors;

```

1. 将id转换为大写，
2. 常用函数以及相应的功能可以参考这篇博文[MySql常用函数大全讲解](https://blog.csdn.net/Evankaka/article/details/47911749)
3. 日期以及事件处理函数，DATE()只返回日期中的日期，TIME只返回其中的时间