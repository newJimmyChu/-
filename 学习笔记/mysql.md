# Mysql学习笔记（深入浅出Mysql）

[TOC]

### 2.2 MySQL入门

三大SQL语句类：

1. DDL（Data Definition Language）语句：数据定义语言（包含create, drop, alter等）

2. DML（Data Manipulation Language）语句：数据操纵语言（包含insert，delete，update，select等）
3. DCL（Data Control Language）语句：数据控制语句，用于控制许可和访问级别的语句（包含grant，revoke等）

#### 2.2.2 DDL语句

**创建数据库 & 查看数据库：** 

```mysql
create database mydb;  #创建数据库mydb
show databases;        #展示数据库
USE mydb;			   #切换并使用数据库
show tables; 		   #展示当前数据库中的所有数据库表
drop database mydb;    #删除mydb数据库
```

**表的基本操作**：

```mysql
create mytable(name varchar(10).birthday date,id int(2));
#创建一个名为mytable的表
desc mytable;   #查看表的定义
show create table newtable;   #查看详细信息
drop table newtable;    #删除表newtable

#修改表newtable的列name
alter table newtable modify name varchar(20); 

#增加同一个新的表字段newcol
alter table newtable add column newcol int(2);

#删除一个表字段name
alter table newtable drop column name;

#字段改名 (change可以修改列名称)
alter table newtable change column name name1 int(4);

#修改字段排列顺序(加在某项字段之后)
alter table newtable add birthdate date after name;
#修改字段age并放在最前面(使用first)
alter table newtable modify age int(3) first;

#修改表名称
alter table newtable rename mytable;
```

#### 2.2.3 DML语句

**1. 插入记录**：

```mysql
#方式1: 制定字段名称
insert into mytable (id,name,sal,birth) values(103.'Jimmy'.80000.2009-01-23);
#方式2： 不指定字段名称
insert into mytable values(120.'Jack'.90000,2020-01-01);

#对于非空字段但是有默认值或者可空字段，可以不在insert后出现
insert into mytable (id, birth) values(120, 2020-01-01);

#insert插入多条记录
insert into mytable values(100,'Jack'),(101,'Jimmy'),
(102,'Cyan');
```

**2. 更新记录**

```mysql
update mytable set id=108 where name='Jimmy'

#update更新多个表中数据
#同时更新了表mytable和表newtable中的数据
update mytable a, newtable b set a.sal=b.sal*a.sal,b.name=a.name where a.id=b.id;
```

**3. 删除记录**

```mysql
delete from mytable where id=101;

#删除多个表中数据(不加where条件会删除所有记录，需要多加小心)
delete a,b from mytable a, newtable b where a.id=b.id and a.name=b.name;
```

**4. 查询记录**

```mysql
select * from mytable where id=101 and name='jimmy';

#1.基本查询 & 去重复查询
select distinct * from mytable

#查询部分字段
select id,name from mytable;

#2.条件查询(查询id=101的记录)
select * from mytable where id=101;
#查询id>101的记录
select * from mytable where id>101;

#3.排序和限制(asc or desc)
select * from mytable where id>101 order by salary asc;

#4.LIMIT 语法为SQL扩展SQL92的语法，其他数据库不通用
#只显示部分使用limit语法(LIMIT offset_start, row_count)
#offset表示开始的偏移量，默认值为0
select * from mytable where id>101 order by sal limit 3;
#offset=1表示从第二条记录开始显示
select * from mytable where id>101 order by sal limit 1,3;

#5.聚合(常用sum, count, max, min)
#having和where的区别：where在聚合前过滤，having在聚合后过滤

#统计总人数
select count(1) from mytable;
#统计每个部门的人数
select deptid,count(1) from mytable group by deptid;
#统计每个部门人数后再汇总(额外显示总数)
select deptid,count(1) from mytable group by deptid with rollup;
#统计人数大于1的部门
select deptid,count(1) from mytable group by deptid having count(1)>1;

#统计薪水总额，最高薪水和最低薪水
select sum(sal),max(sal),min(sal) from mytable;

#6.表连接(需要显示多个表中字段)
select ename,depnmame from etable,deptable where etable.depid=deptable.depid;

#左连接: 包含所有左边表中的记录甚至是右边表中没有匹配的记录
#右连接: 包含所有右边表中的记录甚至是左边表中没有匹配的记录
select ename,depname from etable left join deptable on etable.depid=deptable.depid;
#等效右连接
select ename,depname from deptable right join etable on deptable.depid=etable.depid;

#7.子查询
#关键字: in, not in, =, !=, exists, not exists
select * from etable where depid in(select depid from deptable);
#如果子查询记录唯一，可以用=代替
select * from etalbe where depid = (select depid from deptable limit 1);

#记录联合
#关键字: union和union all
#union all直接合并结果，union则是在union all后进行distinct操作
select * from etalbe
union all
select * from deptable;
```

#### 2.2.4 DCL语句

DCL语句主要用于DBA管理数据库中的对象权限，开发人员较少使用

```mysql
#使用户z1获得对数据库mydb的select和insert权限
grant select,insert on mydb.* to 'z1'@'localhost' identified by '123';
#revoke收回权限
revoke insert on mydb.* from 'z1'@'localhost';
```



### 3. MySQL 支持的数据类型

#### 3.1 数值类型

| 整数类型                | 字节 | min                      | max                      |
| ----------------------- | ---- | ------------------------ | ------------------------ |
| TINYINT                 | 1    | -128 & 0                 | 127 & 255                |
| SMALLINT                | 2    | -32768 & 0               | 32767 & 65535            |
| MEDIUMINT               | 3    | -8388608 & 0             | 8388607 & 1677215        |
| INT INTEGER             | 4    | -2^31                    | 2^31 - 1 & 2^32 - 1      |
| BIGINT                  | 8    | -2^63                    | 2^63 - 1 & 2^64 - 1      |
| FLOAT                   | 4    | ±1.175494351E-38         | ±3.402823466E+38         |
| DOUBLE                  | 8    | ±2.2250738585072014E-308 | ±1.7976931348623157E+308 |
| DEC(M,D) & DECIMAL(M,D) | M+2  | 最大取值与DOUBLE相同     | 有效范围由M,D决定        |
| BIT(M)                  | 1-8  | BIT(1)                   | BIT(64)                  |

**小括号指定宽度：**

int(5)表示当数值宽度小于5位的时候在数字前面填充宽度，不指定宽度默认为int(11)

zerofill：数字位数不够使用字符"0"来填充

AUTO_INCREMENT：在需要产生id或者唯一标识时，可以使用该属性自动加一，并且需要将属性定义为NOT NULL 和 PRIMARY KEY 或者 UNIQUE

```mysql
create table tab (id int auto_increment not null primary key);
create table tab (id int auto_increment not null primary key(id));
create table tab (id int auto_increment not null unique(id));
```

浮点数和定点数(M,D)表示一共显示M位数字，其中D位位于小数点后

float(7,4)最多可以显示999.9999，多出的位近似。如999.00009->999.0001

(M,D)为非标准用法，加入需要数据库迁移，不推荐使用

decimal在不指定精度时，默认(10,0)来使用，通常用于精度要求非常高的计算中（银行等）



**精度：**

1. 如果浮点数不标注精度，则会按照实际精度显示；如果标有精度，则会四舍五入后插入结果，并且不会报错
2. 定点数如果不写精度，则会按照DEC(10,0)来计算；反之超过精度会导致系统报错

BIT(M)：用来存放二进制数，M可取1~64，默认为1位。直接使用select不会显示结果，需要用bin()或者hex()函数读取

```mysql
#通过hex和bin函数读取二进制数
select hex(id),bin(id) from mytable;
```

在插入bit类型字段时时首先转换为二进制数，如果位数超标，则插入失败

#### 3.2 日期时间类型

| 日期和时间类型 | 字节 | min                 | max                 |
| -------------- | ---- | ------------------- | ------------------- |
| DATE           | 4    | 1000-01-01          | 9999-12-31          |
| DATETIME       | 8    | 1000-01-01 00:00:00 | 9999-12-31 23:59:59 |
| TIMESTAMP      | 4    | 19700101080001      | 2038年某时刻        |
| TIME           | 3    | -838:59:59          | 838:59:59           |
| YEAR           | 1    | 1901                | 2155                |

TIMESTAMP字段默认的初始值为CURRENT_TIMESTAMP，插入一个null的timestamp会直接变为当前时间的时间戳

如果有第二个时间戳，则默认值为0000-00-00 00:00:00 (如果插入时间戳溢出也会被赋予该值)

第二个时间戳的默认值不能为current_timestamp，强制修改会报错

**时区：**

系统默认时区为SYSTEM所在的时区

```mysql
show variables like 'time_zone';   #查询time_zone
set time_zone='+9:00';             #设置time_zone
```

TIMESTAMP 和 DATETIME 区别：

1. TIMESTAMP支持的范围较小
2. TIMESTAMP插入和查询受时区影响，DATETIME不受时区影响
3. TIMESTAMP的属性收到MySQL和SQLMode的版本影响很大

YEAR：两位数时00-69表示2000-2069；70-99表示1970-1999

```mysql
#插入不同格式的“2007-9-3 12:10:10”
#1.'YYYY-M-M HH-MM-SS'
insert into tab values('2020-9-3 12:10:10');
#2.允许使用不同的分隔符
insert into t6 values('2007/9/3 12+10+10');
#3.'YYYYMMDDHHMMSS'
insert into t6 values('20070903121010');
#4.纯数字(非字符串)
insert into t6 values(20070903121010);
```

![1597904793554](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1597904793554.png)

#### 3.3.1 CHAR 和 VARCHAR 类型

都用来保存较短字符串

**对比：**

1. CHAR长度固定，为0-255之间的任意值
2. VARCHAR为可变长度，长度可以为0-255或者65535之间的值
3. 在检索时，CHAR删除尾部空格，VARCHAR保留空格

#### 3.3.2 BINARY 和 VARBINARY 类型

包含二进制字符串，不包含非二进制字符串

#### 3.3.3 ENUM 类型

枚举类型，值范围需要在创建时指定

成员数量在1-255时需要一字节，255-65535时需要两字节，最多有65535个成员

ENUM忽略大小写，并且在超出范围时自动插入/返还第一个值

```mysql
#创建一个枚举类型的字段，并插入三个个值
create table t (gender enum('M','F'));
insert into t values('M'),('1'),(null);
```

#### 3.3.4 SET 类型

类型存储大小：

1. 1-8个成员：1字节
2. 9-16个成员：2字节
3. 17-24个成员：3字节
4. 25-32个成员：4字节
5. 33-64个成员：8字节

SET和ENUM的区别：SET可以一次性选取多个成员，而ENUM则只能选取一个

```mysql
create table t (s set ('a','b','c'));
insert into t values('a','b'),('a','b','a');
```



### 4. MySQL 中的运算符

#### 4.1 算数运算符

| 运算符  | 作用 |
| ------- | ---- |
| +       | 加法 |
| -       | 减法 |
| *       | 乘法 |
| /, DIV  | 除法 |
| %， MOD | 余数 |

**比较运算符：**

| BETWEEN        | 作用               |
| -------------- | ------------------ |
| BETWEEN        | 在范围内           |
| IN             | 在指定集合内       |
| IS NULL        | 为NULL             |
| IS NOT NULL    | 不为NULL           |
| LIKE           | 通配符匹配         |
| REGEXP / RLIKE | 正则表达式匹配     |
| <>             | 检查不等，与=相反  |
| <=>            | 在NULL时也可以比较 |

```mysql
#between用法: a between min and max
select 20 between 50 and 70, 10 between 10 and 20;

#in用法: a in (val1,val2,val3)
select 1 in (1,2,3);

#like用法: a like %123% 表示包含123返回1，否则返回0
select 12345 like '%123%',1223 like '%122',14422 like '422%';

#regexp用法: str regexp str_pat
select 'abcdef' regexp 'ab' ,'abcdefg' regexp 'k';
```



#### 4.3 逻辑运算符

| 运算符     | 作用 |
| ---------- | ---- |
| NOT 或 !   | not  |
| AND 或 &&  | and  |
| OR 或 \|\| | or   |
| XOR        | xor  |

#### 4.4 位运算符

| 运算符 | 作用   |
| ------ | ------ |
| &      | 位AND  |
| \|     | 位OR   |
| ^      | 位XOR  |
| ~      | 位取反 |
| >>     | 位右移 |
| <<     | 位左移 |



### 5. 常用函数

#### 5.1 字符串函数

**CONCAT(S1,S2,S3)：**连接输入字符串

```mysql
#aaabbbccc aaa
select concat('aaa','bbb','ccc'),concat('aaa',null);
```

**INSERT(str, x, y, instr)：**将str从x位置开始，y长度的子串替换为instr

```mysql
#nedbdr
select insert('newstr',2,3,'dbd')
```

**LOWER(str) or UPPER(str)：**转换字符串为大写或者小写

**LEFT(str,x) or RIGHT(str，x)：**返还最左边或者最右边的的x个字符

**LPAD(str,n,pad) or RPAD(str,n,pad)：**用pad对最左边或者最右边进行填充，填充n个字符长度（允许重复）

```mysql
#bbbbbbbaaa
lpad('aaa',10,'bb');
```

**TRIM(str)：**去除两遍空格

**LTRIM(str) or RTRIM(str)：****去掉左边或者右边的空格

**REPEAT(str,x)：**返还str重复x次的结果

**REPLACE(str,a,b)：**用字符串b替换str中所有出现的字符串a

```mysql
#mydb_
select replace('mydb_2002','_2002','_');
```

**STRCMP(s1,s2)：**如果s1比s2大，返回1，相等返回零，小于返回-1

**SUBSTRING(str,x,y)：**返回从字符串str中第x位置起y个长度的子串

```mysql
#2008
select substring('beijing2008',8,4);
```

#### 5.2 数值函数

**RAND()：**返还0-1的随机数

**ROUND(x,y)：**返还x的四舍五入y位小数的值

**TRUNCATE(x,y)：**返还数字x截断为y位的小数

#### 5.3 时间函数

**CURDATE()：**当前时间

**HOUR(time)：**返还小时

**DATE_FORMAT(date,fmt)：**返回格式化date值

**DATEDIFF(expr,expr2)：**返还二者之间的天数

#### 5.4 流程函数

**IF(value,t,f)：**如果value为真，返还t，否则返还f

**IFNULL(value1,value2)：**如果value1不为NULL返还1，否则返还2

**CASE WHEN [value1]**
**THEN[result1]…ELSE[default]END：**如果v1为真，返还result1，否则返还default

**CASE [expr] WHEN [value1]**
**THEN[result1]…ELSE[default]END：**如果expr=value1，返还result1，否则返还default

```mysql
#if示例
select if(salary>2000,'high','low') from employee;

```

