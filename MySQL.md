# MySQL

## 必知必会

> MySQL数据类型：
>
> 1. 整数类型：INT,BIGINT；4字节和8字节
> 2. 浮点数类型：
>    * FLOAT(p)：单精度，p取0-24，4字节
>    * DOUBLE(p)：双精度，p取25到53，8字节
> 3. 字符串类型：
>    * CHAR(n)：固定长度的字符类型，最长255
>    * VARCHAR(n)：可变长度的字符串，最长65535
>    * TEXT：长文本数据，最长65535
> 4. 日期和时间类型：
>    * DATE：日期，格式’YYYY-MM-DD‘
>    * TIME：时间，格式’HH:MM:SS‘
>    * DATETIME：日期和时间，格式’YYYY-MM-DD HH:MM:SS‘
>    * TIMESTAMP：日期和时间的时间戳，和DATETIME格式一样但使用UTC时间，一般记录数据修改时间，**自动存储为当前时间**
> 5. 布尔类型：
>    * BOOLEAN或BOOL：在MySQL中实际上就是TINYINT(1)，0表示false，1表示true

---

**MySQL查询子句结构**

```sql
SELECT
FROM
WHERE
GROUP BY
HAVING
ORDER BY
LIMIT
```

**SQL 查询的基本执行顺序**

1. **`FROM`**：首先确定要从哪个表或视图获取数据。
2. **`WHERE`**：应用过滤条件，排除不符合条件的行。
3. **`GROUP BY`**：根据某些列对数据进行分组（如果有 `GROUP BY` 子句）。
4. **`HAVING`**：对分组后的数据进行过滤（在 `GROUP BY` 之后）。
5. **`SELECT`**：选择具体的列，计算表达式或聚合函数。
6. **`ORDER BY`**：对结果进行排序。
7. **`LIMIT`**：如果有，限制返回的行数。

```sql
# 第三章：使用MySQL
show databases
show tables
use database_name
show columns from table_name
show status：显示广泛的服务器状态信息
show create database_name
show create table_name
show grants：显示授予用户的安全权限
ALTER DATABASE dbname CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci：修改数据库字符集
ALTER TABLE tbname CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci：修改表字符集
-----------------------------------------------------------------

# 第四章：检索数据column_name、table_name
select c_name1,c_name2 from t_name
select distinct c_name from t_name
select c_name from t_name limit 5：显示前5行数据（检索下标从0开始）
select * from t_name limit 5,5：从第5行开始显示5行数据
select * from t_name limit 4 offset 3：和3,4表示意思一样
-----------------------------------------------------------------

# 第五章：排序检索数据（升序ASC降序DESC）
select * from t_name order by c_name1,c_name2：按照c_name1和c_name2的顺序排序
select * from t_name order by c_name desc：按照c_name从大到小降序排序
select * from t_name order by c_name1 desc c_name2：按照c_name1降序排列，再然后对c_name2排序
select price from product order by price desc limit 1：找出product中price最高的值
-----------------------------------------------------------------

# 第六章：过滤数据
> 注：where应在order by之前，否则会产生错误
select * from t_name where name='lisi'
> SQL中可用=、!=、>、<、>=、<=、between、<>（不等于）
select c_name 
from t_name 
where c_value between value1 and value2：检索value1到value2之间的c_value值

select prod_name from products where prod_price is NULL：找到prod_price为空的所有prod_name
-----------------------------------------------------------------

# 第七章：数据过滤
AND和OR
select * 
from t_name 
where c_id=1002 OR c_id=1003 AND c_price >=10：返回的不一定都是c_price>=10的数据。因为在OR和AND中AND的优先级更高，所以先匹配AND左右匹配条件的数据
select * 
from t_name
where (c_id=1002 OR c_id=1003) AND c+price >=10：返回正确预期数据
IN关键字：更多的选项清单时，IN比OR快
select * from t_name where t_id in (1001,1002) order by desc
NOT关键字
select * from t_name where t_id not in (1001,1002) order by desc
-----------------------------------------------------------------

# 第八章：用通配符进行过滤
> 搜索模式'%'、'_'
select * from t_name where name like '%jack%'：匹配name=xxxxxjackxxxx的用户信息
select * from t_name where name like 'jack_'：匹配name=jack*的用户信息
-----------------------------------------------------------------

# 第九章：用正则表达式进行搜索
select * from t_name where number regexp '.000'：匹配number为1000、2000...等*000的值
> 注意：regexp正则表达式在一些数值匹配时与like关键字有区别。有些数值匹配时like在匹配文本时不会匹配成功
比如：
select * from t_name where number regexp '1000'
select * from t_name where number like '1000'
> 在like匹配到1000的文本信息时，不会返回任何值，但regexp会
select * from t_name where number regexp '1000|2000'：匹配符合number=1000或number=2000的数据
select * from t_name where number regexp '[1|2|3 tom]'：匹配符合1 tom或2 tom或3 tom的值
-----------------------------------------------------------------

# 第十章：创建计算字段：就是在查询语句中对数据进行处理(加减乘除拼接取平均)再返回给用户
> Concat()、Trim()、RTrim()、Ltrim()、Now()
select Concat('(',role_name,')') 
from t_name：查询出role_name后输出字段名为Concat('(',role_name,')')
> 输出：
+---------------------------+
| Concat('(',role_name,')') |
+---------------------------+
| (普通管理员)               |
| (课程管理员)               |
| (test)                    |
+---------------------------+
> 注意：Concat()方法后输出的字段只是“值”，无法被客户机读取，因此需要使用alias创建别名返回AS关键字
select Concat('(',item')') AS name from t_name;
-----------------------------------------------------------------

# 第十一章：使用数据处理函数
> 字符串处理函数
Concat()、Substring()、Length()、Trim()、Lower()、Upper()、Replace()
> 数值处理函数
Abs()、Ceil()、Floor()、Round()、Mod()、Pow()、Sqrt()、Rand()
> 日期和时间处理函数
Now()、Date()、Time()、Year()、Month()、Day()
> 聚合函数：用于汇总操作，常和GROUP BY一起使用
Count()、Sum()、Avg()、Max()、Min()
> 条件判断函数
IF(a > b, a, b)：a>b在true时返回a，false时返回b
IFNULL(NULL, 'default')：为NULL时返回default

SELECT
	CASE
		WHEN A>500 THEN 'HIGH'
		WHEN A>100  THEN 'MID'
		ELSE name END
FROM t_name;
-----------------------------------------------------------------

# 第十三章：分组数据GROUP BY 必须在where后order by之前
> GROUP BY ... HAVING ... 注意：where在数据分组前过滤，having在数据分组后过滤
select * 
from t_name 
group by t_id having count(*)>=2：根据t_id分组后找到组内数>=2的数据

where和having的例子：
select p_id,count(*) AS num_prods
from products
where prod_price>=10
group by p_id having count(*) >= 2
解读：找到price>=10的数据然后根据p_id进行分组，最后找到组内数量>=2的所有数据
-----------------------------------------------------------------

# 第十四章：使用子查询
> IN关键字子查询
SELECT cust_name,cust_text
FROM customers
WHERE cust_id IN (SELECT cust_id 
                  FROM orders
                  WHERE order_num IN (SELECT order_num 
                                      FROM orderitems
                                      WHERE order_id = 99));
> 复杂子查询
SELECT cust_name,
	   cust_state,
	   (SELECT COUNT(*)
        FROM orders
       	WHERE orders.cust_id = customers.cust_id) AS orders
FROM customers
ORDER BY cust_name;
> 执行过程：FROM读取customers表每一行->进入子查询，每一行都会执行一次子查询语句并返回子查询中的count(*)->SELECT输出cust_name和cust_state和orders->最后ORDER BY根据cust_name进行分组
-----------------------------------------------------------------

# 第十五章：联结表（联结查询）
> 注意：在使用多表查询时，联结表的关系是在查询语句运行中构造的。如果在SELECT中没有WHERE条件约束，那么查询*第一个表中的每一行数据将和第二行的每一行数据配对*，而不管是否逻辑上是否可以相互匹配。
例子：SELECT v_name,p_name,p_price FROM v,p WHERE v.id = p.id ORDER BY v_name;
> **笛卡尔积**：没有联结条件的表关系返回的结果为笛卡尔积，检索出的行的数目将是第一个表中的行数✖第二个表中的行数。
上述例子称为'等值联结'基于两个表之间的相等测试也叫'内部联结'
-----------------------------------------------------------------

# 第十六章：创建高级联结
> 自联结、自然联结、外部联结
自联结1(内部联结)
SELECT prod_id,prod_name
FROM products
WHERE wend_id = (SELECT vend_id
                 FROM products
                 WHERE prod_id = 'DTNTR');
自联结2(内部联结)           
SELECT prod_id,prod_name
FROM products AS p1,products AS p2
WHERE p1.vend_id = p2.vend_id AND p2.vend_id = 'DTNTR';

自然联结
> 所有的内部联结都可以成为自然联结。在定义表时创建专门对外联结的列。例A表b_id和B表a_id相互匹配联结
*外部联结
> JOIN、LEFT JOIN、RIGHT JOIN、FULL JOIN
带聚合函数的外部联结SQL语句例子
SELECT c.cust_name,
	   c.cust_id,
	   COUNT(o.order_num) AS num_ord
FROM customers AS c JOIN orders AS o ON c.cust_id = o.cust_id
GROUP BY c.cust_id;
-----------------------------------------------------------------

# 第十七章：组合查询
> UNION操作符将多条SELECT语句组合成一个结果，必须两条或以上的SELECT才能使用UNION。UNION会默认将多条SELECT语句查出的重复内容去除，如果想返回重复的行可以使用UNION ALL
UNION使用时多条SELECT只能用一个ORDER BY排序，例：
SELECT vend_id,prod_id,prod_price
FROM products
WHERE prod_price <= 5
UNION
SELECT vend_id,prod_id,prod_price
FROM products
WHERE vend_id IN (1001,1002)
ORDER BY vend_id,prod_price;
-----------------------------------------------------------------

# 第十八章：全文本搜索
> 在5.6版本后MyISAM和InnoDB引擎都支持全文本搜索。
> FULLTEXT(content1,conten2,.....)：可以指定多个列的全文本索引
CREATE TABLE products(
	note_id    int NOT NULL AUTO_INCREMENT,
    note_text  text NULL,
    PRIMARY KEY(note_id),
    FULLTEXT(note_text)
)ENGINE=MyISAM;
> 简单例子：
SELECT note_text
FROM products
WHERE Match(note_text) Against('要搜索的文本' IN NATURAL MODE); --默认使用'自然语言'模式
> Match()...Against()：分为三种模式->自然语言模式NATURAL LANGUAGE MODE、布尔模式BOOLEAN MODE、查询拓展模式WITH QUERY EXPANSION



-----------------------------------------------------------------

# 第十九章：插入数据
> INSERT INTO t_name VALUES(val1, val2, val3, NULL, 'val4', now());
-以上插入方式并不安全
> INSERT INTO t_name(t_id, t_name, t_address, t_score, t_text, t_time) VALUES(val1, val2, val3, NULL, 'val4', now());
例子：插入检索出的数据
INSERT INTO customers(cust_id,cust_contact,cust_email) SELECT cust_id,cust_contact,cust_email FROM custnew;
-----------------------------------------------------------------

# 第二十章：更新和删除数据
> UPDATE和DELETE：使用该操作符时一定注意加上WHERE过滤条件，否则更新或删除整条列
UPDATE例子：
UPDATE t_name SET t_val1 = '一个更新的值1',t_val2 = '一个更新的值2' WHERE t_id = '需要更新的指定行';
*注意：UPDATE在更新语句执行中发生错误时会将前面更新过的语句还原，如果不想前面的值被还原可以使用'IGNORE'关键字
例：UPDATE IGNORE t_name SET ....
DELETE FROM t_name WHERE t_id = 1001;

-----------------------------------------------------------------
# 第二十一章：创建和操纵表
> 创建格式如下，'*'表示常用类型，使用InnoDB引擎和utf8mb4编码格式
CREATE TABLE example_table (
    -- 整数类型
*   id INT AUTO_INCREMENT PRIMARY KEY,    --* 自增的整数作为主键
    tiny_int_column TINYINT,              -- 范围: -128 到 127 或 0 到 255
    small_int_column SMALLINT,            -- 范围: -32,768 到 32,767 或 0 到 65,535
    medium_int_column MEDIUMINT,          -- 范围: -8,388,608 到 8,388,607 或 0 到 16,777,215
*   int_column INT NOT NULL,              --* 范围: -2,147,483,648 到 2,147,483,647 或 0 到 4,294,967,295
*   big_int_column BIGINT NULL,           --* 范围: -2^63 到 2^63-1 或 0 到 2^64-1

    -- 小数和浮点数类型
    decimal_column DECIMAL(10, 2),        -- 定点小数，总共10位，其中2位小数
*   float_column FLOAT,                   --* 单精度浮点数
    double_column DOUBLE,                 -- 双精度浮点数

    -- 日期和时间类型
*   date_column DATE DEFAULT CURRENT_DATE,--* YYYY-MM-DD 默认当前日期
*   datetime_column DATETIME,             --* YYYY-MM-DD HH:MM:SS
    timestamp_column TIMESTAMP DEFAULT CURRENT_TIMESTAMP, -- 包含时区，自动更新
    time_column TIME,                     -- HH:MM:SS
    year_column YEAR(4),                  -- 4位年份

    -- 字符串类型
*   char_column CHAR(10),                 -- 固定长度字符串
*   varchar_column VARCHAR(255) DEFAULT 'pending',          --* 可变长度字符串 默认值为'pending'
    tinytext_column TINYTEXT,             -- 长度255个字符以内的文本
    text_column TEXT,                     --* 长度65535个字符的文本
    mediumtext_column MEDIUMTEXT,         -- 长度16,777,215个字符的文本
    longtext_column LONGTEXT,             -- 长度4,294,967,295个字符的文本

    -- 二进制类型
    binary_column BINARY(10),             -- 固定长度的二进制数据
    varbinary_column VARBINARY(255),      -- 可变长度的二进制数据
    tinyblob_column TINYBLOB,             -- 小的二进制大对象
    blob_column BLOB,                     -- 最大长度为65535字节的二进制大对象
    mediumblob_column MEDIUMBLOB,         -- 最大长度16MB的二进制大对象
    longblob_column LONGBLOB,             -- 最大长度为4GB的二进制大对象

    -- 枚举和集合类型
    enum_column ENUM('value1', 'value2', 'value3'),   -- 枚举类型
    set_column SET('option1', 'option2', 'option3'),  -- 集合类型
    
    -- 创建索引
    INDEX idx_text_column (text_column),
    -- 指定主键，主键可以指定多个，但值必须唯一
    PRIMARY(id),
    -- 创建外键（非必要不用外键）
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id) -- 外键，引用customers表中的customer_id
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

> 操纵表
更新列：ALTER TABLE t_name ADD t_val CHAR(10)：更新名为t_name的表添加一个CHAR类型的t_val
删除列：ALTER TABLE t_name DROP COLUMN t_col：删除t_name表中名为t_col的列
加外键：ALTER TABLE t_name ADD CONSTRAINT fk_t_order 
						FOREIGN KEY (t_order) REFERENCES orders(order_num);
删除表：DROP TABLE customers;
重命名：RENAME TABLE customers;
-----------------------------------------------------------------

# 第二十二章：使用视图

```

| 区别       | InnoDB                                                       | MyISAM                                             |
| ---------- | ------------------------------------------------------------ | -------------------------------------------------- |
| 事务       | 四种隔离级别（读未提交、读已提交、重复读、序列化）           | 否                                                 |
| 外键       | 支持                                                         | 否                                                 |
| 锁         | 行级锁                                                       | 表级锁                                             |
| 数据完整性 | 重做日志（Redo Log）和撤销日志（Undo Log）保障系统崩溃自动恢复数据 | 手动恢复                                           |
| 全文搜索   | 5.6版本后支持全文索引（FULLTEXT）                            | 支持                                               |
| 使用场景   | 1.高并发；2.频繁增、删、改；3.有事务需求（数据一致性）       | 读多写少（存日志）                                 |
| 存储结构   | 聚簇索引                                                     | 非聚簇索引                                         |
| 表大小限制 | 64TB                                                         | 256TB                                              |
| 空间管理   | 自动管理表空间，支持压缩表                                   | .MYD和.MYI存储数据和索引，不支持表压缩             |
| 自增字段   | AUTO_INCREMENT基于事务提交，增值不会被回滚                   | 基于表锁的自增，删除最后插入的行时自增值不会被回滚 |

> 总结：InnoDB适合-高并发、增删改多、事务需求；MyISAM适合-读多写少的数据

## 查询页面

### 一、数据库操作命令

1. 显示所有数据库：

   ```sql
   SHOW DATABASES;
   ```

2. 创建数据库：

   ```sql
   CREATE DATABASE dbname;
   ```

3. 删除数据库：

   ```sql
   DROP DATABASE dbname;
   ```

4. 使用某个数据库：

   ```sql
   USE dbname;
   ```

5. 显示当前使用的数据库：

   ```sql
   SELECT DATABASE();
   ```

6. 查看数据库创建语句：

   ```sql
   SHOW CREATE DATABASE dbname;
   ```

7. 修改数据库的字符集：

   ```sql
   ALTER DATABASE dbname CHARACTER SET utf8mb4;
   ```

### 二、表操作命令

1. 显示所有表：

   ```sql
   SHOW TABLES;
   ```

2. 创建表：

   ```sql
   CREATE TABLE table_name (
       column_name1 datatype,
       column_name2 datatype,
       ...
   );
   ```

3. 查看表结构：

   ```sql
   DESCRIBE table_name;
   ```

   或

   ```sql
   SHOW COLUMNS FROM table_name;
   ```

4. 查看创建表的语句：

   ```sql
   SHOW CREATE TABLE table_name;
   ```

5. 删除表：

   ```sql
   DROP TABLE table_name;
   ```

6. 重命名表：

   ```sql
   RENAME TABLE old_table_name TO new_table_name;
   ```

7. 修改表结构（添加列）：

   ```sql
   ALTER TABLE table_name ADD column_name datatype;
   ```

8. 修改表结构（删除列）：

   ```sql
   ALTER TABLE table_name DROP COLUMN column_name;
   ```

9. 修改表结构（修改列数据类型或名称）：

   ```sql
   ALTER TABLE table_name MODIFY column_name new_datatype;
   ALTER TABLE table_name CHANGE old_column_name new_column_name new_datatype;
   ```

### 三、数据操作命令

1. 插入数据：

   ```sql
   INSERT INTO table_name (column1, column2, ...) VALUES (value1, value2, ...);
   ```

2. 插入多行数据：

   ```sql
   INSERT INTO table_name (column1, column2, ...) VALUES (value1, value2, ...), (value3, value4, ...);
   ```

3. 查询数据：

   ```sql
   SELECT column1, column2 FROM table_name WHERE condition;
   ```

4. 更新数据：

   ```sql
   UPDATE table_name SET column1 = value1, column2 = value2 WHERE condition;
   ```

5. 删除数据：

   ```sql
   DELETE FROM table_name WHERE condition;
   ```

6. 清空表（保留结构，删除数据）：

   ```sql
   TRUNCATE TABLE table_name;
   ```

### 四、查询操作命令

1. 查询所有数据：

   ```sql
   SELECT * FROM table_name;
   ```

2. 带条件查询：

   ```sql
   SELECT column1, column2 FROM table_name WHERE condition;
   ```

3. 排序查询：

   ```sql
   SELECT column1, column2 FROM table_name ORDER BY column1 [ASC|DESC];
   ```

4. 限制查询结果：

   ```sql
   SELECT column1, column2 FROM table_name LIMIT offset, row_count;
   ```

5. 分组查询：

   ```sql
   SELECT column1, COUNT(*) FROM table_name GROUP BY column1;
   ```

6. 带条件分组查询：

   ```sql
   SELECT column1, COUNT(*) FROM table_name GROUP BY column1 HAVING condition;
   ```

7. 连接查询（INNER JOIN）：

   ```sql
   SELECT table1.column1, table2.column2
   FROM table1
   INNER JOIN table2 ON table1.common_field = table2.common_field;
   ```

8. 左连接（LEFT JOIN）：

   ```sql
   SELECT table1.column1, table2.column2
   FROM table1
   LEFT JOIN table2 ON table1.common_field = table2.common_field;
   ```

### 五、索引操作命令

1. 创建索引：

   ```sql
   CREATE INDEX index_name ON table_name (column_name);
   ```

2. 删除索引：

   ```sql
   DROP INDEX index_name ON table_name;
   ```

3. 查看表的索引：

   ```sql
   SHOW INDEX FROM table_name;
   ```

### 六、视图操作命令

1. 创建视图：

   ```sql
   CREATE VIEW view_name AS SELECT column1, column2 FROM table_name WHERE condition;
   ```

2. 查看视图：

   ```sql
   SELECT * FROM view_name;
   ```

3. 删除视图：

   ```sql
   DROP VIEW view_name;
   ```

### 七、事务管理命令

1. 启动事务：

   ```sql
   START TRANSACTION;
   ```

2. 提交事务：

   ```sql
   COMMIT;
   ```

3. 回滚事务：

   ```sql
   ROLLBACK;
   ```

### 八、用户管理命令

1. 创建用户：

   ```sql
   CREATE USER 'username'@'host' IDENTIFIED BY 'password';
   ```

2. 删除用户：

   ```sql
   DROP USER 'username'@'host';
   ```

3. 修改用户密码：

   ```sql
   ALTER USER 'username'@'host' IDENTIFIED BY 'new_password';
   ```

4. 查看当前用户：

   ```sql
   SELECT USER();
   ```

5. 授予权限：

   ```sql
   GRANT ALL PRIVILEGES ON database_name.* TO 'username'@'host';
   ```

6. 撤销权限：

   ```sql
   REVOKE ALL PRIVILEGES ON database_name.* FROM 'username'@'host';
   ```

7. 刷新权限：

   ```sql
   FLUSH PRIVILEGES;
   ```

### 九、备份和恢复命令

1. 备份数据库： 

   使用 

   ```sql
   mysqldump
   ```

    命令：

   ```sql
   mysqldump -u username -p database_name > backup_file.sql
   ```

2. 恢复数据库： 

   使用 

   ```
   mysql
   ```

    命令：

   ```sql
   mysql -u username -p database_name < backup_file.sql
   ```

### 十、其他常用命令

1. 查看当前 MySQL 版本：

   ```sql
   SELECT VERSION();
   ```

2. 显示当前连接的用户：

   ```sql
   SHOW PROCESSLIST;
   ```

3. 查看表的大小：

   ```sql
   SELECT table_name, (data_length + index_length) / 1024 / 1024 AS 'Size in MB'
   FROM information_schema.tables
   WHERE table_schema = 'database_name';
   ```