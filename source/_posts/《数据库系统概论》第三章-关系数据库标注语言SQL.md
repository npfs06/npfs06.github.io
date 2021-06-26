---
title: 《数据库系统概论》第三章 关系数据库标注语言SQL
date: 2021-03-29 23:39:35
updated: 2021-03-29 23:39:35
categories:
  - [share]
  - [数据库]
---

《数据库系统概论》第三章 关系数据库标注语言SQL<!--more-->

# 思维导图

![](https://img.npfs06.top/20210329234324.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

# SQL概述

## 什么是SQL?

- SQL（Structured Query Language）结构化查询语言，是关系数据库的标准语言
- SQL是一个通用的、功能极强的关系数据库语言

## SQL的特点

**① 综合统一**

- 集数据定义语言 DDL(Data Definition Language)，数据操纵语言 DML（Data Manipulation Language），数据控制语言 DCL(Data Control Language) 功能于一体。
- 可以独立完成数据库生命周期中的全部活动：
  - 定义关系模式，插入数据，建立数据库；
  - 对数据库中的数据进行查询和更新；
  - 数据库重构和维护
  - 数据库安全性、完整性控制等
- 用户数据库投入运行后，可根据需要随时逐步修改模式，不影响数据的运行。
- 数据操作符统一

**② 高度非过程化**

- 非关系数据模型的数据操纵语言“面向过程”，必须制定存取路径
- SQL只要提出“做什么”，无须了解存取路径。
- 存取路径的选择以及SQL的操作过程由系统自动完成。

**③ 面向集合的操作方式**

- 非关系数据模型采用面向记录的操作方式，操作对象是一条记录
- SQL采用集合操作方式：
  - 操作对象、查找结果可以是元组的集合
  - 一次插入、删除、更新操作的对象可以是元组的集合

**④ 以同一种语法结构提供多种使用方式**

- SQL是独立的语言
  - 能够独立地用于联机交互的使用方式
- SQL又是嵌入式语言
  - SQL能够嵌入到高级语言（例如C，C++，Java）程序中，供程序员设计程序时使用

**⑤ 语言简洁，易学易用**

- SQL功能极强，完成核心功能只用了9个动词。

| SQL功能  | 动词                   |
| -------- | ---------------------- |
| 数据查询 | SELECT                 |
| 数据定义 | CREATE、DROP、ALTER    |
| 数据操纵 | INSERT、UPDATE、DELETE |
| 数据控制 | GRANT、REVOKE          |

## SQL的基本概念

- SQL支持关系数据库三级模式结构

![](https://img.npfs06.top/20210329234343.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)
**基本表**

- 本身独立存在的表
- SQL中一个关系就对应一个基本表
- 一个(或多个)基本表对应一个存储文件
- 一个表可以带若干索引

**存储文件**

- 逻辑结构组成了关系数据库的内模式
- 物理结构是任意的，对用户透明

**视图**

- 从一个或几个基本表导出的表
- 数据库中只存放视图的定义而不存放视图对应的数据
- 视图是一个虚表
- 用户可以在视图上再定义视图

# 数据字典

- `数据字典`是`关系数据库管理系统内部`的一组`系统表`，`它记录了数据库中所有的定义信息，包括关系模式定义、视图定义、索引定义、完整性约束定义、各类用户对数据库的操作权限、统计信息等`。
- 关系数据库管理系统在执行SQL的数据定义语句时，实际上就是在更新数据字典表中的相应信息。
- 在进行查询优化和查询处理时，数据字典中的信息是其重要依据。

# 数据定义

## 数据定义概览

- SQL的数据定义功能: `模式定义`、`表定义`、`视图`和`索引`的定义

![](https://img.npfs06.top/20210329234357.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

## 模式的定义与删除—SCHEMA

**定义模式实际上定义了一个`命名空间`**

- 在这个`空间`中可以`定义`该`模式`包含的`数据库对象`，例如`基本表`、`视图`、`索引`等。
- 在CREATE SCHEMA中可以接受CREATE TABLE，CREATE VIEW和GRANT子句。
- `CREATE SCHEMA <模式名> AUTHORIZATION <用户名>[<表定义子句>|<视图定义子句>|<授权定义子句>]`
- 如果没有指定<模式名>，那么<模式名>隐含为<用户名>

### 定义模式

> dbo database owner 数据库的创建者,创建该对象的用户. guest 顾客 能够访问数据库中对象的数据, 要求dbo分配权限给guest, 一般给他查看的权限select

- 这里我先创建一个数据库用户：
  ![](https://img.npfs06.top/20210329234410.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

**[例1]定义一个学生-课程模式S-T**

```sql
  CREATE SCHEMA "S-T" AUTHORIZATION BitHachi;
  为用户BitHachi定义了一个模式S-T
```

![](https://img.npfs06.top/20210329234427.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

- 如果没有指定<模式名>，那么<模式名>隐含为<用户名>

```sql
CREATE SCHEMA AUTHORIZATION BitHachi；
<模式名>隐含为用户名BitHachi
这个不知道咋回事，没出结果，默认模式BitHachi没创建出来，没显示
```

**[例2]为用户BitHachi创建了一个模式S-T，并在其中定义了一个表TAB1。**

```sql
CREATE SCHEMA "S-T" AUTHORIZATION BitHachi
CREATE TABLE TAB1(
COL1 SMALLINT, 
COL2 INT,
COL3 CHAR(20),
COL4 NUMERIC(10,3),
COL5 DECIMAL(5,2)
);
```

![](https://img.npfs06.top/20210329234453.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

### 删除模式

- `DROP SCHEMA <模式名> <CASCADE|RESTRICT>`
- `CASCADE(级联)`
  删除模式的同时把该模式中所有的数据库对象全部删除
- `RESTRICT(限制)`
  如果该模式中定义了下属的数据库对象（如表、视图等），则拒绝该删除语句的执行。
- 当该模式中没有任何下属的对象时才能执行。
- 以下是运行结果，至于为什么是错误的，暂且放在这里，等熟悉相关知识之后，再来解决。

```sql
DROP SCHEMA "S-T" CASCADE;
```

![](https://img.npfs06.top/20210329234503.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

```sql
DROP SCHEMA "S-T" RESTRICT;
```

![](https://img.npfs06.top/20210329234514.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

## 基本表的定义、删除与修改—TABLE

### 定义基本表的标准格式

```sql
CREATE TABLE <表名>(
      <列名> <数据类型>[ <列级完整性约束条件> ]
      [，<列名> <数据类型>[ <列级完整性约束条件>] ]
       ………
      [，<表级完整性约束条件> ]
 );
```

- `如果完整性约束条件涉及到该表的多个属性列，则必须定义在表级上，否则既可以定义在列级也可以定义在表级。`
- 示例：
  ![](https://img.npfs06.top/20210329234527.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)![](https://img.npfs06.top/20210329234539.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)
  ![](https://img.npfs06.top/20210329234550.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

### 数据类型

- SQL中`域`的概念用`数据类型`来实现
- 定义表的`属性`时 需要指明其`数据类型及长度`
- 选用哪种数据类型
  - 取值范围
  - 要做哪些运算
- 以下是通用数据类型，不同数据库的数据类型可能有所不同，可查相关文档。

![](https://img.npfs06.top/20210329234603.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

### 修改基本表

```sql
ALTER TABLE <表名>
[ ADD <新列名> <数据类型> [ 完整性约束 ] ]
[ DROP <完整性约束名> ]
[ ALTER COLUMN<列名> <数据类型> ]；
```

**[例8]向Student表增加“入学时间”列，其数据类型为日期型。**

- 不论基本表中原来是否已有数据，新增加的列一律为空值。

```sql
     ALTER TABLE Student ADD S_entrance DATE;
```

![](https://img.npfs06.top/20210329234615.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

**[例9]将年龄的数据类型由字符型（假设原来的数据类型是字符型）改为整数。**

```sql
ALTER TABLE Student ALTER COLUMN Sage INT;
```

![](https://img.npfs06.top/20210329234626.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)
**[例10]增加课程名称必须取唯一值的约束条件。**

```sql
ALTER TABLE Course ADD UNIQUE(Cname);
```

![](https://img.npfs06.top/20210329234640.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

### 删除基本表

**标准格式：**
`DROP TABLE <表名>［RESTRICT| CASCADE］；`

- RESTRICT：删除表是有限制的。

  - 欲删除的基本表不能被其他表的约束所引用
  - 如果存在依赖该表的对象，则此表不能被删除

- CASCADE：删除该表没有限制。

  - 在删除基本表的同时，相关的依赖对象一起删除

**[例11] 删除Student表**

- 基本表定义被删除，数据被删除

- 表上建立的索引、视图、触发器等一般也将被删除

- 还是和上述删除模式情况一样，加了CASCADE和RESTRUCT删除不了

  ```sql
    DROP TABLE  Student  CASCADE ;
  ```

**［例12］若表上建有视图，选择RESTRICT时表不能删除**

```sql
    CREATE VIEW IS_Student      
	AS 
	    SELECT Sno,Sname,Sage
	    FROM  Student
    	    WHERE Sdept='IS';
	 DROP TABLE Student RESTRICT;   
```

![](https://img.npfs06.top/20210329234830.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

```
--ERROR: cannot drop table Student because other objects depend on it
```

![](https://img.npfs06.top/20210329234841.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

**[例12]如果选择CASCADE时可以删除表，视图也自动被删除**

- 这里还是跟之前的情况一样，删除不了，可能是我用的数据库不同叭

```sql
	DROP TABLE Student CASCADE; 	    
 --NOTICE: drop cascades to view IS_Student
	SELECT * FROM IS_Student;
--ERROR: relation " IS_Student " does not exist 
```

![](https://img.npfs06.top/20210329234853.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

## 索引的建立与删除—INDEX

**建立索引的目的：`·加快查询速度·`**

- 谁可以建立索引？
  - DBA 或 表的属主（即建立表的人）
  - DBMS一般会自动建立以下列上的索引
  - PRIMARY KEY
  - UNIQUE
- 谁维护索引？
  DBMS自动完成
- 使用索引
  DBMS自动选择是否使用索引以及使用哪些索引
- RDBMS中索引一般采用B+树、HASH索引来实现
  - B+树索引具有动态平衡的优点
  - HASH索引具有查找速度快的特点
- 采用B+树，还是HASH索引 则由具体的RDBMS来决定
- 索引是关系数据库的内部实现技术，属于内模式的范畴
- `CREATE INDEX`语句定义索引时，可以定义索引是`唯一索引、非唯一索引或聚簇索引`

### 建立索引的标准格式

**语句格式**

```sql
CREATE [UNIQUE] [CLUSTER] INDEX <索引名> 
ON <表名>(<列名>[<次序>][,<列名>[<次序>] ]…)；	
```

**[例13]**

```sql
 	  CREATE CLUSTERED	INDEX Stusname
           ON   Student(Sname);
```

![](https://img.npfs06.top/20210329234925.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

- 在Student表的Sname（姓名）列上建立一个`聚簇索引`
- 在最经常查询的列上建立聚簇索引以提高查询效率
- `一个基本表上最多只能建立一个聚簇索引`
- 经常更新的列不宜建立聚簇索引

**[例14]为学生-课程数据库中的Student，Course，SC三个表建 立索引。**

- Student表按学号升序建唯一索引
- Course表按课程号升序建唯一索引
- SC表按学号升序和课程号降序建唯一索引

```sql
CREATE UNIQUE INDEX  Stusno ON Student(Sno);
CREATE UNIQUE INDEX  Coucno ON Course(Cno);
CREATE UNIQUE INDEX  SCno ON SC(Sno ASC,Cno DESC);
```

![](https://img.npfs06.top/20210329234946.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

### 删除索引

**`DROP INDEX <索引名> ON <表名>;`
`DROP INDEX <表名>.<索引名>;`**

- 删除索引时，系统会从数据字典中删去有关该索引的
  描述。

**[例15] 删除Student表的Stusname索引**

```sql
   DROP INDEX Stusno ON Student;
   //等价
   DROP INDEX Student.Stusno;
```

![](https://img.npfs06.top/20210329234958.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)





---



# 思维导图

![](https://img.npfs06.top/20210330000240.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

# Student/SC/Course表数据及结构

- 本篇文章都是围绕这三个表展开的。
  ![](https://img.npfs06.top/20210330000302.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

# SELECT语句的一般格式

先从整体上了解一下SELECT的格式，关键字的位置。

```sql
SELECT [ALL|DISTINCT]   
<目标列表达式> [别名] [ ，<目标列表达式> [别名]] … 
FROM   <表名或视图名> [别名]   [ ，<表名或视图名> [别名]] …
[WHERE <条件表达式>]
[GROUP  BY<列名1>
[HAVING  <条件表达式>]]
[ORDER BY <列名2> [ASC|DESC] 
```

# 单表查询

## 选择表中的若干列

### 查询指定列

- `查询指定列`

**[例1] 查询全体学生的学号与姓名。**

```sql
SELECT Sno,Sname 
FROM Student;
```

![](http://img.npfs06.top/20210330000313.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

**[例2] 查询全体学生的姓名、学号、所在系。**

```sql
SELECT Sname,Sno,Sdept 
FROM Student;
```

![](http://img.npfs06.top/20210330000324.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

### 查询全部列

- 选出所有属性列：在SELECT关键字后面列出所有列名 ，将<目标列表达式>指定为 *

**[例3] 查询全体学生的详细记录。**

```sql
SELECT  Sno,Sname,Ssex,Sage,Sdept 
FROM Student;
//两种方式
SELECT  *FROM Student;
```

![](http://img.npfs06.top/20210330000334.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

### 查询经过计算的值

- `SELECT子句的<目标列表达式>可以为：`
  算术表达式
  字符串常量
  函数
  列别名

#### 算术表达式

**[例4] 查全体学生的姓名及其出生年份。这里假定目前年份是2004年。**

```sql
SELECT Sname,2004-Sage 
FROM Student;
```

![](http://img.npfs06.top/20210330000344.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

#### 字符串常量及函数

**[例5] 查询全体学生的姓名、出生年份和所有系，要求用小写字母表示所有系名，这里假定目前年份是2004年。**

```sql
 SELECT Sname,'Year of Birth: ', 2004-Sage, LOWER(Sdept)
 FROM Student;
```

![](http://img.npfs06.top/20210330000356.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

#### 使用列别名改变查询结果的列标题

```sql
SELECT Sname NAME,'Year of Birth: '  BIRTH,
2000-Sage  BIRTHDAY,
LOWER(Sdept)  DEPARTMENT
FROM Student;
```

![](http://img.npfs06.top/20210330000406.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

## 选择表中的若干元组（行）

### 键词DISTINCT去掉表中重复的行

- 如果没有指定DISTINCT关键词，则缺省为ALL

```sql
SELECT Sno FROM SC;
/*等价于：*/
SELECT ALL  Sno  FROM SC;
```

![](http://img.npfs06.top/20210330000415.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)
**[例6] 查询选修了课程的学生学号。指定`DISTINCT`关键词，去掉表中重复的行**

```sql
SELECT DISTINCT Sno
FROM SC;
```

![](http://img.npfs06.top/20210330000426.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

### 查询满足条件的元组（行）

- 常用的查询条件

| 查询条件             | 谓词                                                |
| -------------------- | --------------------------------------------------- |
| 比较                 | =，>，<，>=，<=，!=，<>，!>，!<；NOT+上述比较运算符 |
| 确定范围             | BETWEEN AND，NOT BETWEEN AND                        |
| 确定集合             | IN，NOT IN                                          |
| 字符匹配             | LIKE，NOT LIKE                                      |
| 空值                 | IS NULL，IS NOT NULL                                |
| 多重条件（逻辑运算） | AND，OR，NOT                                        |

#### 比较大小

**[例7]查询计算机科学系全体学生的名单。**

```sql
SELECT Sname
FROM Student
WHERE Sdept='CS';
```

![](http://img.npfs06.top/20210330000437.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)
**[例8] 查询所有年龄在20岁以下的学生姓名及其年龄。**

```sql
SELECT Sname,Sage 
FROM Student 
WHERE Sage < 20;
```

![](http://img.npfs06.top/20210330000447.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)
**[例9]查询考试成绩有不及格的学生的学号。**

```sql
SELECT DISTINCT Sno
FROM  SC
WHERE Grade<60;
```

![](http://img.npfs06.top/20210330000457.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

#### 确定范围

**谓词:**

- `BETWEEN … AND …`
- `NOT BETWEEN … AND …`

**[例10] 查询年龄在20~23岁（包括20岁和23岁）之间的学生的**

```sql
SELECT Sname,Sdept,Sage
FROM  Student
WHERE Sage 
BETWEEN 20 AND 23; 
```

![](http://img.npfs06.top/20210330000515.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)
**[例11] 查询年龄不在20~23岁之间的学生姓名、系别和年龄**

```sql
SELECT Sname,Sdept,Sage
FROM  Student
WHERE Sage NOT BETWEEN 20 AND 23;
```

![](http://img.npfs06.top/20210330000525.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

#### 确定集合

**谓词：**

- `IN <值表>,`
- `NOT IN <值表>`

**[例12]查询信息系（IS）、数学系（MA）和计算机科学系（CS）学生的姓名和性别。**

```sql
SELECT Sname,Ssex
FROM  Student
WHERE Sdept IN ( 'IS','MA','CS' );
```

![](http://img.npfs06.top/20210330000534.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)
**[例13]查询既不是信息系、数学系，也不是计算机科学系的学生的姓名和性别。**

```sql
SELECT Sname,Ssex
FROM Student
WHERE Sdept NOT IN ( 'IS','MA','CS' );
```

![](http://img.npfs06.top/20210330000545.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

#### 字符匹配

**谓词：**

- `[NOT] LIKE ‘<匹配串>’ [ESCAPE ‘ <换码字符>’]`

**`匹配串为固定字符串`**
**[例14] 查询学号为201215121的学生的详细情况。**

```sql
SELECT *    
FROM  Student  
WHERE  Sno LIKE '201215121';
/*等价于：*/ 
SELECT  * 
FROM  Student 
WHERE Sno = '201215121';
```

![](http://img.npfs06.top/20210330000600.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

**`匹配串为含通配符的字符串`**
**[例15] 查询所有姓刘学生的姓名、学号和性别。**

```sql
SELECT Sname,Sno,Ssex
FROM Student
WHERE  Sname LIKE '刘%';
```

![](http://img.npfs06.top/20210330000609.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)
**[例16] 查询姓"欧阳"且全名为三个汉字的学生的姓名。**

```sql
SELECT Sname
FROM   Student
WHERE  Sname LIKE '欧阳_';
```

![](http://img.npfs06.top/20210330000621.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

**[例17] 查询名字中第2个字为"阳"字的学生的姓名和学号。**

```sql
SELECT Sname,Sno
FROM Student
WHERE Sname LIKE '_阳%';
```

![](http://img.npfs06.top/20210330000631.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

**[例18] 查询所有不姓刘的学生姓名。**

```sql
SELECT Sname,Sno,Ssex
FROM Student
WHERE Sname NOT LIKE '刘%';
```

![](http://img.npfs06.top/20210330000642.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

#### 使用换码字符’'将通配符转义为普通字符

- `ESCAPE '＼' 表示“ ＼” 为换码字符`

**[例19] 查询DB_Design课程的课程号和学分。**

```sql
SELECT Cno,Ccredit
FROM Course
WHERE Cname LIKE 'DB\_Design' ESCAPE '\';
```

![](http://img.npfs06.top/20210330000654.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

**[例20] 查询以"DB_"开头，且倒数第3个字符为 i的课程的详细情况。**

```sql
SELECT  *
FROM   Course
WHERE  Cname LIKE  'DB\_%i_ _' ESCAPE '\';
```

![](http://img.npfs06.top/20210330000704.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

#### 涉及空值的查询

**谓词：**

- `IS NULL`
- `IS NOT NULL`
- `“IS” 不能用 “=” 代替`

**[例21] 某些学生选修课程后没有参加考试，所以有选课记录，但没有考试成绩。查询缺少成绩的学生的学号和相应的课程号。**

```sql
SELECT Sno,Cno
FROM  SC
WHERE  Grade IS NULL;
```

![](http://img.npfs06.top/20210330000713.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

**[例22] 查所有有成绩的学生学号和课程号。**

```sql
SELECT Sno,Cno
FROM  SC
WHERE  Grade IS NOT NULL;
```

![](http://img.npfs06.top/20210330000724.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

#### 多重条件查询

**逻辑运算符：**

- AND和 OR来联结多个查询条件
- AND的优先级高于OR
- 可以用括号改变优先级

**可用来实现多种其他谓词**

- `[NOT] IN`
- `[NOT] BETWEEN … AND …`

**[例23] 查询计算机系年龄在20岁以下的学生姓名。**

```sql
SELECT Sname
FROM  Student
WHERE Sdept= 'CS' AND Sage<20;
```

![](http://img.npfs06.top/20210330000734.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

**改写[例12] 查询信息系（IS）、数学系（MA）和计算机科学系（CS）学生的姓名和性别。**

```sql
SELECT Sname,Ssex
FROM Student
WHERE Sdept IN ( 'IS','MA','CS' );
/*可改写为：*/
SELECT Sname,Ssex
FROM   Student
WHERE  Sdept= 'IS' OR Sdept= 'MA' OR Sdept= 'CS';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328170055535.png)

## ORDER BY子句

**ORDER BY子句**

- `可以按一个或多个属性列排序；`
- `升序：ASC；`
- `降序：DESC；`
- `缺省值为升序；`

**当排序列含空值时**

- `ASC`：排序列为空值的元组`最后显示`
- `DESC`：排序列为空值的元组`最先显示`

**[例24] 查询选修了3号课程的学生的学号及其成绩，查询结果按分数降序排列。**

```sql
SELECT Sno,Grade
FROM  SC
WHERE  Cno= '3'
ORDER BY Grade DESC;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328170501500.png)
**[例25] 查询全体学生情况，查询结果按所在系的系号升序排列，同一系中的学生按年龄降序排列。**

```sql
SELECT  *
FROM  Student
ORDER BY Sdept,Sage DESC;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032817053075.png)

## 聚集函数

**聚集函数：**

- 计数
  `COUNT（[DISTINCT|ALL] *）`
  `COUNT（[DISTINCT|ALL] <列名>）`
- 计算总和
  `SUM（[DISTINCT|ALL] <列名>）`
- 计算平均值
  `AVG（[DISTINCT|ALL] <列名>）`
- 最大最小值
  `MAX（[DISTINCT|ALL] <列名>）`
  `MIN（[DISTINCT|ALL] <列名>）`

**[例26] 查询学生总人数。**

```sql
SELECT COUNT(*)
FROM  Student;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328170802782.png)
**[例27] 查询选修了课程的学生人数。**

```sql
SELECT COUNT(DISTINCT Sno)
FROM SC;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328170847534.png)
**[例28] 计算2号课程的学生平均成绩。**

```sql
SELECT AVG(Grade)
FROM SC
WHERE Cno= '2';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328171143778.png)

**[例29] 查询选修2号课程的学生最高分数。**

```sql
SELECT MAX(Grade)
FROM SC
WHERE Cno= '2';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328171230259.png)

**[例30]查询学生201215121选修课程的总学分数。**

```sql
SELECT SUM(Ccredit)
FROM  SC, Course
WHERE Sno='201215121' AND SC.Cno=Course.Cno;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328171516930.png)

## GROUP BY子句

**`GROUP BY`子句分组：**
**细化聚集函数的作用对象**

- 未对查询结果分组，聚集函数将作用于整个查询结果
- 对查询结果分组后，聚集函数将分别作用于每个组
- 作用对象是查询的中间结果表
- 按指定的一列或多列值分组，值相等的为一组

**`HAVING`短语与`WHERE`子句的区别：**

- 作用对象不同
- WHERE子句作用于`基表或视图`，从中选择满足条件的`元组`
- HAVING短语作用于`组`，从中选择满足条件的`组`。

**[例31] 求各个课程号及相应的选课人数。**

```sql
SELECT Cno,COUNT(Sno)
FROM    SC
GROUP BY Cno;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032817221647.png)

**[例32] 查询选修了2门以上课程的学生学号。**

```sql
SELECT Sno
FROM  SC
GROUP BY Sno
HAVING  COUNT(*) >2;  
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328172509185.png)

# 连接查询

**连接查询：同时涉及多个表的查询**

**连接条件或连接谓词：用来连接两个表的条件**

**一般格式：**

- `[<表名1>.]<列名1> <比较运算符> [<表名2>.]<列名2>`
- `[<表名1>.]<列名1> BETWEEN [<表名2>.]<列名2> AND [<表名2>.]<列名3>`

**连接字段：连接谓词中的列名称**

- 连接条件中的各连接字段类型必须是可比的，但名字不必是相同的

## 连接操作的执行过程

### 嵌套循环法(NESTED-LOOP)

- 首先在表1中找到第一个元组，然后从头开始扫描表2，逐一查找满足连接件的元组，找到后就将表1中的第一个元组与该元组拼接起来，形成结果表中一个元组。
- 表2全部查找完后，再找表1中第二个元组，然后再从头开始扫描表2，逐一查找满足连接条件的元组，找到后就将表1中的第二个元组与该元组拼接起来，形成结果表中一个元组。
- 重复上述操作，直到表1中的全部元组都处理完毕

### 排序合并法(SORT-MERGE)

**常用于=连接**

- 首先按连接属性对表1和表2排序
- 对表1的第一个元组，从头开始扫描表2，顺序查找满足连接条件的元组，找到后就将表1中的第一个元组与该元组拼接起来，形成结果表中一个元组。当遇到表2中第一条大于表1连接字段值的元组时，对表2的查询不再继续
- 找到表1的第二条元组，然后从刚才的中断点处继续顺序扫描表2，查找满足连接条件的元组，找到后就将表1中的第一个元组与该元组拼接起来，形成结果表中一个元组。直接遇到表2中大于表1连接字段值的元组时，对表2的查询不再继续
- 重复上述操作，直到表1或表2中的全部元组都处理完毕为止

### 索引连接(INDEX-JOIN)

- 对表2按连接字段建立索引
- 对表1中的每个元组，依次根据其连接字段值查询表2的索引，从中找到满足条件的元组，找到后就将表1中的第一个元组与该元组拼接起来，形成结果表中一个元组

## 等值与非等值连接查询

**等值连接：连接运算符为=**

**[例33] 查询每个学生及其选修课程的情况**

```sql
SELECT  Student.*,SC.*
FROM     Student,SC
WHERE  Student.Sno = SC.Sno;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328174005236.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

**自然连接**

**[例34] 对[例33]用自然连接完成。**

```sql
SELECT  Student.Sno,Sname,Ssex,Sage,Sdept,Cno,Grade
FROM     Student,SC
WHERE  Student.Sno = SC.Sno;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328174148296.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

## 自身连接

- **自身连接：一个表与其自己进行连接**
- **需要给表起别名以示区别**
- **由于所有属性名都是同名属性，因此必须使用别名前缀**

**[例35]查询每一门课的间接先修课（即`先修课的先修课`）**

```sql
SELECT  FIRST.Cno,SECOND.Cpno
FROM  Course  FIRST,Course  SECOND
WHERE FIRST.Cpno = SECOND.Cno;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328174958693.png)

## 连接JOIN

**SQL join 用于把来自两个或多个表的行结合起来。**
**标准格式：**

```sql
 SELECT column_name(s)
FROM table1//左表
<xxx JOIN> table2//右表
ON table1.column_name=table2.column_name;
```

**分类：**

- `INNER JOIN (JOIN)`
- `LEFT JOIN (LEFT OUTER JOIN)`
- `RIGHT JOIN (RIGHT OUTER JOIN)`
- `FULL JOIN (FULL OUTER JOIN)`

这里就以SC和Course两个表来检验这四类连接

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328184438818.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

### INNER JOIN (JOIN)

- `INNER JOIN`：关键字在表中存在至少一个匹配时返回行。
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328181150122.png)

```sql
SELECT Sno,SC.Cno,Grade,Course.Cno,Cname,Cpno,Ccredit
FROM  SC INNER JOIN Course ON (SC.Cno=Course.Cno);
/*INNER JOIN 与 JOIN结果相同*/
SELECT Sno,SC.Cno,Grade,Course.Cno,Cname,Cpno,Ccredit
FROM  SC  JOIN Course ON (SC.Cno=Course.Cno);
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328184047680.png)

### LEFT JOIN (LEFT OUTER JOIN)

- `LEFT JOIN`：关键字从左表（table1）返回所有的行，即使右表（table2）中没有匹配。如果右表中没有匹配，则结果为 NULL。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328181212136.png)

```sql
SELECT Sno,SC.Cno,Grade,Course.Cno,Cname,Cpno,Ccredit
FROM  SC LEFT JOIN Course ON (SC.Cno=Course.Cno);
/*LEFT JOIN 与 LEFT OUTER JOIN结果相同*/
SELECT Sno,SC.Cno,Grade,Course.Cno,Cname,Cpno,Ccredit
FROM  SC LEFT OUTER JOIN Course ON (SC.Cno=Course.Cno);
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328185007546.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

### RIGHT JOIN (RIGHT OUTER JOIN)

- `RIGHT JOIN`：关键字从右表（table2）返回所有的行，即使左表（table1）中没有匹配。如果左表中没有匹配，则结果为 NULL。
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328181227458.png)

```sql
SELECT Sno,SC.Cno,Grade,Course.Cno,Cname,Cpno,Ccredit
FROM  SC RIGHT JOIN Course ON (SC.Cno=Course.Cno);
/*RIGHT JOIN 与 RIGHT OUTER JOIN结果相同*/
SELECT Sno,SC.Cno,Grade,Course.Cno,Cname,Cpno,Ccredit
FROM  SC RIGHT OUTER JOIN Course ON (SC.Cno=Course.Cno);
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328185227724.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

### FULL JOIN (FULL OUTER JOIN)

- `FULL JOIN`：关键字只要左表（table1）和右表（table2）其中一个表中存在匹配，则返回行。结合了 LEFT JOIN 和 RIGHT JOIN 的结果。
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328181237180.png)

```sql
SELECT Sno,SC.Cno,Grade,Course.Cno,Cname,Cpno,Ccredit
FROM  SC FULL JOIN Course ON (SC.Cno=Course.Cno);
/*FULL JOIN 与 FULL OUTER JOIN结果相同*/
SELECT Sno,SC.Cno,Grade,Course.Cno,Cname,Cpno,Ccredit
FROM  SC FULL OUTER JOIN Course ON (SC.Cno=Course.Cno);
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328185614975.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

## 复合条件连接

**复合条件连接：WHERE子句中含多个连接条件**

**[例37]查询选修2号课程且成绩在88分以上的所有学生**

```sql
SELECT Student.Sno, Sname
FROM    Student,SC
WHERE Student.Sno = SC.Sno AND   
/* 连接谓词*/
SC.Cno= '2' AND SC.Grade > 88;     
/* 其他限定条件 */
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328190502486.png)
**[例38]查询每个学生的学号、姓名、选修的课程名及成绩**

```sql
SELECT Student.Sno,Sname,Cname,Grade
FROM    Student,SC,Course  /*多表连接*/
WHERE Student.Sno = SC.Sno 
and SC.Cno = Course.Cno;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328190635619.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

# 嵌套查询

## 嵌套查询概述

- 一个SELECT-FROM-WHERE语句称为一个`查询块`
- 将一个`查询块嵌套`在另一个`查询块`的`WHERE`子句或`HAVING`短语的条件中的查询称为`嵌套查询`

**一个例子：**

```sql
 SELECT Sname/*外层查询/父查询*/
 FROM Student
 WHERE Sno IN
(SELECT Sno    /*内层查询/子查询*/
 FROM SC
 WHERE Cno= '2');
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328191426836.png)

- 子查询的`限制`： `·不能使用ORDER BY子句·`
- 层层嵌套方式反映了 SQL语言的结构化
- 有些嵌套查询可以用连接运算替代

## 不相关子查询

**`子查询的查询条件不依赖于父查询`**

- 由里向外 逐层处理。即每个子查询在上一级查询处理之前求解，子查询的结果用于建立其父查询的查找条件。

## 相关子查询

**`子查询的查询条件依赖于父查询`**

- 首先取外层查询中表的第一个元组，根据它与内层查询相关的属性值处理内层查询，若WHERE子句返回值为真，则取此元组放入结果表
- 然后再取外层表的下一个元组
- 重复这一过程，直至外层表全部检查完为止

## 带有IN谓词的子查询

**[例39] 查询与“刘晨”在同一个系学习的学生。此查询要求可以分步来完成**

- ① 确定“刘晨”所在系名

```sql
SELECT  Sdept  
FROM     Student                            
WHERE  Sname= '刘晨';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328192246580.png)

- ② 查找所有在CS系学习的学生。

```sql
SELECT  Sno,Sname,Sdept     
FROM    Student                 
WHERE  Sdept= 'CS';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328192207929.png)
**将第一步查询嵌入到第二步查询的条件中**

```sql
SELECT Sno,Sname,Sdept
FROM Student
WHERE Sdept  IN
(SELECT Sdept
FROM Student
WHERE Sname= '刘晨');
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328192431892.png)

- 此查询为不相关子查询。

**[例40]查询选修了课程名为“信息系统”的学生学号和姓名**

```sql
SELECT Sno,Sname /*③ 最后在Student关系中取出Sno和Sname*/
FROM    Student  
WHERE Sno  IN
(  SELECT Sno  	/*② 然后在SC关系中找出选修了3号课程的学生学号*/
	 FROM    SC      
	 WHERE  Cno IN
		( SELECT Cno   /*① 首先在Course关系中找出 “信息系统”的课程号,为3号*/
		  FROM Course    
		  WHERE Cname= '信息系统'
     )
);
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328194014135.png)

**用连接查询实现[例40]**

```sql
SELECT Student.Sno,Sname
FROM    Student,SC,Course
WHERE Student.Sno = SC.Sno 
AND SC.Cno = Course.Cno 
AND Course.Cname='信息系统';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328194217851.png)

## 带有比较运算符的子查询

- 带有比较运算符的子查询是指父查询与子查询之间用比较运算符进行连接。当用户能确切知道内层查询返回的是`单个值`时，可以用>、<、=、>=、<= 、!=或< >等`比较运算符`。
- 与ANY或ALL谓词配合使用

**例：假设一个学生只可能在一个系学习，并且必须属于一个系，则在[例39]可以用`= 代替IN`：**

```sql
SELECT Sno,Sname,Sdept
FROM Student
WHERE Sdept  =
(SELECT Sdept
FROM Student
WHERE Sname= '刘晨');
/*两种方式都可以*/
SELECT Sno,Sname,Sdept
FROM Student
WHERE
(SELECT Sdept
FROM Student
WHERE Sname= '刘晨')
= Sdept ;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328210722539.png)
**［例41］找出每个学生超过他选修课程平均成绩的课程号。**

```sql
SELECT Sno, Cno
FROM  SC  x
WHERE Grade >=(SELECT AVG(Grade)  /*相关子查询*/ 
   	           FROM  SC y
              WHERE y.Sno=x.Sno
   						 );
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032821122791.png)

**`［例41］可能的执行过程：`**
1.从外层查询中取出SC的一个元组x，将元组x的Sno值（201215121）传送给内层查询。

```sql
SELECT AVG(Grade)
FROM SC y
WHERE y.Sno='201215121';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328211628491.png)

2.执行内层查询，得到值88（近似值），用该值代替内层查询，得到外层查询：

```sql
SELECT Sno, Cno
FROM  SC x
WHERE Grade >=88; 
```

3.执行这个查询，得到
（200215121，1）
（200215121，3）

4.外层查询取出下一个元组`重复做上述1至3步骤`，直到外层的SC元组全部处理完毕。结果为:
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032821122791.png)

## 带有ANY（SOME）或ALL谓词的子查询

**谓词语义：**

- `ANY：任意一个值`
- `ALL：所有值`

**需要配合使用比较运算符:**

|               |                                              |
| ------------- | -------------------------------------------- |
| > ANY         | 大于子查询结果中的某个值                     |
| > ALL         | 大于子查询结果中的所有值                     |
| < ANY         | 小于子查询结果中的某个值                     |
| < ALL         | 小于子查询结果中的所有值                     |
| >= ANY        | 大于等于子查询结果中的某个值                 |
| >= ALL        | 大于等于子查询结果中的所有值                 |
| <= ANY        | 小于等于子查询结果中的某个值                 |
| <= ALL        | 小于等于子查询结果中的所有值                 |
| = ANY         | 等于子查询结果中的某个值                     |
| =ALL          | 等于子查询结果中的所有值（通常没有实际意义） |
| !=（或<>）ANY | 不等于子查询结果中的某个值                   |
| !=（或<>）ALL | 不等于子查询结果中的任何一个值               |

**[例42] 查询其他系中比计算机科学某一学生年龄小的学生姓名和年龄**

```sql
SELECT Sname,Sage
FROM    Student
WHERE Sage < ANY (SELECT  Sage
                 FROM    Student
                  WHERE Sdept= 'CS')
   			 AND Sdept <> 'CS' ; /*父查询块中的条件 */
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328213109641.png)
**执行过程：**

> 关系数据库管理系统（Relational Database Management System：RDBMS）

1.RDBMS执行此查询时，首先处理子查询，找出 CS系中所有学生的年龄，构成一个集合(20，19)
\2. 处理父查询，找所有不是CS系且年龄小于 20 或 19的学生

**用聚集函数实现[例42]**

```sql
SELECT Sname,Sage
FROM   Student
WHERE Sage < (SELECT MAX(Sage)
             FROM Student
             WHERE Sdept= 'CS')
          AND Sdept <> 'CS';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328213809434.png)

**[例43] 查询其他系中比计算机科学系所有学生年龄都小的学生姓名及年龄。**

- 方法一：用ALL谓词

```sql
SELECT Sname,Sage
FROM Student
WHERE Sage < ALL (SELECT Sage
                 FROM Student
                  WHERE Sdept= 'CS')
              AND Sdept <> 'CS';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328213933146.png)

- 方法二：用聚集函数

```sql
SELECT Sname,Sage
FROM Student
WHERE Sage < (SELECT MIN(Sage)
               FROM Student
               WHERE Sdept= 'CS')
            AND Sdept <> 'CS';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328214050416.png)

**ANY（或SOME），ALL谓词与聚集函数、IN谓词的等价转换关系**

|      | =    | <>或!= | <    | <=    | >    | >=    |
| ---- | ---- | ------ | ---- | ----- | ---- | ----- |
| ANY  | IN   | –      | <MAX | <=MAX | >MIN | >=MIN |
| ALL  | –    | NOT IN | <MIN | <=MIN | >MAX | >=MAX |

## 带有EXISTS谓词的子查询

**EXISTS谓词**

- 存在量词 ∃
- 带有EXISTS谓词的子查询不返回任何数据，只产生逻辑真值“true”或逻辑假值“false”。
  - 若内层查询结果非空，则外层的WHERE子句返回真值
  - 若内层查询结果为空，则外层的WHERE子句返回假值
- 由EXISTS引出的子查询，其目标列表达式通常都用* ，因为带EXISTS的子查询只返回真值或假值，给出列名无实际意义

**NOT EXISTS谓词**

- 若内层查询结果非空，则外层的WHERE子句返回假值
- 若内层查询结果为空，则外层的WHERE子句返回真值

**[例44]查询所有选修了1号课程的学生姓名。**

**思路分析：**

- 本查询涉及Student和SC关系
- 在Student中依次取每个元组的Sno值，用此值去检查SC关系
- 若SC中存在这样的元组，其Sno值等于此Student.Sno值，并且其Cno=‘1’，则取此Student.Sname送入结果关系

**1.用嵌套查询**

```sql
SELECT Sname
FROM Student
WHERE EXISTS(SELECT *
             FROM SC
              WHERE Sno=Student.Sno 
   			AND Cno= '1');
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328220025911.png)

**2.用连接运算**

```sql
SELECT Sname
FROM Student, SC
WHERE Student.Sno=SC.Sno 
AND SC.Cno= '1';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328220145896.png)

**[例45] 查询没有选修1号课程的学生姓名。**

```sql
SELECT Sname
FROM Student
WHERE NOT EXISTS(SELECT *
             FROM SC
              WHERE Sno=Student.Sno 
   			 AND Cno= '1');
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328220235205.png)

**不同形式的查询间的替换**

- 一些带EXISTS或NOT EXISTS谓词的子查询不能被其他形式的子查询等价替换
- 所有带IN谓词、比较运算符、ANY和ALL谓词的子查询都能用带EXISTS谓词的子查询等价替换
  用EXISTS/NOT EXISTS实现全称量词(难点)
  SQL语言中没有全称量词∀（For all）
  可以把带有全称量词的谓词转换为等价的带有存在量词的谓词：
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328220340747.png)
  **例：[例39]查询与“刘晨”在同一个系学习的学生。**
  **可以用带EXISTS谓词的子查询替换：**

```sql
SELECT Sno,Sname,Sdept
FROM Student S1
WHERE EXISTS(SELECT *
           FROM Student S2
           WHERE S2.Sdept = S1.Sdept
   		   AND S2.Sname = '刘晨');
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328220610972.png)

**[例46] 查询选修了全部课程的学生姓名。**

```sql
SELECT Sname
FROM Student
WHERE NOT EXISTS(SELECT *
                 FROM Course
                 WHERE NOT EXISTS(SELECT *
                                  FROM SC
                                  WHERE Sno= Student.Sno
                                   AND Cno= Course.Cno)
                 );
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328220839126.png)

**用EXISTS/NOT EXISTS实现逻辑蕴函(难点)**

- SQL语言中没有蕴函(Implication)逻辑运算
- 可以利用谓词演算将逻辑蕴函谓词等价转换为：
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328220918284.png)
  **[例47]查询至少选修了学生201215122选修的全部课程的学生号码。
  解题思路：**
- 用逻辑蕴函表达：查询学号为x的学生，对所有的课程y，只要201215122学生选修了课程y，则x也选修了y。
- 形式化表示：
  用P表示谓词 “学生201215122选修了课程y”
  用q表示谓词 “学生x选修了课程y”
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328221001631.png)
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328221513898.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)
  **用NOT EXISTS谓词表示：**

```sql
SELECT DISTINCT Sno
FROM SC SCX
WHERE NOT EXISTS(SELECT *
                FROM SC SCY
                WHERE SCY.Sno = '201215122' 
   			    AND NOT EXISTS(SELECT *
                                FROM SC SCZ
                                WHERE SCZ.Sno=SCX.Sno 
   					        	AND SCZ.Cno=SCY.Cno
   							)
   				);
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328221455379.png)

# 集合查询

## 集合操作的种类

- `并操作UNION`
- `交操作INTERSECT`
- `差操作EXCEPT`

**参加集合操作的各`查询结果`的`列数必须相同`；对应项的`数据类型也必须相同`**

## 集合操作举例

**[例48] 查询计算机科学系的学生及年龄不大于19岁的学生。**
**方法一：**

```sql
SELECT *
FROM Student
WHERE Sdept= 'CS'
UNION SELECT *
       FROM Student
       WHERE Sage<=19;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328231826579.png)

- `UNION`：将多个查询结果`合并`起来时，系统自动`去掉重复元组`。
- `UNION ALL`：将多个查询结果`合并`起来时，`保留重复元组`

**方法二：**

```sql
SELECT  DISTINCT  *
FROM Student
WHERE Sdept= 'CS'  
OR  Sage<=19;
```

**[例49] 查询选修了课程1`或者`选修了课程2的学生。**

```sql
SELECT Sno
FROM SC
WHERE Cno='1'
UNION
SELECT Sno
FROM SC
WHERE Cno= '2';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328231932550.png)

**[例50] 查询计算机科学系的学生与年龄不大于19岁的学生的`交集`**

```sql
SELECT *
FROM Student
WHERE Sdept='CS' 
INTERSECT
SELECT *
FROM Student
WHERE Sage<=19;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328232036527.png)

**[例50] 实际上就是查询计算机科学系中年龄不大于19岁的学生**

```sql
SELECT *
FROM Student
WHERE Sdept= 'CS' 
AND  Sage<=19;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328232149368.png)

**[例51] 查询选修课程1的学生集合与选修课程2的学生集合的`交集`**

```sql
SELECT Sno
FROM SC
WHERE Cno='1' 
INTERSECT
SELECT Sno
FROM SC
WHERE Cno='2';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328232244520.png)

**[例51]实际上是查询既选修了课程1`又`选修了课程2 的学生**

```sql
SELECT Sno
FROM SC
WHERE Cno='1' AND Sno IN
(SELECT Sno
FROM SC
WHERE Cno='2');
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328232458730.png)

**[例52] 查询计算机科学系的学生与年龄不大于19岁的学生的`差集`。**

```sql
SELECT *
FROM Student
WHERE Sdept='CS'
EXCEPT
SELECT  *
FROM Student
WHERE Sage <=19;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328232558341.png)

**[例52]实际上是查询计算机科学系中年龄大于19岁的学生**

```sql
SELECT *
FROM Student
WHERE Sdept= 'CS' 
AND  Sage>19;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328232657354.png)





---

# 思维导图

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401161830948.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

# 基本表更新—TABLE

## 插入数据—INSERT INTO

**两种插入数据方式**

1. 插入元组
2. 插入子查询结果

- 可以一次插入多个元组

### 插入元组

**语句格式**

```sql
	INSERT
	INTO <表名> [(<属性列1>[，<属性列2 >…)]
	VALUES (<常量1> [，<常量2>]    …           )
```

功能:将新元组插入指定表中

**INTO子句**

- 属性列的顺序可与表定义中的顺序不一致
- 没有指定属性列
- 指定部分属性列

**VALUES子句**

- 提供的值必须与INTO子句匹配
- 值的个数
- 值的类型

**［例1］ 将一个新学生元组（学号：200215128；姓名：陈冬；性别：男；所在系：IS；年龄：18岁）插入到Student表中。**

```sql
    INSERT
    INTO  Student (Sno，Sname，Ssex，Sdept，Sage)
    VALUES ('200215128'，'陈冬'，'男'，'IS'，18)；
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401132731647.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)
**［例2］ 将学生张成民的信息插入到Student表中。**

```sql
INSERT
INTO  Student
VALUES ('200215126', '张成民', '男',18,'CS'); 
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401132943495.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

**［例3］ 插入一条选课记录( ‘200215128’，'1 ')。**

```sql
INSERT
INTO SC(Sno,Cno)//RDBMS将在新插入记录的Grade列上自动地赋空值
VALUES ('200215128','1');
//等价
INSERT
INTO SC
VALUES ('200215128','1',NULL);
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020040113321448.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

### 插入子查询结果

**语句格式**

```sql
    INSERT 
    INTO <表名>  [(<属性列1> [，<属性列2>…  )]
    子查询；
```

**功能**
将子查询结果插入指定表中

**INTO子句(与插入元组类似)**

**子查询**

- SELECT子句目标列必须与INTO子句匹配
  值的个数
  值的类型

**[例4] 对每一个系，求学生的平均年龄，并把结果存入数据库。**

**第一步：建表**

```sql
CREATE  TABLE  Dept_age(
Sdept  CHAR(15),       /* 系名*/
Avg_age SMALLINT		  /*学生平均年龄*/
);   	 
```

**第二步：插入数据**

```sql
INSERT INTO  Dept_age(Sdept,Avg_age)
SELECT  Sdept,AVG(Sage)
FROM  Student
GROUP BY Sdept;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401134827273.png)

**RDBMS在执行插入语句时会检查所插元组是否破坏表上已定义的完整性规则**

- 实体完整性
- 参照完整性
- 用户定义的完整性
- NOT NULL约束
- UNIQUE约束
- 值域约束

## 修改数据—UPDATE SET

**语句格式**

```sql
UPDATE  <表名>
SET  <列名>=<表达式>[，<列名>=<表达式>]…
[WHERE <条件>];
```

**功能**
修改指定表中满足WHERE子句条件的元组

**SET子句**

- 指定修改方式
- 要修改的列
- 修改后取值

**WHERE子句**

- 指定要修改的元组
- `缺省表示要修改表中的所有元组`

**三种修改方式**

1. 修改某一个元组的值
2. 修改多个元组的值
3. 带子查询的修改语句

### 修改某一个元组的值

**[例5] 将学生201215121的年龄改为22岁**

```sql
UPDATE  Student
SET Sage=22
WHERE  Sno='201215121'; 
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401135354968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

**[例6] 将所有学生的年龄增加1岁**

```sql
UPDATE Student
SET Sage= Sage+1;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401135500751.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)
**[例7] 将计算机科学系全体学生的成绩置零。**

```sql
UPDATE SC
SET  Grade=0
WHERE  'CS'=( 
				SELECT  Sdept
				FROM  Student
				WHERE  Student.Sno = SC.Sno
			);
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020040113581718.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

**RDBMS在执行修改语句时会检查修改操作是否破坏表上已定义的完整性规则**

- 实体完整性
- 主码不允许修改
- 用户定义的完整性
- NOT NULL约束
- UNIQUE约束
- 值域约束

## 删除数据 —DELETE

**三种删除方式**

1. 删除某一个元组的值
2. 删除多个元组的值
3. 带子查询的删除语句

### 删除某一个元组的值

```sql
DELETE
FROM Student
WHERE Sno= '200215128';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401140233412.png)

### 删除多个元组的值

**[例9] 删除所有的学生选课记录。**

```sql
        DELETE
        FROM SC；//这个我就不截图了
```

### 带子查询的删除语句

[例10] 删除计算机科学系所有学生的选课记录。

```sql
DELETE
FROM SC
WHERE  'CS'=(
   				SELECT Sdept
   				FROM Student
   				WHERE Student.Sno=SC.Sno
   			);
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401140620653.png)

# 视图—VIEW

**视图的作用：**

1. 视图能够简化用户的操作
2. 视图使用户能以多种角度看待同一数据
3. 视图对重构数据库提供了一定程度的逻辑独立性
4. 视图能够对机密数据提供安全保护
5. 适当的利用视图可以更清晰的表达查询

**视图的特点**

- 虚表，是从一个或几个基本表（或视图）导出的表
- 只存放视图的定义，不存放视图对应的数据
- 基表中的数据发生变化，从视图中查询出的数据也随之改变

**基于视图的操作**

- 查询
- 删除
- 受限更新
- 定义基于该视图的新视图

> 视图的UPDATE、DELETE、INSERT INTO(有受限)，与基本表同步。

## 定义视图

### 建立视图

- **语句格式**

```sql
CREATE  VIEW <视图名>  [(<列名>  [，<列名>]…)]
AS  <子查询>
[WITH  CHECK  OPTION]；
```

- 组成视图的属性列名：全部省略或全部指定
- `子查询不允许`含有`ORDER BY`子句和`DISTINCT`短语
- RDBMS执行CREATE VIEW语句时只是把视图定义存入`数据字典`，并不执行其中的SELECT语句。
- 在对视图查询时，按视图的定义从基本表中将数据查出。

**[例1] 建立信息系IS学生的视图。**

```sql
CREATE VIEW IS_Student
AS 
SELECT Sno,Sname,Sage
FROM    Student
WHERE  Sdept= 'IS';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401141734215.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)
**[例2]建立信息系学生的视图，并要求进行修改和插入操作时仍需保证该视图只有信息系IS的学生 。**
**对IS_Student视图的更新操作：**

- 修改操作：自动加上Sdept= 'IS’的条件
- 删除操作：自动加上Sdept= 'IS’的条件
- 插入操作：自动检查Sdept属性值是否为’IS’
  - 如果不是，则拒绝该插入操作
  - 如果没有提供Sdept属性值，则自动定义Sdept为’IS’

```sql
CREATE VIEW IS_Student
AS 
SELECT Sno,Sname,Sage
FROM  Student
WHERE  Sdept= 'IS'
WITH CHECK OPTION;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401142141109.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)
**基于多个基表的视图**

**[例3] 建立信息系选修了1号课程的学生视图。**

```sql
CREATE VIEW IS_S1(Sno,Sname,Grade)
AS 
SELECT Student.Sno,Sname,Grade
FROM  Student,SC
WHERE  Sdept= 'IS' 
AND Student.Sno=SC.Sno 
AND SC.Cno= '1';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401142857172.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)
**基于视图的视图**

**[例4] 建立信息系选修了1号课程且成绩在90分以上的学生的视图。**

```sql
CREATE VIEW IS_S2
AS
SELECT Sno,Sname,Grade
FROM  IS_S1
WHERE  Grade>=90;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401143210685.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

**带表达式的视图**

**[例5] 定义一个反映学生出生年份的视图。**

```sql
CREATE  VIEW BT_S(Sno,Sname,Sbirth)
AS 
SELECT Sno,Sname,2000-Sage
FROM  Student;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401143446220.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

**分组视图**

**[例6] 将学生的学号及他的平均成绩定义为一个视图**

```sql
CREATE  VIEW S_G(Sno,Gavg)
AS  
SELECT Sno,AVG(Grade)
FROM  SC
GROUP BY Sno;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401143653506.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)
**不指定属性列**
**[例7]将Student表中所有女生记录定义为一个视图**

```sql
CREATE VIEW F_Student(F_Sno,name,sex,age,dept)
AS
SELECT *
FROM  Student
WHERE Ssex='女';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401143941159.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)
**缺点：**

- 修改基表Student的结构后，Student表与F_Student视图的映象关系被破坏，导致该视图不能正确工作。

### 删除视图

**语句的格式:**

```
DROP VIEW <视图名>;
```

- 该语句从`数据字典`中删除指定的视图定义
- 如果该视图上还导出了其他视图，使用`CASCADE级联`删除语句，把该视图和由它导出的所有视图一起删除
- 删除基表时，由该基表导出的所有视图定义都必须显式地使用DROP VIEW语句删除

**［例8］ 删除视图IS_S1**

```sql
DROP VIEW IS_S1;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401145332456.png)

- 关于级联删除CASCADE，不知道为什么，删除模式、删除表、删除视图，有约束却删除不了，有待解决，先放这里。

## 查询视图

**用户角度：`查询视图与查询基本表相同`**

**RDBMS实现视图查询的方法**

- 视图消解法（View Resolution）
  - 进行有效性检查
  - 转换成等价的对基本表的查询
  - 执行`修正`后的查询

关系数据库管理系统执行对视图的查询时，首先进行`有效性检查`，检查查询中涉及的表、视图等是否存在。如果存在，则从`数据字典`中取出视图的定义，把定义中的`子查询`和`用户的查询`结合起来，`转换成等价的对基本表的查询`，`然后再执行修正了的查询`。这一转换过程称为`视图消解`(view resolution)。

**[例9] 在信息系学生的视图中找出年龄小于等于20岁的学生。**

```sql
SELECT   Sno,Sage
FROM      IS_Student
WHERE   Sage<=20;
```

IS_Student视图的定义 (参见视图定义例1)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401151344373.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401150136382.png)
**视图消解转换后的查询语句为：**

```sql
 SELECT  Sno，Sage       
 FROM  Student
 WHERE  Sdept= 'IS'  AND  Sage<=20；
```

**[例10] 查询选修了1号课程的信息系学生**

```sql
SELECT  IS_Student.Sno,Sname
FROM     IS_Student,SC
WHERE  IS_Student.Sno =SC.Sno AND SC.Cno= '1';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401151417241.png)

**视图消解法的局限**

- 有些情况下，视图消解法不能生成正确查询。

**[例11]在S_G视图中查询平均成绩在90分以上的学生学号和平均成绩**

```sql
SELECT *
FROM   S_G
WHERE  Gavg>=90;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401151606151.png)

**S_G视图的子查询定义：**

```sql
CREATE VIEW S_G (Sno,Gavg)
AS 
SELECT  Sno,AVG(Grade)
FROM  SC
GROUP BY Sno;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401151548147.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

```sql
错误：
SELECT Sno,AVG(Grade)
FROM     SC
WHERE  AVG(Grade)>=90
GROUP BY Sno;

正确：
SELECT  Sno,AVG(Grade)
FROM  SC
GROUP BY Sno
HAVING AVG(Grade)>=90;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401151818567.png)

## 更新视图

### 更新数据—UPDATE SET

**[例12] 将信息系学生视图IS_Student中学号201215125的学生姓名改为“刘辰”。**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401152252972.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

```sql
UPDATE  IS_Student
SET  Sname= '刘辰'
WHERE  Sno= '201215125';
转换后的语句：		//基本表和视图表同步更新
UPDATE  Student
SET Sname= '刘辰'
WHERE Sno= '201215125' 
AND Sdept= 'IS'; 
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401152617938.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401152432280.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

### 插入数据—INSERT INTO

**[例13] 向信息系学生视图IS_Student中插入一个新的学生记录：200215111，赵新，20岁**

```sql
视图IS_Student的定义
CREATE VIEW IS_Student
AS 
SELECT Sno,Sname,Sage
FROM    Student
WHERE  Sdept= 'IS';
//如果加了WITH CHECK OPTION，则视图不能进行插入数据操作
CREATE VIEW IS_Student
AS 
SELECT Sno,Sname,Sage
FROM  Student
WHERE  Sdept= 'IS'
WITH CHECK OPTION;

//插入数据
INSERT
INTO IS_Student
VALUES('200215111','赵新',20);
//插入后基本表Student字段Sdept为空，视图表IS_Student无数据
转换为对基本表的更新：
INSERT
INTO   Student(Sno,Sname,Sage,Sdept)
VALUES('200215129','赵新2',20,'IS');
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401154939170.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401154949177.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

### 删除数据—DELETE

**[例14]删除信息系学生视图IS_Student中学号为200215129的记录**

```sql
DELETE
FROM IS_Student
WHERE Sno= '200215129';
转换为对基本表的更新：
DELETE
FROM Student
WHERE Sno= '200215129' AND Sdept= 'IS';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401155510155.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401155515911.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

### 更新视图的限制

- `更新视图的限制：`一些视图是不可更新的，因为对这些视图的更新不能唯一地有意义地转换成对相应基本表的更新

**例：视图S_G为不可更新视图。**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401160322767.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkxNDYwNA==,size_16,color_FFFFFF,t_70)

```sql
UPDATE  S_G
SET          Gavg=90
WHERE  Sno= '200215121';
```

这个对视图的更新无法转换成对基本表SC的更新

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401160413759.png)

- 允许对行列子集视图进行更新
- 对其他类型视图的更新不同系统有不同限制