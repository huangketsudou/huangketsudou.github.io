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
SELECT id,number FROM orders WHERE DATE (order_Date)='2005-08-01';
```

1. 将id转换为大写，
2. 常用函数以及相应的功能可以参考这篇博文[MySql常用函数大全讲解](https://blog.csdn.net/Evankaka/article/details/47911749)
3. 日期以及事件处理函数，DATE()只返回日期中的日期，TIME只返回其中的时间

### 汇总数据
对数据进行分析获得数据的汇总，对数据进行分析和报表生成，注意SQL的函数是对列进行的处理

#### 聚集函数
```mysql
SELECT AVG(pro_price) AS avg_price FROM products;
SELECT AVG(pro_price) AS avg_price FROM products WHERE id=1003;
SELECT AVG(pro_price) AS avg_price FROM products WHERE id=1003;
SELECT COUNT(*) AS num_cust FROM customers;
SELECT COUNT(cust_email) AS num_cust FROM customers;
SELECT MAX(price) AS maxprice FROM products;
SELECT SUM(price) AS item_sum FROM orderitems WHERE order_num=20005;
SELECT SUM(itemprice*quantity) AS total_price FROM orderitems WHERE order_num=20005;
```
1. AVG函数返回指定列的均值，第二个命令返回指定id=1003的pro_price列的均值，忽略NULL
2.COUNT(*)对表中的所有列进行计数，不论其中的值是否为NULL，COUNT(column)返回某一列的行数，忽略NULL值
3. MAX返回列中的最大值，忽视列中为NULL的行，如果用于文本表示，MIN则相应返回最小值
4. SUM计算列的总数，也可以用于数值计算，忽略NULL

#### 聚集不同的值
```mysql
SELECT AVG(DISTINCT prod_price) AS avg_price FROM products WHERE id=1003;
```
1. 对所有的行进行聚集计算，可以用ALL指定，ALL是默认参数，可以缺省，DISTICT则只考虑其中具有不同值的项

#### 组合聚集函数
```mysql
SELECT COUNT(*) AS num_items,MIN(price) AS price_min,MAX(price) AS maxprice,AVG(price) AS priceavg FROM products;
```

### 分组数据
#### 数据分组
```mysql
SELECT COUNT(*) AS num_prods FROM products WHERE id=1003;
```
上面的命令返回id为1003的列值，但是无法返回多个id

#### 创建分组
```mysql
SELECT id,COUNT(*) AS num_prods FROM products GROUP BY id;
```
group by 指示语句按照id排序并分组数据，下面是使用Group By子句需要知道的一些重要的规定

1. Group By子句可以包含任意数目的列，因而可以对分组进行嵌套,进行更细致的分组。
2. Group By子句中列出的每一列都必须是检索列(或者有效的表达式,注意不能是聚集函数)。如果在SELECT中使用了检索列(或者表达式),则在Group By子句中使用相同的表达式,不能使用别名。
3. 大多数SQL不允许Group By带有可变长度的数据类型(如文本,text类型)。
4. 除聚集计算语句外,SELECT语句中的每一列都必须在Group By中给出。
5. 如果分组列中包含具有Null值的行,则Null将作为一个分组返回,如果列中有多行Null，他们将作为一个分组返回。
6. Group By必须出现在Where子句之后,Order By子句之前。
7. 如果在Group By子句中嵌套了分组,数据将在最后指定的分组上进行汇总。换句话说,在建立分组时，指定的所有列都一起计算(不能从个别的列中取回数据)。

#### 过滤以及排序分组
对分组进行过滤必须基于完整的组而非个别的行，where是对行进行的顾虑，不具备分组概念，
分组可以选择使用HAVING，WHERE在分组前过滤，HAVING在分组后过滤

```mysql
SELECT cust_id,COUNT(*) AS orders FROM orders GROUP BY cust_id HAVING  COUNT(*)>=2;
SELECT id,COUNT(*) AS num_prods FROM roducts WHERE pro_price >=10 GROUP BY id HAVING COUNT(*) >=2;
SELECT order_num,SUM(quantity*item_price) AS ordertotal FROM orderitems GROUP BY order_num HAVING SUM(quantity*item_price)>=50 ORDER BY ordertotal
```
1. 利用having过滤达到两个订单及以上的分组
2. 利用where先提出价格至少为10的，再分组
3. 排序

order by：排序产生输出，对任意列可以使用，甚至非选择的列

group by：分组行，输出不一定是分组顺序，只可能选择列或者表达式列，且必须使用每个列表达式
如果与聚集函数一起使用列，那么必须使用

### 使用子查询
#### 使用子查询进行过滤
可以将某一查询的返回结果给另一查询作为where子句,注意子查询总是从内向外执行
应用场景，所需的数据信息分属不同的表格
```mysql
SELECT order_num FROM orderitems WHERE prod_id='TNT';
SELECT cust_id FROM orders WHERE order_num IN (2005,20007);
SELECT cust_id FROM orders WHERE order_num IN (SELECT order_num 
                                  FROM orderitems WHERE prod_id='TNT2');
```

#### 作为计算字段使用子查询

```mysql
SELECT cust_name,cust_state,(SELECT COUNT(*) 
                          FROM orders WHERE orders.cust_id = customers.cust_id) AS orders
                       FROM customers ORDER BY cust_name;
```
上面的查询称为相关子查询，需要使用完全限定的列名，以防止出现错误，如果不用mysql会认为是在一个表格中的同一
字段进行比较，导致错误。
### 联结表
外键为某个表中的一列，它包含另一个表的主键值，定义两个表的关系，关系数据库的可伸缩性比非关系数据库要好

```mysql
SELECT vend_name.prod_name,prod_price
FROM vendors,products
WHERE vendors.vend_id=products.vend_id
ORDER BY vend_name,prod_name;
SELECT vend_name,prod_name,prod_price 
FROM vendors INNER JOIN products 
ON vendors.vend_id=products.vend_id;
SELECT prod_name,vend_name,prod_price,quantity
FROM orderitems,products,vendors
WHERE products.vend_id=vendors.vend_od
AND orderitems.prod_id=products.prod_id
AND order_num=20005;
```
1. SELECT选择的列在不同的表中，FROM子句中有两个表，指示进行联结的表，WHERE指示mysql匹配两表中的id项
，没有正确使用where指示匹配的列将会返回笛卡尔积
2. 内部联结(等值联结)的推荐方式，FROM指示需要进行联结的表，ON指出1命令中where的联结条件
3. 联结多个表

#### 高级联结
```mysql
SELECT cust_name,cust_contact
FROM customers AS c ,orders AS o,ordersitems AS oi
WHERE c.cust_id=o.cust_id
AND oi.order_num=o.order_num
AND prod_id='TNT2';
SELECT prod_id,prod_name FROM products 
WHERE vend_id=(SELECT vend_id
                FROM products
                WHERE prod_id='DTNTR');
SELECT p1.prod_id,p1.prod_name
FROM products AS p1,products AS p2
WHERE p1.vend_id=p2.vend_id
AND p2.prod_id='DTNTR';
SELECT c.*,o.order_num,o.order_date,
oi.prod_id,oi.quantity,OI.item_price
FROM customers AS c,orders AS o,orderitems AS oi 
WHERE c.cust_id=o.cust_id
AND oi.order_num=o.order_num 
AND prod_id='FB';
SELECT customers.cust_name,customers.cust_id,COUNT(orders.order_num) 
AS num_ord FROM customers INNER JOIN orders ON customers.cust_id=orders.cust_id
GROUP BY customers.cust_id;
SELECT customers.cust_name,customers.cust_id,COUNT(orders.order_num) AS num_ord
FROM customers LEFT OUTER JOIN orders
ON customers.cust_id=orders.cust_id 
GROUP BY customers.cust_id;
```

1. 别名还可以用于表，用于缩短查询语句
2. 自联结用于在单条语句中不止一次地引用相同的表,如可以将上面的第二个命令变为第三个的形式，用笛卡尔积思考
3. 自然联结排除联结时的重复语句，但mysql不完成这个工作，需要用户自己完成
4. 外部联结，将一个表与另一个表中的行相关联，但有时需要包含没有关联行的行，因此需要使用外关联
例如，对所有的客户进行订单统计，包括那些未下单的用户，外联结具有LEFT OUTER JOIN和
RIGHT OUTER JOIN
5. 对于内外关联等可以参考这篇[博客](https://www.cnblogs.com/GreenLeaves/p/5824201.html)


### 组合查询
将多个查询，也就是SELECT组合为一个表作为结果返回，这样的查询称为并组合查询与查询中的多条where相同
#### 创建组合查询
```mysql
SELECT vend_id,prod_id,prod_price
FROM products WHERE prod_price<=15 UNION 
SELECT vend_id,prod_id,prod_price
FROM products WHERE vend_id IN (1001,1002);
SELECT vend_id,prod_id,prod_price
FROM products WHERE prod_price<=5 OR vend_id IN (1001,1002);
SELECT vend_id,prod_id,prod_price
FROM products WHERE prod_price<=15 UNION 
SELECT vend_id,prod_id,prod_price
FROM products WHERE vend_id IN (1001,1002)
ORDER BY vend_id,prod_id;
```
上面的两条命令返回结果相同，UNION规则

1. UNION 由两条或者以上的SELECT语句构成，且语句之间要用UINION分割开
2. UNION中的每个查询必须包含相同的列，表达式或聚集函数
3. 列数据类型必须兼容，但不必完全相同
4. UNION会在查询结果中自动忽略重复的行，使用UNION ALL可以返回所有的行，包括重复行
5. 利用order by可对组合查询结果进行排序

### 全文搜索
不是所有的数据库都支持全文搜索的，两个最常用的是MYISAM和InnoDB
```mysql
CREATE TABLE productnotes(
note_id int NOt NUll AUTO_INCREMENT,
prod_id char(10) NOT NULL ,
note_date datetime NOT NULL ,
note_text text NULL ,
PRIMARY KEY (note_id),
FULLTEXT (note_text)
)ENGINE=MyISAM;
SELECT note_text FROM productnotes WHERE Match(note_text) Against('rabbit');
```

1. 创建全文本搜索，利用fulltext指定
2. match()指定要搜索的列，against()指定要使用的搜索表达式,返回的结果以在文本中找到的位置进行排序

#### 查询扩展
```mysql
SELECT note_text FROM productnotes
WHERE Match(note_text) Against('anvils' WITH QUERY EXPANSION );
```
查询拓展用来设法放宽所返回的全文本搜索结果的范围，例如想找出提到anvils的注释，又想找到与anvils相关的行。
在使用查询拓展时，mysql对数据和索引进行两边扫描来完成搜索：

1. 首先，进行一个基本的全文本搜索，找出与搜索条件匹配的所有行；
2. 其次，mysql检查这些匹配行并选择所有有用的词
3. 再其次，mysql再次进行全文本搜索，这次不仅使用原来的条件，而且还使用所有有用的词
4. 注意查询扩展可能会增加你不需要的行


#### 布尔文本搜索
全文本搜索的另一种形式，可以提供如下的功能
1. 要匹配的词
2. 要排斥的词（如果某行包含这个词，就不返回，即使它包含其他的指定词）
3. 排列提示（指定某些词比其他词更重要）
4. 表达式分组

````mysql
SELECT note_text FROM productnotes 
WHERE Match(note_text) Against('heavy' IN BOOLEAN MODE);
SELECT note_text FROM productnotes WHERE 
Match(note_text) Against('heavy -rope*' IN BOOLEAN MODE );
````

- 在索引全文本数据时，短语被忽略且从索引中排除。短语定义为那些具有3个及以下字符的词
- mysql带有一个内奸的非用词列表，这些词在搜索全文本数据时总是被忽略
- 许多词出现的频率很高，搜索时没有用处，因此有50%规则，如果一个次出现在50%以上的行中，那么它将作为一个非用词忽略。
- 50%规则不用于IN BOOLEAN MODE中
- 如果表中的行数少于3行，则全文本搜索不返回结果，因为每个词要么不出现，要么出现在50%的行中
- 忽略词中的单引号

### 插入数据
INSERT用来插入或者添加行到数据库表中

- 插入完整的行，
- 插入行的一部分
- 插入多行
- 插入某些查询的结果

注:insert语句一般没有输出
```mysql
INSERT INTO Customers VALUES(NULL,'pep','100 mian street','los angles','CA','90046','USA',NULL,NUll);
INSERT INTO Customers(cust_name,cust_address,cust_city,cust_state,cust_zip,cust_country,cust_contact,cust_email) VALUES(NULL,'pep','100 mian street','los angles','CA','90046','USA',NULL,NUll);
INSERT INTO Customers(cust_name,cust_address,cust_city,cust_state,cust_zip,cust_country,cust_contact,cust_email) VALUES(NULL,'pep','100 mian street','los angles','CA','90046','USA',NULL,NUll),
('M','42 Galaxy','NEW York','NY','11213','USA');
INSERT INTO customers(cust_id,cust_cotact,cust_enail,cust_name,cust_address,cust_city,cust_state,cust_zip,cust_country)
SELECT cust_id,cust_cotact,cust_enail,cust_name,cust_address,cust_city,cust_state,cust_zip,cust_country FROM custnew;
```
1. 插入完整的一行，第二种写法更好
2. 对于insert操作中的省略要求该列定义允许NULL值或者在表定义中给出默认值
3. 插入多条语句，可以用多条select插入，也可以一条select语句插入多个values
，要求插入时的列语序是相同的
4. 插入检索出来的数据，insert和select的结合，注意cust_id作为主键使用，此时要保障插入时不会有重复


### 更新和删除数据
UPDATE语句可以更新表中的特定行或者所有行，注意不能省略where子句，update语句由要更新的表，列名和相应的新值和要更新行的过滤条件构成
```mysql
UPDATE customers SET cust_email='elmer@fudd.com' WHERE cust_id=1005;
UPDATE customers SET cust_name='The Fudds',cust_email='elmer@fudd.com' WHERE cust_id=1005;
DELETE from customers where cust_id=10006;
```
1. 利用set子句更新一行中某一列的值，也可更新多列，update语句可与select语句一起用
2. delete语句要注意where，否则会删除全部行
3. 注意命令中必须带上where，除非确实要删除全部的行
4. 保证每个表都有一个主键
5. mysql没有undo按钮

### 创建表
```mysql
CREATE TABLE customers(
cust_id int NOT NULL AUTO_INCREMENT,
cust_name char(50) NOT NULL ,
cust_address char(50) NULL, 
cust_city char(50) NULL, 
cust_state char(50) NULL, 
cust_zip char(50) NULL, 
cust_country char(50) NULL, 
cust_contact char(50) NULL, 
cust_email char(50) NULL, 
PRIMARY KEY (cust_id)
) ENGINE =InnoDB;
```

- 如果要求仅在表不存在时创建，那么在表名之后加上if not exists
- 使用NULL或者NOT NULL规定改列是否可使用NULL值
- 主键必须为，可以有单个列或者多个列构成主键，例如PRIMARY KEY (order_num,order_item)
- AUTO_INCREMENT列对一列进行递增操作，且该列在一个表中仅能有一个，且必须被索引
- 对于指定为NOT NULL的列可以通过设置default n设定默认值，

几个引擎的区别
1. INnoDB是一个可靠的事务处理引擎
2. memory在功能上等同于myisam但数据存在内存上，速度快，适用于临时表
3. myisam支持全文索引但不支持事务处理

#### 更新表定义
例如为表增加一列或者删除一列，使用alter table
```mysql
ALTER TABLE vendors ADD vend_phone CHAR(20);
ALTER TABLE vendors DROP COLUMN vend_phone;
```

#### 删除或者重命名表
```mysql
DROP TABLE customers2;
RENAME TABLE back_customers TO customers,back_vendors TO cendors;
```

### 视图
视图不包含表中有的任何列或数据，它包含一个SQL查询
+ 视图的应用
    - 重用SQL语句
    - 简化复杂的SQL语句
    - 使用表的组成部分而不是整个表
    - 保护数据，只给用户部分的访问权限
    - 更改数据格式
+ 视图规则
    - 视图唯一命名
    - 对于视图的创建没有数量限制
    - 创建视图，必须具有足够厚的访问权限
    - 视图可以进行嵌套，可以进行排序，视图禁止索引也禁止关联的触发器
    
#### 使用视图
`CREATE VIEW`创建视图，`SHOW CREATE VIEW viewname`查看视图，`DROP VIEW viewname;`删除视图,
更新视图时先`DROP`后`CREATE`或者`CREATE OR REPLACE VIEW`