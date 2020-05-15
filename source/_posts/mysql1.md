---
title: 数据库入门学习
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
SELECT id,n FROM orders WHERE DATE(order_Date)='2005-08-01';
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

```mysql
CREATE VIEW productcustomers AS SELECT cust_name,cust_contact,prod_id
FROM customers,orders,orderitems
WHERE customers.cust_id=orders.cust_id 
AND orderitems.order_num=orders.order_num;
SELECT cust_name,cust_contact FROM productcustomers
WHERE prod_id='TNT2';
```

#### 利用视图格式化输出、过滤数据，提供计算字段
在之前讲过用函数来格式化输出，如果这个格式化的结果是经常需要用到的，那么可以创建一个视图保存下来
对于数据过滤等方式也可以这样使用

```mysql
SELECT CONCAT(RTRIM(vend_name),'(',RTRIM(vend_country),')') AS vend_title FROM vendors ORDER BY vend_name;
CREATE VIEW vendorlocations AS SELECT CONCAT(RTRIM(vend_name),'(',RTRIM(vend_country),')') 
AS vend_title FROM vendors ORDER BY vend_name;
CREATE VIEW customermaillist AS SELECT cust_id,cust_name,cust_email FROM customers
WHERE cust_email IS NOT NULL;
CREATE VIEW orderitemsexpanded AS SELECT order_num,prod_id,quantity,item_price,
quantity*item_price AS expanded_price FROM orderitems;
```

1. 格式化输出
2. 过滤数据
3. 提供计算字段

#### 视图的更新
通常来说视图是可以更新的即可以对其使用UPDATE，INSERT和DELETE，但是更新一个视图意味着更新他的基表，但
并不是所有的视图都是可以更新的，具有以下操作的视图不可以被更新，在大部分情况下，不应该对视图进行更新。
- 分组 GROUP BY 和 HAVING
- 联结
- 子查询
- 并
- 聚集函数，如min，max，sum等
- DISTINCT；
- 导出列

### 存储过程

存储过程就是为以后的使用而保存的一条或多条mysql语句的集合，视为批文件，注意大部分情况下
用户只有使用存储过程的权限而没有创建存储过程的权限

#### 使用存储过程

```mysql
CALL productpricing(@pricelow,@pricehigh,@riceaverage);
CREATE PROCEDURE productpricing()
BEGIN
  SELECT AVG(prod_price) AS priceaverage FROM products;
end;
DROP PROCEDURE productpricing;
CREATE PROCEDURE productpricing(OUT p1 DECIMAL(8,2),OUT ph DECIMAL(8,2),OUT pa DECIMAL(8,2))
BEGIN 
  SELECT MIN(prod_price) INTO p1 FROM products;
  SELECT MAX(prod_price) INTO ph FROM products;
  SELECT AVG(prod_price) INTO pa FROM products;
end;
SELECT @pricehigh,@pricelow,@priceaverage;
CREATE PROCEDURE ordertotal(
  IN onumber INT,OUT ototal DECIMAL(8,2)
)
BEGIN 
  SELECT SUM(item_price*quantity) FROM orderitems WHERE order_num =onumber INTO ototal;
end;
```

1. 执行存储过程，CALL命令接受存储过程的名字以及需要传递的参数，注意即使过程不需要传参，过程的()也是必须的
2. 创建存储过程,CREATE PROCEDURE 创建，BEGIN和END定义过程体，
3. 删除存储过程，注意命令之后不需要用到().
4. 变量variable是内存中一个特殊的位置,用来存放临时数据,OUT指出相应的参数用来从存储过程
传出一个值，变量需要明确类型，INTO再过程体中表示select语句检索出的结果保存到相应的变量，
IN指示将变量传递给存储过程
5. 在执行了CALL执行存储过程之后，通过`SELECT @变量`获得结果
6. 该命令中使用IN 指示需要传递给存储过程的参数，执行这个命令使用命令`CALL ordertotal(20005,@total);`,其中传递出的参数也必须使用两个，
显示合计结果可以使用`SELECT @total;`

#### 创建智能存储过程

```mysql
-- Name:ordertotal
-- Parameters:onumber=order number
--            taxable = 0 if not taxable,1 if taxable
--            ototal = rder total variable

CREATE PROCEDURE ordertotal(
  IN onumber INT,
  IN taxable BOOLEAN,
  OUT ototal DECIMAL(8,2)
)COMMENT 'Obtain order total,optionally adding tax'
BEGIN 
  -- Declare variable for total
  DECLARE total DECIMAL(8,2);
  -- Declare tax percentage
  DECLARE taxrate INT DEFAULT 6;
  -- Get the order total
  SELECT SUM(item_price*quantity) FROM orderitems WHERE order_num = onumber INTO total;
  -- Is tis taxable?
  IF taxable THEN
      -- Yes,so add taxrate to the total
      SELECT total+(total/100*taxrate) INTO total;
  end if;
  -- And fianlly,save to out variable
  SELECT total INTO ototal;
end;
```

- 设置了两个传入参数，一个int，一个bool变量，declare定义了两个局部变量，--用于设置注释说明，comment非必须的参数
，如果给出，会出现在`SHOW PROCEDURE STATUS`中

#### 检查存储过程
```mysql
SHOW CREATE PROCEDURE ordertotal;
SHOW PROCEDURE STATUS;
```

- 显示用来创建一个存储过程的CREATE语句
- 显示何时，由谁创建的存储过程列表(全部的过程)，可以用LIKE限制输出指定的过程`SHOW PROCEDURE STATUS LIKE 'ordertotal';`

### 游标
mysql操作返回的结果总是与sql语句相匹配的行或者多行无法得到第一行，下一行等操作，游标可以提供在检索结果中前进一行
或者后退一行这样的操作，mysql中游标只能用于存储过程和函数

<br>使用游标的步骤</br>
- 在使用游标之前需要先定义它，该过程没有检索数据，只是定义了使用的SELECT语句
- 声明之后，打开游标以使用，此时前面定义的SELECT语句把数据检索出来
- 对于填有数据的游标，根据需要取出各行
- 结束游标使用时，必须关闭游标

#### 定义游标
```mysql
CREATE PROCEDURE processorders()
BEGIN 
    DECLARE ordernumbers CURSOR FOR 
    SELECT order_num FROM orders;
    OPEN ordernumbers;
    CLOSE ordernumbers;
END;
```


1. DECLARE 定义一个游标，FOR指示需要使用的SELECT语句
2. OPEN打开一个游标，CLOSE关闭一个游标

#### 使用游标
```mysql
CREATE PROCEDURE processorders()
BEGIN 
  -- Declare local variables
  DECLARE done BOOLEAN DEFAULT 0;
  DECLARE o INT;
  DECLARE t DECIMAL(8,2);
  -- Declare the cursor
  DECLARE ordernumbers CURSOR FOR 
  SELECT order_num FROM orders;
  -- Declare continue handler
  DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done=1;
  -- Create a table to store the results
  CREATE TABLE IF NOT EXISTS ordertotals(order_num INT,total DECIMAL(8,2));
  -- Open the cursor
  OPEN ordernumbers;
  -- Loop through all rows
  REPEAT 
      -- get order number
      FETCH ordernumbers INTO o;
      -- Grt the total for this order
      CALL ordertotal(o,1,t);
      -- Insert order and total into ordertotals
      INSERT INTO ordertotals(order_num, total) VALUES(o,t);
      -- End of loop
  until done end repeat;
  CLOSE ordernumbers;
end;
```

1. `CONTINUE HANDLER`字段表示当`SQLSTATE`出现‘02000’时，设置done为1
2. REPEAT定义循环体，他反复执行直到done为真，
3. FETCH检索当前行的某一列，INTO放进某一个变量中
4. CALl执行某一个存储过程


### 触发器
在某个表发生更改时自动处理，触发器是mysql相应以下语句而自动处理的一条mysql语句，包括
DELETE、INSERT、UPDATE

#### 创建或者删除触发器
创建触发器需要
- 唯一的触发器名
- 触发器关联的表
- 触发器应该相应的活动
- 触发器何时执行

注：在mysql中不同的表可以具有同名的触发器，但是建议最好还是在整个数据库中仅有唯一的触发器
    <br>只有表支持触发器，视图不支持触发器，临时表也不支持</br>
```mysql
CREATE TRIGGER newproduct AFTER INSERT ON products
FOR EACH ROW SELECT 'product added';
DROP TRIGGER newproduct;
```
`CREATE TRIGGER`创建一个触发期，在insert之后响应（注意是ON）,对每个行的插入显示一次products added文本一次

触发器按照每个表每个事件每次进行定义，每个表每个时间每次只允许响应一个触发器，因此每个表最多支持6个触发器，
单一触发器不能与多个事件或者多个表进行关联,触发器不能更新或者覆盖

注：如果BEFORE触发器失败，那么mysql将不会执行请求的操作，如果BEFORE触发器或者语句本身失败的话，
AFTER触发器也不会执行

#### 使用触发器

##### INSERT触发器
- 在insert触发器代码内，可饮用一个名为NEW的虚拟表，访问被插入的行
- 在before insert触发器中，NEW中的值也可以被更新，允许更改被插入的值
- 对于自增的列，NEW在insert之前包含0，在insert执行之后包含新的自动生成的值
```mysql
CREATE TRIGGER neworder AFTER INSERT ON orders 
FOR EACH ROW SELECT NEW.order_num;
```
##### DELETE触发器
- 在delete触发器代码内部，可以引用一个名为OLD的虚拟表，访问被删除的行；
- OLD中的值全部都是只读的，不可以更新
```mysql
CREATE TRIGGER deleteorder BEFORE DELETE ON orders FOR EACH ROW 
BEGIN 
    INSERT INTO archive_orders(order_num,order_date,cust_id) VALUES(OLD.order_num,OLD.order_date,OLD.cust_id);
end;
```

BEGIN END 可以为触发器设置多条语句

##### UPDATE触发器
- 在UPDATE触发器中，可以引用一个名为OLD的虚拟表访问以前的值，引用一个名为NEW的虚拟表访问新的更新的值
- 在BEFORE UPDATE触发器中NEW的只可能也被更新
- OLD的值全部都是只读的，不能更新
```mysql
CREATE TRIGGER udatevendor BEFORE UPDATE ON vendors
FOR EACH ROW SET NEW.vend_state = UPPER(NEW.vend_state);
```
#### 触发器的使用要点
- 使用触发器来实现数据的一致性，大小写，格式等
- 创建审计跟踪，即把表格的更改记录到其他的表中

### 事务处理
事务处理用于维护数据库的完整性，保证成批的mysql要么完全处理，要么不处理。事务处理是一种
机制，用来管理必须成批执行的mysql操作，以保证数据库不包含不完整的操作结果

- 事务指一组SQL语句
- 回退指撤销指定SQL语句的过程
- 提交指将未存储的SQL语句结果写入数据库表
- 保留点指事务处理中设置的临时占位符，可以对其发布回退

```mysql
START TRANSACTION ;
ROLLBACK ;
```
`start transaction`标识事务的开始，`ROLLBACK`用于回退命令
- 可以回退的命令包括INSER、UPDATE、DELETE，不能回退CREATE、DROP
- ROLLACK只能在一个事务处理内使用

#### 使用commit
一般的mysql都是直接针对数据库执行和编写的，一般是隐含提交，即提交操作是自动进行的
使用commit语句进行明确的提交

```mysql
START TRANSACTION ;
DELETE FROM orderitems WHERE order_num =20010;
DELETE FROM orders WHERE order_num=20010;
COMMIT ;
```
利用事务处理来保证相关联的两个表不会被部分删除，而最后的commit仅在不出错时写出更改，
例如第一条delete语句起作用，而第二条失败，则delete不会被提交，实际上是自动撤销的

#### 使用保留点
在事务处理块中合适的位置放置占位符，可以在需要回退时回退到某个占位符，称这些占位符为保留点
,保留点在事务处理完成之后（ROLLBACK或者COMMIT）后自动释放
```mysql
SAVEPOINT delete1;
ROLLBACK TO delete1;
```

### 字符集
不同的语言和字符集需要以不同的方式存储和检索，因此mysql需要适应不同的字符集，适应不同的
排序和检索方法
- 字符集：字母和符号的集合
- 编码：为某个字符集成员的内部表示
- 校对：为规定字符如何比较的指令

#### 使用字符集和校对顺序
```mysql
SHOW CHARACTER SET ;
SHOW COLLATION ;
SHOW VARIABLES LIKE 'character%';
CREATE TABLE mytable(
column1 INT,colunm2 VARCHAR(10)
)DEFAULT CHARACTER SET hebrew COLLATE hebrew_general_ci;
CREATE TABLE mytable(
column1 INT,column2 VARCHAR(10),column3 VARCHAR(10) CHARACTER SET latin1 COLLATE latin_general_ci
)DEFAULT CHARACTER SET hebrew COLLATE hebrew_general_ci;
SELECT * FROM customers ORDER BY lastname,firstname COLLATE latin1_general_cs;
```
1. 显示所有可用的字符集以及其描述和默认校对
2. 显示所支持的校对的完整列表
3. 确定所用的字符集以及校对
4. 设置表的默认字符集以及校对
5. 针对表的一个特定列进行设置校对
6. 需要使用与创建表时不同的校对顺序排序特定的SELECT语句，可在SELECT语句中进行


### 安全管理
对用户的权限进行控制

```mysql
USE user;
SELECT user FROM user;
```
在mysql中存在一个user数据库，其中包含全部的用户账号
#### 用户创建删除等
```mysql
CREATE USER ben IDENTIFIED By '123456';
RENAME USER ben TO tom;
DROP USER tom;
```

- 创建用户
- 重命名用户
- 删除用户

#### 设置访问权限
上面的用户创建之后没有任何的权限，需要在创建之后修改权限

```mysql
SHOW GRANTS FOR ben;
GRANT SELECT ON crashcourse.* TO ben;
REVOKE SELECT ON crashcourse.* FROM ben; 
GRANT SELECT,INSERT ON crashcourse.* TO ben;
SET PASSWORD FOR ben = Password('234567');
```

1. 查看用户权限
2. 用GRANT来赋予权限，
    - 要赋予的权限
    - 被授予访问权限的数据库或表
    - 用户名
<br>上面的GRANT命令允许用户访问crashcourse数据库中所有相关的表，权限中的USAGE表示对任意数据库和数据表没有权限</br>
3. REVOKE取消赋予用户的权限
4. GRANT和REVOKE可以在几个层次上控制访问权限
    - 整个服务器，使用`GRANT ALL`和`REVOKE ALL`;
    - 整个数据库，使用`ON database.*`
    - 特定的表，使用`ON database.table`
    - 特定的列；
    - 特定的存储过程；
5. 同一命令给与多个权限
6. SET PASSWORD用于更改用户口令，新的口令必须传递给Password函数，命令中的FOR 用户可以缺省，表示更新当前用户的口令

### 数据库维护
对数据文件进行维护，解决方案如下
- 使用命令行程序mysqldump转存数据库内容到某个外部文件
- 使用命令行程序mysqlhotcopy从一个数据库中复制所有程序
- 使用mysql的backup table和select into outfile转存所有的数据到某个外部文件
#### 进行数据库的维护

```mysql
ANALYZE TABLE orders;
CHECK TABLE orders,orderitems;
```

1. ANALYZE检查表键是否正确
2. CHECK检查表的多种问题

#### 日志文件
- 错误日志:包括启动和关闭问题以及任意关键错误的细节，日志名为：hostname.err
- 查询日志：记录所有的mysql活动，，日志名为：hostname.log
- 二进制日志：记录更新过数据的所有语句，日志名为：hostname-bin
- 缓慢查询日志：记录执行缓慢的任何查询，对于数据库的优化很有用，日志名为：hostname-slow.log
