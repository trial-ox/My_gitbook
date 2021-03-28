# 书籍笔记-《SQL必会知识》

## 一、概念

1. 数据库：DB（database）
2. 数据库软件：DBMS（database manage system）

## 二、基本操作

### SELECT操作

1. 从 products表中检索一个名为prod_name和prod_id的列 

   

   ```
   SELECT prod_name,prod_id FROM products;复制代码
   ```

2.  从 products表中检索所有的列 

   

   ```
   SELECT * FROM products;复制代码
   ```

3. 从 products表中检索所有供应商（过滤相同的id），使用 DISTINCT 关键字

   

   ```
   SELECT DISTINCT vend_id FROM products;复制代码
   ```

4. 从 products表中检索不多于5行数据，使用 **LIMIT** 关键字

5. - 获取前5行（从第一行开始，索引为0即LIMIT 0,5）

     ```
     SELECT prod_name FROM products LIMIT 5;复制代码
     ```

   - 从第6行开始，获取10条数据（指定开始的行数，和获取的行数）

     ```
     // 第1种方式
     SELECT prod_name FROM products LIMIT 5,10;
     // 第2中方式
     SELECT prod_name FROM products LIMIT 10 OFFSET 6;复制代码
     ```

6. 限定数据库下的表和表下的列名称

   ```
   SELECT products.prod_name FROM carshcourse.products;复制代码
   ```

   

### 排序检索数据

排序规则说明：默认按照字母排序，不区分大小写（如要区分大小写，需要在数据库中设置，通过SQL语句设置不了）

1. 先按价格排序，然后在每个价格中再按名排序 ，使用 ORDER BY 

   ```
   SELECT prod_id,prod_price,prod_name FROM products ORDER BY prod_price,prod_name;复制代码
   ```

   

2. 指定排序方向，使用DESC（降序）

   ```
   SELECT prod_id,prod_price,prod_name FROM products ORDER BY prod_price DESC;复制代码
   ```

   

3. 多个排序字段，指定排序方向，未指定的字段按照默认升序 (ASC) 排序

   ```
   SELECT prod_id,prod_price,prod_name FROM products ORDER BY prod_price DESC,prod_name;复制代码
   ```

   

4. 使用ORDER BY和LIMIT的组合，能够找出一个列中最高或最低的值 

   ```
   SELECT prod_price FROM products ORDER BY prod_price DESC LIMIT 1; 复制代码
   ```

### 简单过滤数据

1. 从products表中检索两个列，但不返回所有行，只返回prod_price值为2.50的行 

   ```
   SELECT prod_price，prod_name FROM products WHERE prod_price = 2.50; 复制代码
   ```

   

2. 条件操作符：等于（=）、不等于（<>或者!=）、小于（<）、小于等于（<=）、大于（>）、大于等于（>=）、在指定的两个值之间（BETWEEN）在使用

    BETWEEN时，必须指定两个值——所需范围的低端值和高端值。这两个值必须用AND关键字分隔

   ```
   SELECT prod_price，prod_name FROM products WHERE prod_price BETWEEN 5 AND 10; 复制代码
   ```

   

3. 空值（NULL）检查

   ```
   SELECT prod_name FROM products WHERE prod_price IS NULL; 复制代码
   ```

   

### 复杂数据过滤

1. 检索由供应商1003制造且价格小于等于10美元的所有产品的名称和价格 (AND)

   ```
   SELECT prod_price，prod_name FROM products WHERE vend_id = 1003 AND prod_price = 10; 复制代码
   ```

   

2. 检索由任一个指定供应商制造的所有产品的产品名和价格 (OR)

   ```
   SELECT prod_price，prod_name FROM products WHERE vend_id = 1002 OR vend_id = 1003; 复制代码
   ```

   

3. 列出价格为10美元（含）以上且由1002或1003制造的所有产品 ,一定要使用“()”，因为AND的计算次序优先级高于OR

   ```
   SELECT prod_price，prod_name FROM products WHERE (vend_id = 1002 OR vend_id = 1003) AND prod_price >= 10;; 复制代码
   ```

   

4. IN操作符 - 用来指定条件范围 。例：检索供应商1002和1003制造的所有产品 

   ```
   // 在使用长的合法选项清单时， IN操作符的语法更清楚且更直观 
   // 在使用IN时，计算的次序更容易管理（因为使用的操作符更少）
   // IN操作符一般比OR操作符清单执行更快
   // IN的最大优点是可以包含其他SELECT语句，使得能够更动态地建立WHERE子句
   SELECT prod_price，prod_name FROM products WHERE vend_id IN (1002,1003);复制代码
   ```

5. NOT操作符 - 否定之后所跟的任何条件。 例：列出除1002和1003之外的所有供应商制造的产品 

   ```
   SELECT prod_price，prod_name FROM products WHERE vend_id NOT IN (1002,1003);复制代码
   ```

### 通配符过滤数据

1. **LIKE**操作符

2. - 百分号（%）通配符 - 表示任何字符出现任意次数。 注意：尾部的空格会干扰通配符匹配，即使值使用"%"，也不能匹配值为NULL的元素。 

     ```
     // 例：检索任意以jet起头的词 
     SELECT prod_price，prod_name FROM products WHERE product_name LIKE 'jet%';
     // 例：匹配任何位置包含文本anvil的值 
     SELECT prod_price，prod_name FROM products WHERE product_name LIKE '%anvil%';
     // 例：找出以s起头以e结尾的所有产品 
     SELECT prod_price，prod_name FROM products WHERE product_name LIKE 's%e';复制代码
     ```

   - 下划线（_）通配符 - 用途与%一样，但下划线只匹配**单个字符**而不是多个字符。

     ```
     // a jet , b jet * jet 这些都可以匹配
     SELECT prod_price，prod_name FROM products WHERE product_name LIKE '_ jet';复制代码
     ```

     

   - 注意：%能匹配0个或多个字符，_总是匹配一个字符，不能多也不能少

3. **使用技巧**：因为通配符搜索的处理一般要比前面讨论的其他搜索所花时间更长 ，所以通配符使用时应注意以下几点

4. - 不要过度使用通配符。如果其他操作符能达到相同的目的，应该
     使用其他操作符。
   -  在确实需要使用通配符时，除非绝对有必要，否则**不要把它们用**
     **在搜索模式的开始处**。把通配符置于搜索模式的开始处，搜索起
     来是最慢的

### 正则表达式

1. 使用 REGEXP 关键字（默认匹配不区分大小写，添加BINARY关键字可以设置区分大小写），例：检索列prod_name包含文本1000的所有行：

   ```
   SELECT prod_name FROM products WHERE product_name REGEXP '1000';复制代码
   ```

   

2. 检索列prod_name包含文本1000或2000的所有行,使用"|"

   ```
   SELECT prod_name FROM products WHERE product_name REGEXP '1000|2000';复制代码
   ```

   

3. 匹配几个字符之一 ，使用"[]" 

   ```
   // 1 Ton, a Ton, b Ton都可以被匹配
   SELECT prod_name FROM products WHERE product_name REGEXP '[1ab] Ton';
   // 等价于
   SELECT prod_name FROM products WHERE product_name REGEXP '[1|a|b] Ton';复制代码
   ```

   

4. 匹配除指定字符外的任何东西 ,使用"^"

   ```
   SELECT prod_name FROM products WHERE product_name REGEXP '[^1ab] Ton';复制代码
   ```

5. 匹配范围：[0-9]

   ```
   SELECT prod_name FROM products WHERE product_name REGEXP '[0-9] Ton';复制代码
   ```

   

6. 匹配特殊字符 ：如匹配"."则需要写成"\\." 

   ```
   SELECT prod_name FROM products WHERE product_name REGEXP '\\.';复制代码
   ```

   

7. 匹配任意字符："."

   ```
   SELECT prod_name FROM products WHERE product_name REGEXP '.';复制代码
   ```

   

8. 字符类匹配

9. - [:alnum:] 任意字母和数字（同[a-zA-Z0-9]） 
   - [:alpha:] 任意字符（同[a-zA-Z]）
   - [:blank:] 空格和制表（同[\\t]）
   - [:cntrl:] ASCII控制字符（ASCII 0到31和127）
   - [:digit:] 任意数字（同[0-9]）
   - [:graph:] 与[:print:]相同，但不包括空格
   - [:lower:] 任意小写字母（同[a-z]）
   - [:print:] 任意可打印字符
   - [:punct:] 既不在[:alnum:]又不在[:cntrl:]中的任意字符
   - [:space:] 包括空格在内的任意空白字符（同[\\f\\n\\r\\t\\v]）
   - [:upper:] 任意大写字母（同[A-Z]）
   - [:xdigit:] 任意十六进制数字（同[a-fA-F0-9]）

10. 多个匹配

11. - \*     0个或多个匹配
    - \+    1个或多个匹配（等于{1,}）
    - ?     0个或1个匹配（等于{0,1}）
    - {n}   指定数目的匹配
    - {n,}   不少于指定数目的匹配
    - {n,m} 匹配数目的范围（m不超过255） 

12. 例：匹配连在一起的4位数字 

    ```
    SELECT prod_name FROM products WHERE product_name REGEXP '[[:digit:]]{4}';
    // 等价于
    SELECT prod_name FROM products WHERE product_name REGEXP '[0-9]{4}';
    // 等价于
    SELECT prod_name FROM products WHERE product_name REGEXP '[0-9][0-9][0-9][0-9]';复制代码
    ```

13. 定位符

14. - ^     文本的开始（注意：在集合中（用[和]定义），用它来否定该集合 ）
    - $     文本的结尾
    - [[:<:]]  词的开始
    - [[:>:]]  词的结尾 

### 计算字段及常用数据处理函数

1. 拼接字段Concat

   。例：创建由两列组成的标题 

   ```
   SELECT Concat(vend_name,'(',vend_country,')') FROM vendors;复制代码
   ```

   

2. RTrim()(去掉字符串右边的空格),

   LTrim()(去掉左边的空格),

   Trim()(去掉左右两边的空格)

   ```
   SELECT Concat(RTrim(vend_name),'(',Trim(vend_country),')') FROM vendors;复制代码
   ```

   

3. 使用别名AS

   ```
   SELECT Concat(RTrim(vend_name),'(',Trim(vend_country),')') AS vend_title FROM vendors;复制代码
   ```

   

4.  检索订单号20005中的所有物品并计算价格

   ```
   SELECT prod_id,quantity,item_price,quantity*item_price AS expanded_price FROM orderitems WHERE order_num = 20005;复制代码
   ```

   

5. Upper()

   函数将文本转换为大写

   ```
   SELECT vend_name, Upper(vend_name) AS vend_name_upcase FROM vendors复制代码
   ```

   

6. Left()

   函数返回指定长度的字符串,例：取列prod_description左侧的50个字符。

   ```
   SELECT prod_name, LEFT(prod_description, 50) summary FROM products;  复制代码
   ```

7. 日期和时间处理函数

8. - **AddDate()** 增加一个日期（天、周等）

   - **AddTime()** 增加一个时间（时、分等）

   - **CurDate()** 返回当前日期

   - **CurTime()** 返回当前时间

   - Date()

      返回日期时间的日期部分-返回格式都是yyyy-mm-dd格式

     ```
     // 检索出一个订单记录，该订单记录的order_date为2005-09-01。 
     SELECT cust_id,order_num FROM orders WHERE Date(order_date) = '2005-09-01';
     // 检索出2005年9月下的所有订单 
     SELECT cust_id,order_num FROM orders WHERE Date(order_date) BETWEEN '2005-09-01' AND '2005-09-30';
     // 检索出2005年9月下的所有订单(不需要记住每个月中有多少天或不需要操心闰年2月)
     SELECT cust_id,order_num FROM orders WHERE Year(order_date)= 2005 AND Month(order_date) = 9;
     复制代码
     ```

     

   - **DateDiff()** 计算两个日期之差

   - **Date_Add()** 高度灵活的日期运算函数

   - **Date_Format()** 返回一个格式化的日期或时间串

   - **Day()** 返回一个日期的天数部分

   - **DayOfWeek()** 对于一个日期，返回对应的星期几

   - **Hour()** 返回一个时间的小时部分

   - **Minute()** 返回一个时间的分钟部分

   - **Month()** 返回一个日期的月份部分

   - **Now()** 返回当前日期和时间

   - **Second()** 返回一个时间的秒部分

   - **Time()** 返回一个日期时间的时间部分

   - **Year()** 返回一个日期的年份部分 

9. 数值处理函数

10. - **Abs()** 返回一个数的绝对值
    - **Cos()** 返回一个角度的余弦
    - **Exp()** 返回一个数的指数值
    - **Mod()** 返回除操作的余数
    - **Pi()** 返回圆周率
    - **Rand()** 返回一个随机数
    - **Sin()** 返回一个角度的正弦
    - **Sqrt()** 返回一个数的平方根
    - **Tan()** 返回一个角度的正切 

11. 聚集函数

12. - AVG() 

      返回某列的平均值(AVG()只能用来确定特定数值列的平均值，而且列名必须作为函数参数给出 ,忽略NULL的行)。例：返回products表中所有产品的平均价格 

      ```
      SELECTAVG(prod_price) AS avg_price FROM products;复制代码
      ```

    - COUNT() 

      返回某列(*表示所有行数)的行数(如果指定列名，则指定列的值为空的行被COUNT()函数忽略，但如果COUNT()函数中用的是星号（ *），则不忽略 )

      ```
      // 统计所有行数
      SELECT COUNT(*) AS num_cust FROM custormers;
      // 只对具有电子邮件地址的客户计数 
      SELECT COUNT(cust_email) AS num_cust FROM custormers;复制代码
      ```

    - **MAX()** 返回某列的最大值(虽然MAX()一般用来找出最大的数值或日期值，但MySQL允许将它用来返回任意列中的最大值，包括返回文本列中的最大值。在用于文本数据时，如果数据按相应的列排序，则MAX()返回最后一行。 忽略列值为NULL的行。 )

    - **MIN()** 返回某列的最小值

    - **SUM()** 返回某列值之和 (忽略列值为NULL的行 )

13. 其他常用函数：

14. - **Length()** 返回串的长度
    - **Locate()** 找出串的一个子串
    - **Lower()** 将串转换为小写
    - **Soundex()** 返回串的SOUNDEX值，对串进行发音比较而不是字母比较。
    - **SubString()** 返回子串的字符

### 分组数据

1. **GROUP BY** 分组数据

2. - GROUP BY子句可以包含任意数目的列。这使得能对分组进行嵌套，为数据分组提供更细致的控制。 

   - 如果在GROUP BY子句中嵌套了分组，数据将在最后规定的分组上进行汇总。换句话说，在建立分组时，指定的所有列都一起计算（所以不能从个别的列取回数据）

   - GROUP BY子句中列出的每个列都必须是检索列或有效的表达式（但不能是聚集函数）。如果在SELECT中使用表达式，则必须在GROUP BY子句中指定相同的表达式。**不能使用别名** 。

   - 除聚集计算语句外， SELECT语句中的每个列都必须在GROUP BY子句中给出

   - 如果分组列中具有NULL值，则NULL将作为一个分组返回。如果列中有多行NULL值，它们将分为一组。

   - GROUP BY子句必须出现在WHERE子句之后， ORDER BY子句之前。  

     ```
     // 例：按vend_id分组数据，并统计相同vend_id的产品有多少个
     SELECT vend_id, COUNT(*) AS num_prods FROM products GROUP BY vend_id;复制代码
     ```

3. HAVING过滤分组 （与WHERE类似，WHERE用来过滤行，HAVING用来过滤分组）

   ```
   // 例：按vend_id分组数据，并统计相同vend_id的产品有多少个，并且只需要大于两条的数据
   SELECT vend_id, COUNT(*) AS num_prods FROM products GROUP BY vend_id HAVING COUNT(*)>=2;
   // 组合HAVING和WHERE,例：它列出具有2个（含）以上、价格为10（含）以上的产品的供应商 
   SELECT vend_id, COUNT(*) AS num_prods FROM products WHERE prod_price >= 10 GROUP BY vend_id HAVING COUNT(*)>=2;复制代码
   ```

   

### 子查询

例子：（利用子查询进行过滤）

(1) 检索包含物品TNT2的所有订单的编号。

(2) 检索具有前一步骤列出的订单编号的所有客户的ID。

(3) 检索前一步骤返回的所有客户ID的客户信息。 

上述每个步骤都可以单独作为一个查询来执行。可以把一条SELECT语句返回的结果用于另一条SELECT语句的WHERE子句。也可以使用子查询来把3个查询组合成一条语句。 

> 在SELECT语句中，子查询总是从内向外处理。

```
SELECT cust_name,cust_contact FROM customers WHERE cust_id IN (
SELECT cust_id FROM orders WHERE order_num IN (
SELECT order_num FROM orderitems WHERE prod_id = 'TNT2'));
// 使用联结方式性能更好-联结在后面介绍
SELECT cust_name,cust_contact FROM customers,orders,orderitems WHERE
customers.cust_id = orders.cust_id AND 
orderitems.order_num = orders.order_num AND
prod_id = 'TNT2';复制代码
```



例子：（利用子查询作为计算字段使用）

(1) 从customers表中检索客户列表。

(2) 对于检索出的每个客户，统计其在orders表中的订单数目。 

```
SELECT cust_name,cust_state,
(SELECT COUNT(*) FROM orders WHERE orders_cust_id = customers.cust_id) AS orders 
FORM customers ORDER BY cust_name;复制代码
```

### 联结（Join）

> 一条SELECT语句中可以联结的表的数目没有限制 

如果数据存储在多个表中，怎样用单条SELECT语句检索出数据？ 

子查询虽然也可以解决问题，但是性能并不是最好。

```
SELECT vend_name, prod_name, prod_price FR复制代码
```

1. 内部联结

    INNER JOIN ,

   基于两个表之间的相等测试。这种联结也称为内部联结。

   ```
   SELECT vend_name,prod_name,prod_price FROM vendors INNER JOIN 
   products 
   ON vendors.vend_id = products.vend_id;复制代码
   ```

   

2. 自联结 

   ```
   SELECT p1.prod_id,p1.prod_name FROM products AS p1,
   products AS p2 WhERE
   p1.vend_id = p2.vend.id AND
   p2.prod_id = 'DTNTR';复制代码
   ```

   

### 高级联结

1. 使用表别名

   ```
   SELECT cust_name,cust_contact FROM customers AS c WHERE c.cust_id = 1;复制代码
   ```

   

2. 自然联结：排除多次出现，使每个列只返回一次。 

   ```
   SELECT c.*, o.order_num FROM customers AS c,orders AS o WHERE c.cust_id = o.cust_id; 复制代码
   ```

3. 外部联结（与内部联结关联两个表中的行不同的是，

   外部联结还包括没有关联行

    

   ）在使用OUTER JOIN语法时，必须使用RIGHT或LEFT关键字指定包括其所有行的表（RIGHT指出的是OUTER JOIN右边的表，而LEFT指出的是OUTER JOIN左边的表） 

   ```
   // 左外联结
   SELECT customers.cust_id, orders.order_num FROM customers 
   LEFT OUTER JOIN orders ON 
   customers.cust_id = orders.cust_id;
   // 右外联结
   SELECT customers.cust_id, orders.order_num FROM customers 
   RIGHT OUTER JOIN orders ON 
   orders.cust_id = customers.cust_id;复制代码
   ```

### 组合查询 

 有两种基本情况，其中需要使用组合查询： 

- 在单个查询中从不同的表返回类似结构的数据 
- 对单个表执行多个查询，按单个查询返回数据。 

**使用UNION** 

- UNION必须由两条或两条以上的SELECT语句组成，语句之间用关键字UNION分隔（因此，如果组合4条SELECT语句，将要使用3个UNION关键字）。 
- UNION中的每个查询必须包含相同的列、表达式或聚集函数（不过 各个列不需要以相的次序列出）。 
- 列数据类型必须兼容：类型不必完全相同，但必须是DBMS可以隐含地转换的类型（例如，不同的数值类型或不同的日期类型）。 

```
// 返回的数据会自动去除查询语句中都出现的重复的行
SELECT vend_id,prod_id,prod_privice FROM products WHERE prod_price <=5 
UNION
SELECT vend_id,prod_id,prod_price FROM products WHERE vend_id IN (1001,1002);复制代码
```



**包含或取消重复的行（UNION AL）**

使用UNION返回的数据会自动去除查询语句中都出现的重复的行

使用UNION ALL， MySQL不取消重复的行

### 全文搜索

MySQL支持几种基本的数据库引擎。并非所有的引擎都支持本书所描述的全文本搜索。两个最常使用的引擎为**MyISAM**和**InnoDB**，前者支持全文本搜索，而后者不支持。 默认MySQL创建的表都是InnoDB模式，如果需要用到全文搜索，则该表要创建为MyISAM模式

在使用全文本搜索时， MySQL不需要分别查看每个行，不需要分别分析和处理每个词。MySQL创建指定列中各词的一个索引，搜索可以针对这些词进行。这样， MySQL可以快速有效地决定哪些词匹配（哪些行包含它们），哪些词不匹配，它们匹配的频率，等等。 

为了进行全文本搜索，必须索引被搜索的列，而且要随着数据的改变不断地重新索引。在对表列进行适当设计后， MySQL会自动进行所有的索引和重新索引。 

在索引之后， SELECT可与**Match()**和**Against()**一起使用以实际执行搜索。 

下面的CREATE语句演示了**FULLTEXT**子句的使用： 

```
CREATE TABLE productnotes
(
 note_id int         NOT NULL AUTO_INCREMENT,
 prod_id char(10)    NOT NULL,
 note_text text      NOT NULL,
 PRIMARY KEY(note_id),
 FULLTEXT(note_text)
) ENGINE = MyISAM;复制代码
```

为了进行全文本搜索，MySQL根据子句FULLTEXT(note_text)的指示对它进行索引 这里的FULLTEXT索引单个列，如果需要也可以指定多个列。

在定义之后， MySQL自动维护该索引。在增加、更新或删除行时，索引随之自动更新。 

在索引之后，使用两个函数Match()和Against()执行全文本搜索，其中**Match()**指定被搜索的列， **Against()**指定要使用的搜索表达式。 

```
SELECT note_text FROM productnotes WHERE Match(note_text) Against('rabbit');复制代码
```



> 传 递 给 Match() 的 值 必 须 与FULLTEXT()定义中的相同。如果指定多个列，则必须列出它们（而且次序正确） 

### 使用查询扩展 

查询扩展用来设法放宽所返回的全文本搜索结果的范围 

```
SELECT note_text FROM productnotes WHERE Match(note_text) Against('rabbit' WITH QUERY EXPANSION);复制代码
```

### 全文本布尔操作符 

例：匹配包含heavy但不包含任意以rope开始的词的行， 可使用以下查询： 

```
SELECT note_text FROM productnotes WHERE Match(note_text) Against('rabbit -rope' IN BOOLEAN MODE);复制代码
```

例：搜索匹配包含词rabbit和bait的行 



```
SELECT note_text FROM productnotes WHERE Match(note_text) Against('+rabbit +bait' IN BOOLEAN MODE);复制代码
```



例：搜索匹配包含rabbit和bait中的至少一个词的行。 

```
SELECT note_text FROM productnotes WHERE Match(note_text) Against('rabbit bait' IN BOOLEAN MODE);复制代码
```



例：匹配短语rabbit bait而不是匹配两个词rabbit和bait 

```
SELECT note_text FROM productnotes WHERE Match(note_text) Against('"rabbit bait"' IN BOOLEAN MODE);复制代码
```



1. \+ 包含，词必须存在
2. \- 排除，词必须不出现
3. \> 包含，而且增加等级值
4. < 包含，且减少等级值
5. () 把词组成子表达式（允许这些子表达式作为一个组被包含、排除、排列等）
6. ~ 取消一个词的排序值
7. \* 词尾的通配符
8. "" 定义一个短语（与单个词的列表不一样，它匹配整个短语以便包含或排除这个短语） 

注释事项

- 如果表中的行数少于3行，则全文本搜索不返回结果（因为每个词或者不出现，或者至少出现在50%的行中）。 
-  忽略词中的单引号。例如， don't索引为dont。 
- 仅在MyISAM数据库引擎中支持全文本搜索。 
- 在索引全文本数据时，短词被忽略且从索引中排除。短词定义为那些具有3个或3个以下字符的词（如果需要，这个数目可以更改）。 
- MySQL带有一个内建的非用词（stopword）列表，这些词在索引 全文本数据时总是被忽略。如果需要，可以覆盖这个列表（请参阅MySQL文档以了解如何完成此工作） 

### INSERT

插入一条包含所有记录的行

```
INSERT INTO Customers VALUES(NULL,
'Pep E. LaPew',
'100 Main Street',
'CA',
'90047',
NULL,
NULL);复制代码
INSERT INTO Customers(cust_name,cust_contact,cust_email) VALUES(
'Pep E. LaPew',
NULL,
NULL);复制代码
```

> 没有输出 INSERT语句一般不会产生输出 

各个列必须以它们在表定义中出现的次序填充。第一列cust_id也为NULL。这是因为每次插入一个新行时，该列由MySQL自动增量。 

插入多行

```
INSERT INTO Customers(cust_name,cust_contact,cust_email) VALUES(
'Pep E. LaPew',
NULL,
NULL),(
'Peo L',
NULL,
NULL);复制代码
```

### UPDATE

更新一行

```
UPDATE Customers SET cust_email = 'elmer@fudd.com' WHERE cust_id = 10005;复制代码
```

更新多行

```
UPDATE Customers SET cust_email = 'elmer@fudd.com',cust_name='THe Fudds' WHERE cust_id = 10005;复制代码
```

> UPDATE语句中可以使用子查询，使得能用SELECT语句检索出的数据更新列数据 

>  如果用UPDATE语句更新多行，并且在更新这些行中的一行或多行时出一个现错误，则整个UPDATE操作被取消（错误发生前更新的所有行被恢复到它们原来的值）。为即使是发生错误，也继续进行更新，可使用IGNORE关键字，如下所示：UPDATE IGNORE customers… 

### DELETE

删除一行： 

```
DELETE FROM customers WHERE cust_id = 10005;复制代码
```

> DELETE语句从表中删除行，甚至是删除表中所有行。但是， DELETE不删除表本身。 

> 如果想从表中删除所有行，不要使用DELETE。可使用TRUNCATE TABLE语句，它完成相同的工作，但速度更快 

**在对UPDATE或DELETE语句使用WHERE子句前，应该先用SELECT进行测试，保证它过滤的是正确的记录，以防编写的WHERE子句不正确** 

### CREATE TABLE

```
CREATE TABLE `t_bas_user` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '用户id',
  `account` varchar(128) NOT NULL COMMENT '账户',
  `password` varchar(128) NOT NULL COMMENT '密码',
  `name` varchar(128) NOT NULL COMMENT '账户角色名称（如：管理员）',
  `phone` varchar(20) NOT NULL COMMENT '手机号码',
  `status` tinyint(1) NOT NULL DEFAULT 1 COMMENT '账号状态，默认1（0：禁用，1：启用）',
  `meta_created` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP  COMMENT '创建时间',
  `meta_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '最后一次修改时间',
  `meta_logic_flag` tinyint(1) NOT NULL DEFAULT 0 COMMENT '逻辑删除标识，默认0（0：否，1：是）',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;复制代码
```



### 设置默认值

默认时间：

```
// 数据库中datetime默认保存的格式为'2019-06-03 15:26:51'
`meta_created` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP  COMMENT '创建时间';
复制代码
```



### 引擎类型 

**InnoDB**是一个可靠的事务处理引擎，它不支持全文本搜索； 

**MEMORY**在功能等同于MyISAM， 但由于数据存储在内存（不是磁盘）中，速度很快（特别适合于临时表）； 

**MyISAM**是一个性能极高的引擎，它支持全文本搜索，但不支持事务处理。 

### 更新表(ALTER TABLE)

给表添加一列

```
ALTER TABLE vendors ADD vend_phone CHAR(20);复制代码
```

删除一列

```
ALTER TABLE vendors DROP COLUMN vend_phone;复制代码
```

### 重命名表 (RENAME TABLE )

```
RENAME TABLE vendors TO vendors1;
```