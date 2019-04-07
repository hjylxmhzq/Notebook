#### 创建数据库

```sql
CREATE DATABASE DBNAME;
```

#### 创建表

```sql
CREATE TABLE table_name( 
   column1 datatype, 
   column2 datatype, 
   column3 datatype, 
   ..... 
   columnN datatype, 
   PRIMARY KEY( one or more columns ));
   
CREATE TABLE CUSTOMERS( 
   ID   INT              NOT NULL, 
   NAME VARCHAR (20)     NOT NULL, 
   AGE  INT              NOT NULL, 
   ADDRESS  CHAR (25) , 
   SALARY   DECIMAL (18, 2),        
   PRIMARY KEY (ID));
```

NOT NULL：无空值，表示此列中不存在空值

判断是否为空值不能用=，要用is null 或者is not null

IDENTITY(1,1)：MS SQL Server使用identity字段执行自动增量，其中第一个数为起始值，第二个数为每次增量

```sql
CREATE TABLE table_name(
	ID INT IDENTITY(1,1) PRIMARY KEY,
	NAME VARCHAR(20)
);
```

#### 删除表

```sql
DROP TABLE table_name;
```

#### 添加记录(INSERT)

```sql
INSERT INTO TABLE_NAME [(column1,column2,...)]
VALUES (value1,value2,...)
```

如果插入所有列那么也可以不指定插入的列，但要保证插入值的顺序与默认值相同

```sql
INSERT INTO table_name VALUES (value1,value2,...)
--例
INSERT INTO CUSTOMERS VALUES (7, 'Muffy', 24, 'Indore', 10000.00 );
INSERT INTO CUSTOMERS VALUES (2, 'Khilan', 25, 'Delhi', 20000.00 );
```

也可以通过另一个表的值填充表

```sql
INSERT INTO first_table_name
	SELECT col1,col2,...
		FROM second_table_name
		[WHERE condition];
```

#### 查询记录(SELECT)

选择表中的任意列

```sql
SELECT col1, col2 FROM table_name;
```

也可以选择所有列

```sql
SELECT * FROM table_name
--例
SELETE * FROM CUSTOMERS;
```

#### 修改记录(UPDATE)

通过set和where语句结合修改特定的记录

```
UPDATE table_name
SET column1=value1, column2=value2
WHERE [condition];
--例
UPDATE CUSTOMERS
SET ID=6， NAME='Ramesh'
WHERE ID = 7;
```

当没有where条件控制时会修改所有记录的对应列的值

#### 删除记录(DELETE)

使用delete from配合where条件控制语句删除特定记录

```sql
DELETE FROM table_name
WHERE [condition];
--例
DELETE FROM CUSTOMERS
WHERE ID = 6;
```

#### 条件查询(WHERE)

where条件查询语句可以配合update，select，delect使用

```sql
SELECT ID, NAME
FROM CUSTOMERS
WHERE NAME = 'Ramesh' and ID = 6
```

and,or,not为逻辑运算符

#### 模糊查询(LIKE)

like模糊查询使用两个通配符(%和_)进行运算

%：零个或一个或多个字符

_：单个数字或字符

```sql
SELECT columns FROM table_name
WHERE column LIKE '%xxx';
--例
SELECT NAME FROM CUSTOMERS
WHERE column LIKE '%xxx';
```

#### 排序(ORDER BY)

order by子句可以根据一个或多个列对数据进行排序，一般默认为升序排序[ASC]，也可以按降序排序[DESC]

```sql
SELECT * FROM CUSTOMERS
	ORDER BY NAME, SALARY DESC
```

#### 分组查询(GROUP BY)

group by对数据分组并对已分组的数据进行处理，group by子句需要放在where子句后，order by子句前

```sql
SELECT NAME, SUM(SALARY) FROM table_name
GROUP BY NAME 
```

#### 去重复(DISTINCT)

```sql
--例
SELECT DISTINCT SALARY FROM CUSTOMERS
	ORDER BY SALARY
--salary列中只有唯一值
```

#### 连接表(JOIN)

通过join对表进行连接，join跟在from后面

```sql
SELECT col1, col1
FROM table_name1 INNER JOIN table_name2 ON table_name1.col = table_name2.col 
```

自连接

```sql
SELECT A, B
FROM table1, table2
WHERE condition
```

其中连接的类型有

- **INNER JOIN** -当两个表中都有匹配项时返回行

- **LEFT JOIN** -返回左侧表中的所有行，即使右表中没有匹配项

- **RIGHT JOIN** -返回右表中的所有行，即使左表中没有匹配项

- **FULL JOIN** -在其中一个表中存在匹配项时返回行

- **SELF JOIN** -这用于将表连接到自身，就像该表是两个表，临时重命名MS SQL Server语句中的至少一个表

- **CARTESIAN JOIN** -返回两个或多个联接表中的记录集的笛卡尔乘积

#### 返回特定数量记录(SELECT TOP)

```sql
SELECT TOP number|percent column_name(s)
FROM table_name
WHERE condition;
```

select top与mysql中的limit和oracle中的rownum等价

#### 修改table(ALTER TABLE)

alter table可以改变列名，列的数据类型，删除列和增加列

```sql
--增加列
ALTER TABLE table_name
ADD col_name datatype
--删除列
ALTER TABLE table_name
DROP COLUMN col_name
--修改列的数据类型
ALTER TABLE tbale_name
ALTER COLUMN col_name datatype
```

#### SQL约束

SQL约束一般包括

- NOT NULL
- UNIQUE
- PRIMARY KEY
- FOREIGN KEY
- DEFAULT
- CHECK

##### 非空约束(NOT NULL)

NOT NULL 约束强制列不接受 NULL 值

```sql
CREATE TABLE Persons(
P_Id int NOT NULL,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255)
);
```

##### 唯一约束(UNIQUE)

UNIQUE 约束唯一标识数据库表中的每条记录

```sql
CREATE TABLE Persons(
	P_Id int NOT NULL UNIQUE
);
```

命名unique约束并对多个列进行unique约束

```sql
CREATE TABLE Persons(
	P_Id INT NOT NULL,
	NAME VARCHAR(20) NOT NULL,
	CONSTRAINT UC_PERSONID UNIQUE(P_Id, NAME)
);
```

通过alter添加unique约束

```sql
ALTER TABLE Persons
ADD UNIQUE(P_Id)
--对多列添加约束，并对约束命名
ALTER TABLE Persons
ADD CONSTRAINT UC_PERSONID UNIQUE(P_ID, NAME)
```

通过alter删除unique约束

```sql
ALTER TABLE Persons
DROP CONSTRAINT UC_PERSONID
```

##### 主键约束(PRIMARY KEY)

PRIMARY KEY 约束唯一标识数据库表中的每条记录

主键列不能包含 NULL 值

每个表都应该有一个主键，并且每个表只能有一个主键

```sql
CREATE TABLE Persons(
	P_Id INT NOT NULL PRIMARY KEY,
	NAME VARCHAR(20) NOT NULL
)
```

定义多列组成的primary key

```sql
CREATE TABLE Persons(
	P_Id INT NOT NULL,
	NAME VARCHAR(20) NOT NULL,
	CONSTRAINT pk_PERSONID PRIMARY KEY(P_Id, NAME)
)
```

使用alter添加主键

```sql
ALTER TABLE Persons
ADD UNIQUE(P_Id)
--添加多列组成的主键并对主键约束命名
ALTER TABLE Persons
CONSTRAINT PK_PERSONID PRIMARY KEY (P_Id,NAME)
```

删除主键约束

```
ALTER TABLE Persons
DROP PRIMARY KEY
--OR
ALTER TABLE Persons
DROP CONSTRAINT PK_PERSONID
```

#### 外键约束(FOREIGN KEY)

```sql
CREATE TABLE Orders (
    O_Id INT NOT NULL,
    P_Id Int,
    PRIMARY KEY (O_Id),
    FOREIGN KEY (P_Id) REFERENCES Persons(P_Id)
)
```

命名外键约束并定义由多列组成的外键

```sql
CREATE TABLE Orders (
    O_Id INT NOT NULL,
    P_Id Int,
    PRIMARY KEY (O_Id),
    CONSTRAINT FK_PERORDS FOREIGN KEY (P_Id)
    REFERENCES Persons (P_Id)
)
```

使用alter添加foreign key

```sql
ALTER TABLE Orders
ADD FOREIGN KEY (P_Id) 
REFERENCES Persons (P_Id)
--OR
ALTER TABLE Orders
ADD CONSTRAINT FK_PERORDS FOREIGN KEY (P_Id)
REFERENCES Persons (P_Id)
```

使用alter删除foreign key约束

```sql
ALTER TABLE Orders
DROP CONSTRAINT FK_PERORDS
```

#### 默认值约束(DEFAULT)

DEFAULT 约束用于向列中插入默认值

如果没有规定其他的值，那么会将默认值添加到所有的新记录

```sql
CREATE TABLE Persons(
	P_Id INT PRIMARY KEY,
	City VARCHAR(20) DEFAULT 'Sandnes'
)
--OR
CREATE TABLE Persons(
	P_Id INT PRIMARY KEY,
	Day DATE DEFAULT GETDATE() 
)
```

使用alter添加default约束

```sql
ALTER TABLE Persons
ADD CONSTRAINT DF_PERSON DEFAULT('Sandnes') FOR CITY
```

alter删除default约束

```sql
ALTER TABLE Persons
ALTER COLUMN City DROP DEFAULT
```

#### UNION子句

通过union子句可以将多个select结果组合起来，不过组合的表必须列数相同且数据类型相同，结合的结果表不包含重复表

```sql
SELECT col1
FROM table1
WHERE [condition]
UNION
SELECT col1
FROM table2
WHERE [condition]
```

UNION ALL

通过union all子句可以将表中所有数据联合起来，包括重复的数据

```sql
SELECT col1
FROM table1
WHERE [condition]
UNION ALL
SELECT col1
FROM table2
WHERE [condition]
```

#### SQL索引

##### 单列索引

```sql
CREATE INDEX index_name 
ON table(col);
```

##### 唯一索引

唯一索引能提高查询速度也能保证数据完整性，唯一索引列不允许有重复值

```sql
CREATE UNIQUE INDEX index_name
ON table(col)
```

##### 聚簇索引

聚簇索引建立在两个或以上的列上

```sql
CREATE INDEX index_name
ON table(col1, col2)
```

##### 删除索引

```sql
DROP INDEX table.index_name
```

#### 子查询

子查询可以在 SELECT、INSERT、UPDATE 和 DELETE 语句中，同 =、<、>、>=、<=、IN、BETWEEN 等运算符一起使用

##### SELECT语句中的子查询

```sql
SELECT * 
FROM table_name
WHERE ID IN (SELECT ID
			 FROM table1_name
			)
```

##### INSERT语句中的子查询

```sql
INSERT INTO table_name col1, col2
			SELECT col1, col2
			FROM table1
			WHERE [condition]
```

##### UPDATE语句中的子查询

```sql
UPDATE CUSTOMERS
SET SALARY=SALARY*0.25
WHERE AGE IN (SELECT AGE FROM CUSTOMERS_BKP WHERE AGE >= 27)
```

##### DELETE中的子查询

```sql
DELETE FROM table_name
WHERE AGE IN (SELECT AGE FROM table1_name WHERE AGE > 27)
```

