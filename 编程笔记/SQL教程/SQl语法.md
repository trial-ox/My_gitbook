## SQL语法

- SQL不区分大小写
- SQL结尾一般用分号来区分不同的语句
- SQL忽略所有空格
- 注释语法
  - \*\\                        \\\*
  - --   注释行内
  - \#    注释整行

##### 一些最重要的 SQL 命令

- **SELECT** - 从数据库中提取数据

  SELECT 语句用于从数据库中选取数据。结果被存储在一个结果表中，称为结果集。

  ```sql
  SELECT * FORM Websites
  ```

  意思是从Websites中选择所有列输出到结果集

  ```sql
  SELECT id,name,price FOR Webstes
  ```

  检索多个数据用，隔开

  ```sql
  SELECT DISTINCT country FROM Websites;
  ```

  意思是从Websites中选择出country列中所有不同的元素

  SELECT语句默认查询所有行，要限制行数的，用SQL语句，但是不同数据库系统不一样。

- **ORDER BY **

  SQL语句由子句构成.

  一个子句通常由一个关键字加上所提供的数据组成

  下面就是三个子句构成的SQL语句

  ```sql
  SELECT prod_name
  FROM Products
  ORDER BY prod_name;
  ```

  - 在指定一条ORDER BY子句时，应该保证它是SELECT语句中最后一条子句。

  - 对多列进行排序，同样使用逗号**，**隔开，但是多行排序有特定规则

    对前面的列进行排序，对后面的列，只在前面的列有重复时才针对重复的列对应的后面的列进行排序

    加入，可以想象成电话簿，相对音序排序，在对同一个音序中不同名字进行排序

  - 可以使用对应的列的顺序号进行排序

    比如这样

    ```sql
    SELECT prod_name
    FROM Products
    ORDER BY 1;
    ```

  - 可以使用DESC进行降序排序

- **WHERE** - 过滤指定数据

  

- **UPDATE** - 更新数据库中的数据
- **DELETE** - 从数据库中删除数据
- **INSERT INTO** - 向数据库中插入新数据
- **CREATE DATABASE** - 创建新数据库
- **ALTER DATABASE** - 修改数据库
- **CREATE TABLE** - 创建新表
- **ALTER TABLE** - 变更（改变）数据库表
- **DROP TABLE** - 删除表
- **CREATE INDEX** - 创建索引（搜索键）
- **DROP INDEX** - 删除索引

