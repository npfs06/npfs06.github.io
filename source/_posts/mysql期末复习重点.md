---
title: General Introductory of Database System
date: 2021-06-26 21:06:32
updated: 2021-06-26 21:06:32
categories: 数据库
password: Npfs2333
---

《数据库系统概论》期末复习要点<!--more-->

# DBMS的特点

数据库管理系统是位于用户和操作系统之间的一层数据管理软件。是计算机的基础软件。它的主要功能包括：

1. 数据定义功能
2. 数据组织 、 存储和管理
3. 数据操纵功能
4. 数据库的事务管理和运行管理
5. 数据库的建立和维护功能
6. 其他功能



# 三级模式/两级映象与逻辑独立性、物理独立性的关系

![](http://img.npfs06.top/20210624155934.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

数据库系统的三级模式结构是指数据库系统是由外模式、模式、内模式三级构成。数据库系统的三级模式是数据的三个抽象级别，二级映象（外模式/模式映像、模式/内模式映像）在DBMS内部实现这三个抽象层次的联系和转换。

正是这两层映像保证了数据库系统中的数据能够具有较高的逻辑独立性和物理独立性

**当模式改变时，有数据库管理员对各个外模式/模式的映像作必要修改，可以使外模式保存不变。应用程序时依赖与外模式编写的，因此应用程序不必改变，保证了数据与程序的逻辑独立性**

**当数据库的存储结构改变时，由数据库管理员对模式/内模式的映像作相应改变，可以使模式保持不变，从而应用程序也不会发生改变。保证了数据与程序的物理独立性**





# 数据模型及其特点

数据模型是对现实世界数据特征的抽象，是用来描述数据、组织数据对数据进行操作的。**数据模型是数据库系统的核心和基础。数据模型通常由数据结构、数据操作和数据完整性约束三部分组成**

数据模型主要分为两类：1.概念模型 2. 逻辑模型和物理模型（基本数据模型）

概念模型是按用户的观点来对数据和信息建模，主要用于数据库设计（E-R图是概念模型的一种表试方法）

逻辑模型是按计算机的观点对数据建模，主要用于数据库管理系统的实现

物理模型是对数据最底层的抽象

逻辑模型分为关系模型和非关系模型，其中非关系模型有层次模型和网状模型

> 层次结构：以树结构为基础结构，将数据组织成一对多关系的结构。有且只有一个结点没有双亲结点，根以外的所有节点都有且仅有一个双亲结点。任何一个给定的记录值只能按其层次路径查看，没有一个子女记录值能脱离双亲记录值而存在

> 网状模型：允许一个以上的结点无双亲，一个结点可以有多于一个的双亲，两个结点之间可以有多种联系

> 关系模型：由一组关系组成，每个关系的数据结构是一张规范化的二维表，关系模型由关系数据结构、关系操作集合和关系完整性约束三部分组成

术语解释

![](http://img.npfs06.top/20210624165525.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](http://img.npfs06.top/20210626210525.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



# 层次及网状数据库系统的典型代表

**DBTG**系统（也称为CODASYL系统）是网状模型的典型代表

**IMS**（information management system）是层次模型的典型代表



# 关系的特点

关系：现实世界的实体以及实体之间的各种联系

关系有三种类型：基本类型、查询表、视图表

关系是元组的集合

关系是关系模型在某一时刻的状态或内容。关系模型是静态的、稳定的，而关系是动态的、随时间不断变化的

关系的两个不变性：参照完整性和实体完整性

# 笛卡尔积的元数与元组个数

元数：即关系模型中属性的个数，也可以说表中**列的个数**。
　　例如：在关系模型——学生(学号，姓名，年龄，性别，班级)中，元数为5。
元组（Tuple）：笛卡尔积中每一个元素（d1，d2，…，dn）叫作一个n元组（n-tuple）或简称元组。

元组个数是关系的基数,就是元组的组数,（通俗点讲，就是**有几行**的意思）

例子：就以这张图来举例![](http://img.npfs06.top/20210624184825.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



元数个数为3，元组个数为12



# 数据模型的分类

参照上面的`数据模型及其特点`

# 关系代数操作中的五种基本操作

五种基本操作为`选择、投影、并、差、笛卡儿积`

![](http://img.npfs06.top/20210624201545.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



# 关系代数操作结果中元组数目的最大值和最小值



# 基本表与视图的联系与区别

**区别**

视图又叫虚表，就是事实上并不存在的表，它是由多或一个基本表或视图导出的表。
基本表是实实在在存在的表，它被用来储存数据库的数据。
基本表存在于概念模型，而视图存在于外模型
视图是已经编译好的sql语句。而表不是
视图没有实际的物理记录。而表有
表是内容，视图是窗口
视图的建立和删除只影响视图本身，不影响对应的基本表。

**联系**

视图（view）是在基本表之上建立的表，它的结构（即所定义的列）和内容（即所有数据行）都来自基本表，它依据基本表存在而存在。一个视图可以对应一个基本表，也可以对应多个基本表。视图是基本表的抽象和在逻辑意义上建立的新关系。

![](http://img.npfs06.top/20210626210424.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](http://img.npfs06.top/20210626210440.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)





# SQL语句的书写及查询结果分析

基本格式：

```mysql
SELECT [ALL|DISTINCT]   
<目标列表达式> [别名] [ ，<目标列表达式> [别名]] … 
FROM   <表名或视图名> [别名]   [ ，<表名或视图名> [别名]] …
[WHERE <条件表达式>]
[GROUP  BY<列名1>
[HAVING  <条件表达式>]]
[ORDER BY <列名2> [ASC|DESC] 
```

order by 和group by 的区别

## order by（排序查询）

> order by（排序查询）,asc升序，desc降序（默认升序，可缺省）

- 例（默认方式）：

```
//查询所有信息按id升序排序
select * from 表名 order by id
```

- 例（多条件排序方式）：

```
//先按id升序排序，后按年龄降序排序
select * from 表名 order by id,age desc
```

## group by（分组查询）

> having只能用于group by子句，作用于组内，having条件子句可以直接跟函数表达式，使用group by子句的查询语句需要使用聚合函数（类似于sum(),mix(),count()）

- 例：

```
//按照学号分组，查询每个学号的总成绩
select 学号,SUM(成绩) from 选课表 group by 学号
```

# 参照完整性及违约处理

**定义：**

① 设F是基本关系R的一个或一组属性，但不是关系R的码。如果F与基本关系S的主码Ks相对应，则称F是基本关系R的外码,即该码是另一个表的主码。
基本关系R称为参照关系（Referencing Relation），即本表。
基本关系S称为被参照关系（Referenced Relation） 或目标关系（Target Relation），即外码对应的主码所在的表。

② 若属性（或属性组）F是基本关系R的外码它与基本关系S的主码Ks相对应（基本关系R和S不一定是不同的关系），则对于R中每个元组在F上的值必须为：

- 或者取空值（F的每个属性值均为空值）
- 或者等于S中某个元组的主码值
- 外码的值要么为空，要么为S中某个元组的主码值

**违约处理**

（1） 拒绝（NO ACTION）执行
不允许该操作执行。该策略一般设置为默认策略
（2） 级联（CASCADE）操作
当删除或修改被参照表（Student）的一个元组造成了与参照表（SC）的不一致，则删除或修改参照表中的所有造成不一致的元组
（3）设置为空值（SET-NULL）
当删除或修改被参照表的一个元组时造成了不一致，则将参照表中的所有造成不一致的元组的对应属性设置为空值。

# 关系数据模型的完整性约束分类

关系的完整性约束分为三类：

实体完整性：关系必须有主键，且不能为空；

参照完整性：维护实体之间的引用关系，外键可以为空，或者其值为参照关系对应的主键值；

用户定义的完整性：由应用环境决定，针对具体关系数据库的约束条件。



# 索引的特点及其用途

第一，通过创建唯一性索引，可以保证数据库表中每一行数据的**唯一性**。 
第二，可以大大加快 数据的**检索速度**，这也是创建索引的最主要的原因。 
第三，可以加速表和表之间的连接，特别是在实现数据的**参考完整性**方面特别有意义。 
第四，在使用分组和排序 子句进行数据检索时，同样可以显著减少查询中分组和排序的**时间**。 
第五，通过使用索引，可以在查询的过程中，使用优化隐藏器，提高系统的**性能**。 

建立索引是加快查询速度的有效手段。能快速定位到需要查找的内容。提供多种存储路径

# 实现数据安全性的方法

用户身份鉴别：静态口令验证、动态口令验证、生物特征鉴别、智能卡鉴别

多层存取控制：定义用户权限（用户权限由数据对象和操作类型组成）、合法权限检查



# 如何判断关系模式的分解具有函数依赖保持及无损分解特性

## 是否保持函数依赖

有属性集，ABCDEF，存在这样的函数依赖集{A->BC , CD->E , B->D , BE->F , EF->A},然后有这样的分解

{ABC , BD , BEF}。

设U1=ABC，A->BC，U2=BD，B->D ，U3=BEF，BE->F ，即我们不能推出 CD->E 

，EF->A，所以也不具有保持函数依赖的特性。

## 是否是无损分解

**方法一：无损连接定理**

关系模式R(U，F)的一个分解，ρ={R1<U1,F1>,R2<U2,F2>}具有无损连接的充分必要条件是：

U1∩U2→U1-U2 €（属于）F+ 或U1∩U2→U2 -U1€F+

**方法二：算法**

ρ={R1<U1,F1>,R2<U2,F2>,...,Rk<Uk,Fk>}是关系模式R<U,F>的一个分解，U={A1,A2,...,An}，F={FD1,FD2,...,FDp}，并设F是一个最小依赖集，记FDi为Xi→Alj，其步骤如下：

① 建立一张n列k行的表，每一列对应一个属性，每一行对应分解中的一个关系模式。若属性Aj Ui，则在j列i行上真上aj，否则填上bij；

② 对于每一个FDi做如下操作：找到Xi所对应的列中具有相同符号的那些行。考察这些行中li列的元素，若其中有aj，则全部改为aj，否则全部改为bmli，m是这些行的行号最小值。

如果在某次更改后，有一行成为：a1,a2,...,an，则算法终止。且分解ρ具有无损连接性，否则不具有无损连接性。

对F中p个FD逐一进行一次这样的处理，称为对F的一次扫描。

③ 比较扫描前后，表有无变化，如有变化，则返回第② 步，否则算法终止。如果发生循环，那么前次扫描至少应使该表减少一个符号，表中符号有限，因此，循环必然终止。

举例1：已知R<U,F>，U={A,B,C}，F={A→B}，如下的两个分解：

① ρ1={AB,BC}

② ρ2={AB,AC}

判断这两个分解是否具有无损连接性。

①因为AB∩BC=B，AB-BC=A，BC-AB=C

所以B→A ¢（不属于）F+，B→C ¢ （不属于）F+

故ρ1是有损连接。

② 因为AB∩AC=A，AB-AC=B，AC-AB=C

所以A→B €（属于）F+，A→C ¢（不属于）F+

故ρ2是无损连接。

举例2：已知R<U,F>，U={A,B,C,D,E}，F={A→C,B→C,C→D,DE→C,CE→A}，R的一个分解为R1(AD)，R2(AB)，R3(BE)，R4(CDE)，R5(AE)，判断这个分解是否具有无损连接性。

 ① 构造一个初始的二维表，若“属性”属于“模式”中的属性，则填aj，否则填bij

![](http://img.npfs06.top/20210626155530.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

② 根据A→C，对上表进行处理，由于属性列A上第1、2、5行相同均为a1，所以将属性列C上的b13、b23、b53改为同一个符号b13（取行号最小值）。

![](http://img.npfs06.top/20210626155547.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

③ 根据B→C，对上表进行处理，由于属性列B上第2、3行相同均为a2，所以将属性列C上的b13、b33改为同一个符号b13（取行号最小值）。

![](http://img.npfs06.top/20210626155556.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

④ 根据C→D，对上表进行处理，由于属性列C上第1、2、3、5行相同均为b13，所以将属性列D上的值均改为同一个符号a4。

![](http://img.npfs06.top/20210626155606.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

⑤ 根据DE→C，对上表进行处理，由于属性列DE上第3、4、5行相同均为a4a5，所以将属性列C上的值均改为同一个符号a3。

![](http://img.npfs06.top/20210626155615.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

⑥ 根据CE→A，对上表进行处理，由于属性列CE上第3、4、5行相同均为a3a5，所以将属性列A上的值均改为同一个符号a1。

![](http://img.npfs06.top/20210626155627.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

⑦ 通过上述的修改，使第三行成为a1a2a3a4a5，则算法终止。且分解具有无损连接性。


# Armstrong公理系统的三个基本规则

Armstrong公理系统设关系模式R<U,F>，其中U为属性集，F是U上的一组函数依赖，那么有如下推理规则：

A1自反律：若Y⊆X⊆U，则X→Y为F所蕴含；
A2增广律：若X→Y为F所蕴含，且Z⊆U，则XZ→YZ为F所蕴含；
A3传递律：若X→Y，Y→Z为F所蕴含，则X→Z为F所蕴含。

## 最小依赖集计算

（1）将F中的所有函数依赖的右边化为单一属性；

（2）去掉F中的所有函数依赖左边的冗余属性；

（3）去掉F中所有冗余的函数依赖。

F的函数最小依赖集F{min}并不是唯一的，它与对各函数依赖FD{i}及X→A中X个属性的处置的顺序有关。

![](http://img.npfs06.top/20210626143740.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](http://img.npfs06.top/20210626143754.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)


# 会判定关系R的候选码及其规范化程度

**判断候选码的方法**：
（1）如果有属性不在函数依赖集中出现，那么它必须**包含**（这里要注意一下，包含并不代表B是候选码）在候选码中，继续求它的闭包，如果他的闭包是属性全集，则为候选码，如果不是，则需要结合其他属性求闭包，继而判断是否是候选码；
（2）如果有属性只在函数依赖集右边出现，那么它必**不包含**在候选码中；
（3）如果有属性只在函数依赖集的左边出现，则该属性一定**包含**在候选码中。
（4）如果有属性或属性组能唯一标识元组，则它就是候选码，也就是说，通过函数依赖所求出的候选码的闭包中，能够包含所有的属性。
（5）对于左右出现过的属性：
		a. 先结合只在左边出现的属性求闭包，看看是否包括所有属性，如果他的闭包是属性全集，则为候选码；
		b. 如果没有包括所有属性，就结合其他左右都出现的求闭包，看看是否包括所有属性，如果他的闭包是属性全集，则为候选码



**例子：**

```
有关系模式R<U,F>，U={A,B,C,D}，
1)F={A→B,B→C,C→D,D→A}
2)F=Φ(空集)
3)F={A→B,B→A,A→C}
4)F={(A,B)→C,D→A}
5)F={(A,B)→C,C→A}
求它们的候选码

1）：所有属性ABCD在左右两侧均出现，因此前三条判断均不起作用。通过（4）的判断方法可以得知，A的闭包为{A，B，C，D}；B的闭包为{A，B，C，D}；C的闭包为{A，B，C，D}；D的闭包为{A，B，C，D}。四者的闭包均包含了所有的属性，因此此关系模式的候选码为A、B、C、D。
2）：函数依赖为空集，根据（1）可知，此关系模式的候选码为A、B、C、D。
3）：属性D在函数依赖集中从未出现，因此候选码中一定有它，而属性C只在右侧出现，因此候选码中一定没有它，结合上述方法（4）闭包的判断，候选码为(A,D),(B,D)。
4）：同上，C只在右侧出现，而B和D只在左侧出现，因此候选码中一定有D无C，最后判断A，可知候选码为(B,D)
5）：同理，候选码为(A,B,D)和(B,C,D)
```

![](http://img.npfs06.top/20210626133351.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](http://img.npfs06.top/20210626131243.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



# E－R图中各类元素的表示及联系的数的含义



# 关系模式设计不当所导致的问题



# 数据库设计常用方法和描述工具

常用的数据库设计方法如下：
（1）新奥尔良方法：将数据库设计分为若干阶段和步骤
（2）基于 E-R 模型的设计方法：概念设计阶段广泛采用
（3）基于 3NF 的设计方法：逻辑阶段可采用的有效方法
（4）ODL（Object Definition Language）方法：面向对象的数据库设计方法
（5）UML：统一建模语言

描述概念结构的有力工具是 E-R 模型。

```
 数据库设计的概念设计阶段，表示概念结构的常用方法和描述工具是（C ）。
　　A.层次分析法和层次结构图
　　B.数据流程分析法和数据流程图
　　C.实体联系法和实体联系图
　　D.结构分析法和模块结构图
```



# 根据需求分析进行概念结构及逻辑结构设计

# 建立 E-R 模型

（1）设计局部 E-R 模型
确定局部结构范围
定义实体
联系定义
属性分配

（2）设计全局 E-R 模型
确定公共实体类型
局部E-R模型的合并
消除冲突

（3）全局 E-R 模型的优化
实体类型的合并
冗余属性的消除
冗余联系的消除



（1）两实体间的 1:1 联系

一个 1:1 联系可以转换为一个独立的关系模式，也可以与任意一端对应的关系模式合并。如果转换为一个独立的关系模式，则与该联系相连的各实体的码以及联系本身的属性均转换为关系的属性，每个实体的码均是该关系的候选码。如果与某一端实体对应的关系模式合并，则需要在该关系模式的属性中加入另一个关系模式的码和联系本身的属性。可将任一方实体的主码纳入另一方实体对应的关系中，若有，联系的属性也一并纳入。

例如，如图 6.1 所示的 E-R 图可转换为如下关系模式：
工厂（厂号，厂名，地点，姓名，任期）
厂长（姓名，性别，年龄）
或者：
工厂（厂号，厂名，地点）
厂长（姓名，性别，年龄，厂号，任期）

![](http://img.npfs06.top/20210626172312.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

（2）两实体间的 1:m 联系

可将“1”方实体的主码纳入“m”方实体对应的关系中作为外码，同时把联系的属性也一并纳入“m”方对应的关系中。

例如，如图 6.2 所示的 E-R 图转换为如下关系模式：
仓库（仓库号，地点，面积）
商品（货号，品名，价格，仓库号，数量）

![](http://img.npfs06.top/20210626172246.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

（3）同一实体间的 1:m 联系

可在这个实体所对应的关系中多设一个属性，作为与该实体相联系的另一个实体的主码。

例如，如图 6.3 所示的 E-R 图可转换为如下关系模式：
职工（工号，姓名，年龄，性别，职称，工资，领导者工号，民意测验）

![](http://img.npfs06.top/20210626172332.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)


（4）两实体间的弱实体联系

可将被依赖实体的主码纳入弱实体中，作为弱实体的主码或主码中的一部分。

例如，如图 6.4 所示的 E-R 图可转换为如下关系模式：
职工（工号，姓名，年龄，性别，职称）
亲属（工号，亲属姓名，亲属关系）

![](http://img.npfs06.top/20210626172350.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)


（5）超类和子类的转换

超类、子类实体都可转换为一个关系，并将超类实体的主码加到子类实体中。

例如，如图 6.5 所示的 E-R 图中各个实体的属性为：
职员：职工号，姓名，性别，年龄，参加工作时间
飞行员：飞行小时，健康检查，飞机型号
机械师：学历，级别，专业职称
管理员：职务，职称

该 E-R 图转换为如下关系模式：

职员（职工号，姓名，性别，年龄，参加工作时间）
飞行员（职工号，飞行小时，健康检查，飞机型号）
机械师（职工号，学历，级别，专业职称）
管理员（职工号，职务，职称）

为了查询方便，可在超类实体中增加一个指示器属性，根据指示器的值直接查询子类实体表。所以，职员关系又可以为：
职员（职工号，姓名，性别，年龄，参加工作时间，职员类型）

![](http://img.npfs06.top/20210626172409.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)




（6）两实体间的 m:n 联系

必须对“联系”单独建立一个关系，该关系中至少包含被它所联系的双方实体的“主码”，如果联系有属性，也要纳入这个关系中。

例如，如图 6.6 所示的 E-R 图转换为如下关系模式：
学生（学号，姓名，性别，年龄）
课程（课程号，课程名，学时）
选修（学号，课程号，成绩）

![](http://img.npfs06.top/20210626172433.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)


（7）同一实体间的 m:n 联系

必须为这个“联系”单独建立一个关系，该关系中至少应包含被它所联系的双方实体的“主码”，如果联系有属性，也要纳入这个关系中。由于这个“联系”只涉及一个实体，所以加入的实体的主码不能同名。

例如，如图 6.7 所示的 E-R 图转换为如下关系模式：
零部件（代号，名称，价格）
组装（代号，组装件代号，数量）

![](http://img.npfs06.top/20210626172452.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)


（8）两个以上实体间的 m:n 联系

必须为这个“联系”单独建立一个关系，该关系中至少应包含被它所联系的各个实体的“主码”，若是联系有属性，也要纳入这个关系中。

例如，如图 6.8 所示的 E-R 图可转换为如下关系模式：
供应商（供应商号，供应商名，地址）
零件（零件号，零件名，重量）
项目（项目编号，项目名称，开工日期）
供应（供应商号，项目编号，零件号，零件数）


![](http://img.npfs06.top/20210626172513.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

# 找出关系模式的候选键及外键

**外键（Froeign Key）:如果模式R中的属性k是其他模式的主键，那么k在模式R中称为外键。**

候选码见《会判定关系R的候选码及其规范化程度》

# SQLServer 数据库系统安全性

数据库的安全性是指保护数据库以防止不合法的使用所造成的数据泄露、更改或破坏。

**数据库安全性控制的常用方法：**

- 用户标识和鉴定
- 存取控制
- 视图
- 审计
- 密码存储

# 数据库日志

日志文件是用来记录事务对数据库的更新操作的文件。日志文件主要有两种格式：以记录为单位的日志文件和以数据块为单位的日志文件

日志文件的作用：

1. 事务故障恢复和系统故障恢复必须用日志文件
2. 在动态转储方式中必须建立日志文件，后备副本和日志文件结合起来才能有效恢复数据库
3. 在静态转储方式中也可以建立日志，当数据库毁坏后可重新装入后援副本

# ACID

事务的4个特性：原子性、一致性、隔离性、持续性