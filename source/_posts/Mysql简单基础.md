-------------
title: Mysql简单基础
date: 2018-02-07 23:37:00
-------------
#### 进入数据库:
mysql -u root

#### 创建数据库：
CREATE DATABASE mysql_shiyan;

#### 查看数据库：
show databases;

#### 连接数据库：
use mysql_shiyan;

#### 删除数据库:
DROP DATABASE 数据库名字;

#### 查看数据表：
show tables;

#### 新建数据表：
CREATE TABLE 表的名字
(
     列名a 数据类型(数据长度),
     列名b 数据类型(数据长度)，
     列名c 数据类型(数据长度)
)

#### 重命名数据表：
RENAME TABLE 原名 TO 新名字;   ALTER TABLE 原名 RENAME 新名;  ALTER TABLE 原名 RENAME TO 新名;

#### 删除数据表：
DROP TABLE 表名字;

#### 数据表增加一列：
ALTER TABLE 表名字 ADD 列名字 数据类型 约束;
ALTER TABLE 表名字 ADD 列名字 数据类型 约束 AFTER 列1;(加入到列1后面,FIRST则为加入在第一列)

#### 数据表删除一列：
ALTER TABLE 表名字 DROP 列名字;

#### 数据表重命名一列：
ALTER TABLE 表名字 CHANGE 原列名 新列名 数据类型 约束;

#### 数据表改变数据类型：
ALTER TABLE 表名字 MODIFY 列名字 新数据类型;

#### 数据表修改表中某个值：
UPDATE 表名字 SET 列1=值1,列2=值2 WHERE name="Tom";

#### 数据表删除一行记录：
DELETE FROM 表名字 WHERE name="Tom" ;

#### 数据类型：
| 数据类型       | 大小(字节)    |  用途  |  格式  |
| --------   | :-----   | :---- | :-------- |
| INT| 4      |   整数    | |
| FLOAT| 4      |   单精度浮点数    | |
| DOUBLE| 8      |   双精度浮点数    | |
| ENUM|       |   单选,比如性别    |ENUM('a','b','c') |
| SET|       |   多选    | SET('1','2','3')|
| DATE| 3      |   日期    | YYYY-MM-DD|
| TIME| 3      |   时间点或持续时间    | HH:MM:SS|
| YEAR|  1     |   年份值    |YYYY |
| CHAR|  0~255     |   定长字符串  | |
| VARCHAR|  0~255     |   变长字符串    | |
| TEXT|  0~65535     |   长文本数据  | |

#### 插入数据：
INSERT INTO 表的名字( 列名a, 列名b, 列名c) VALUES (值1,值2,值3);

#### 约束分类：
| 约束类型：      | 主键    |  默认值  |  唯一  | 外键 | 非空 |
| --------   | :-----   | :---- | :--------| :------ | :----- |
| 关键字：| PRIMARY KEY      |   DEFAULT    | UNIQUE| FOREIGN KEY | NOT NULL|

#### 基本的SELECT语句：
SELECT 要查询的列名(*号为查询全部) FROM 表名字 WHERE 限制条件;

##### 1.数学符号查询：
SELECT name,age FROM employee WHERE age>25; (查出age>25的数据)
SELECT name,age,phone FROM employee WHERE name='Mary'; (查出一个name="Mary"的数据)

##### 2.“AND”与“OR”：
SELECT name,age FROM employee WHERE age<25 OR age>30;(查出一个小于25或大于30，AND是与)

##### 3. IN 和 NOT IN：
SELECT name,age,phone,in_dpt FROM employee WHERE in_dpt IN ('dpt3','dpt4'); (查询 in_dpt 为 dpt3 或 dpt4的人)
SELECT name,age,phone,in_dpt FROM employee WHERE in_dpt NOT IN ('dpt1','dpt3'); ( 查询 in_dpt 既不是 dpt1 又不是 dpt3 的信息)

##### 4.通配符：
SQL中的通配符是 _ 和 % 。其中 _ 代表一个未指定字符，% 代表不定个未指定字符。
SELECT name,age,phone FROM employee WHERE phone LIKE '1101__';(查询开头为1101,后面两位未定义的数据)
SELECT name,age,phone FROM employee WHERE name LIKE 'J%';(查询名字为J开头的)

##### 5.对结果排序：
ORDER BY的结果是升序排列，而使用关键词ASC和DESC可指定升序或降序排序。
SELECT name,age,salary,phone FROM employee ORDER BY salary DESC;(按salary字段降序排列)

##### 6.SQL 内置函数和计算：
| 函数名：      | COUNT    |  SUM  |  AVG  | MAX | MIN |
| --------   | :-----   | :---- | :--------| :------ | :----- |
| 作用：| 计数      |   求和    | 求平均值| 最大值 | 最小值|

##### 7.子查询：(嵌套)
SELECT cust_id
FROM orders
WHERE order_num IN (SELECT order_num
                    FROM orderitems
                    WHERE prod_id = "TNT2"); (嵌套查询)

##### 8.连接查询：
SELECT id,name,people_num
FROM employee,department
WHERE employee.in_dpt = department.dpt_name
ORDER BY id;

或者使用JOIN ON
SELECT id,name,people_num
FROM employee JOIN department
ON employee.in_dpt = department.dpt_name
ORDER BY id;

#### 视图：
CREATE VIEW 视图名(列a,列b,列c) AS SELECT 列1,列2,列3 FROM 表名字;

#### 导入：
LOAD DATA INFILE '文件路径和文件名' INTO TABLE 表名字;

#### 导出：
SELECT 列1，列2 INTO OUTFILE '文件路径和文件名' FROM 表名字;

#### 备份：
mysqldump -u root mysql_shiyan(数据库名 表名字) > bak.sql(备份文件名);

#### 恢复：
mysql -u root 数据库名 < bak.sql(备份的文件名);