---
layout:    post   				    # 使用的布局（不需要改）
title:    「Major Related」数据库系统原理错题集    # 标题 
subtitle:  来源于平时上课作业的错题好题收集，主要注重对基础知识的铺垫。 #副标题
date:      2020-03-09 				# 时间
author:    Culaccino					# 作者
header-img: img/upd_img10.png        #这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - 数据库系统
    - 专业课
---



# 数据库系统概论

**（1）从<u>用户</u>角度看数据库管理系统，其功能包括：（D）**

A.数据库操纵		B.数据库控制

C.数据库维护		D.全部正确

**补充**：从<u>系统</u>角度看DBMS的功能包括：数据路语言的编译、查询实现、查询优化、事务处理功能等

**同时，DBMS也是DBS的核心。**

————

**（2）DML、DDL、DCL（DBMS的三大部分）的区别：**

DML（Data Manipulation Language）数据操作语言，用于日常的操作，如select、update、insert、delete等；  DDL（Data Definition Language）数据定义语言，用于改变表的结构、数据类型、表与表之间的链接和约束等初始化工作；  DCL(Data Control Language)数据控制语言，用于设置改变权限等，用于保护数据库完整性、安全性。

————

**（3）下列说法正确的是（B）**

A.在三级模式间引入二级映像的主要作用是提高数据与程序的安全性。

B.对一个数据库系统来说，实际存在的只有物理级数据库。

C.数据的存储结构和逻辑结构之间的独立性称为逻辑独立性。

D.在数据库系统的三级模式中，内模式描述了数据库中全体数据的全局逻辑结构和特征。

A:~用于提高独立性     **B:正确，其他的包含物均是更高级的逻辑抽象概念。**

**C：内模式->物理独立性，模式->逻辑独立性。而内模式、模式与外模式间称为数据独立性（即物理独立性+逻辑独立性）**

————

**（4）下列说法正确的是（B）**

A.DBMS是处于计算机硬件和操作系统之间的可以直接对数据库进行操作的一个软件系统

B.DBMS能实现数据库的安全性控制、完整性控制、并发控制及数据库恢复等运行管理功能

C.DBMS包含DB和DBS

D.DBMS提供DCL，能实现对数据库中数据的查询、插入、修改和删除等操作。

**A:DBMS是处于用户和操作系统间的**   |  C:DBS包含了DBMS、DB及其他（系统分析员、用户、DBA、应用程序员都算DBS的人员）  |  D：增删查改是DML的功能

————

**（5）下列给出的内容中，不属于DBA的职责的是（C）**

A.定义数据库中的信息内容和结构			B.数据库的改进和重组、重构

C.设计编写应用程序							  	D.定义编写数据的安全性要求和完整性约束条件

————

**（6）文件系统阶段，用户虽有了一定的方便，但系统仍有很多缺点，主要由（数据冗余度大）和（数据和程序缺乏独立性）。**



# 建立数据模型

**（1）关系数据模型是目前最重要的一种数据模型，它的三个要素分别是（  ）。**

- [ ] 实体完整性、参照完整性、用户定义完整性
- [ ] 数据增加、数据修改、数据查询
- [x] 数据结构、关系操作、完整性约束
- [ ] 外模式、模式、内模式

**（2）在SQL中，关系模式称为（）子模式称为（）。**

- [x] 基本表，视图
- [ ] 视图，基本表
- [ ] 基本表，存储文件
- [ ] 存储文件，视图



# 数据库完整性

**（1）在数据库系统中，保证数据及语义正确和有效的功能是 （  ）。**

- [ ] 并发控制
- [ ] 存取控制
- [ ] 安全控制
- [x] 完整性约束控制

**（2）基于如下两个关系，其中雇员信息表关系EMP主键是雇员号，部门信息表关系DEPT的主键是部门号。**

| 雇员号 | 雇员名 | 部门号 | 工资  |
| ------ | ------ | ------ | ----- |
| 007    | 张无忌 | 02     | 8000  |
| 110    | 周芷若 | 04     | 6000  |
| 001    | 张三丰 | 02     | 9000  |
| 100    | 赵敏   | 03     | 10500 |

| 部门号 | 部门名 | 地址  |
| ------ | ------ | ----- |
| 01     | 销售部 | 1号楼 |
| 02     | 业务部 | 2号楼 |
| 03     | 人事部 | 3号楼 |
| 04     | 财务部 | 4号楼 |

**问1：若执行下面列出的操作，哪个操作不能成功执行？**

- [ ] 从EMP中删除（'010','周芷若',01,1200）
- [ ] 从EMP中插入行('102','赵敏',02,1200)
- [ ] 将EMP中雇员号为'056'的员工的工资改为1600元
- [x] 将EMP中雇员号为'101'的员工的部门号改为05

注：B的主码雇员号的取值未与已有记录重复，故可以加入；A、C中虽然定位到需要的记录，但并没有语法错误，可以执行成功（不会对表做任何操作）；D中雇员号为‘101’无法定位并不是错误点，错误在于没有05号部门，使得该表不满足参照完整性。

**问2：若执行下列操作，哪个操作不能成功执行？（）**

- [ ] 从DEPT中删除部门号=‘03’的行
- [x] 将DEPT中部门号为'02'的部门号改为'10'

注：删除部门是合法的操作，当然对于子表而言可以通过CASCADE进行级联删除或SET NULL置空；但将部门号修改后EMP表中部门号为02的员工就无法对应该部门，会违背参照性完整约束。



# 数据库安全性

**（1）视图机制提高了数据库系统的()。**

- [ ] 完整性
- [x] 安全性
- [ ] 一致性
- [ ] 并发控制

**(2)以下关于视图的描述不正确的是()。**

- [ ] 视图是外模式
- [x] 使用视图可以加快查询语句的执行速度
- [ ] 视图是虚表
- [ ] 使用视图可以加快查询语句的编写速度



# 数据库操作

**（1）SQL集数据查询、数据操纵、数据定义和数据控制功能于一体,语句ALTER TABLE实现下列哪类功能()。**

- [ ] 数据查询
- [ ] 数据操作
- [x] 数据定义
- [ ] 数据控制

**（2）在表或视图上执行除了（ 　）以外的语句都可以激活触发器。**

- [ ] Insert
- [ ] Update
- [ ] Delete
- [x] Create

**（3）下列不是数据定义语言DDL的是**

- [x] Insert（操纵）
- [x] Delete（操纵）
- [x] Update（操纵）
- [ ] Create
- [ ] Alter
- [ ] Drop
- [ ] 
- [ ] 

**（4）某公司管理有部门表（部门号，部门名，部门地址，经理）和员工表（员工号，员工姓名，员工性别，职称，出生日期，部门号），现将进行部门调整合并或删除某些部门设置，员工不辞退，则员工表的数据将采取哪种操作最为合理。**

- [ ] on update no action on delete no action
- [ ] on update no action on delete set null
- [ ] on update on delete set default
- [x] on update cascade on delete set null

**(5)根据SQL标准，针对用户Tom、Rose拥有对表student和course中的查询权限，并允许他将此权限授予他人，现要将收回Tom的权限，下面哪条语句适用？**

- [x] Remove Select on Student from Tom,Rose cascade
  Remove Select on Course from Tom,Rose cascade

**（6）已知Titles(Title_Id char(4), Title char(20), Pub_id char(4), price integer),其中Title_Id图书编号,Title图书名,Pub_id出版商编号,price价格。列出Titles表中其价格高于出版商ID为0736的出版商出版的书的最大价格的书的标题和ID,合适的查询语句是()。**

- [ ] Select Title_id,Title from Titles where price>ANY(select price from Titles where Pub_id='0736')
- [x] Select Title_id,Title from Titles where price>ALL(select price from Titles where Pub_id='0736')

注：ANY为存在，ALL为任意

**（7）假定学生关系是S(SNo, SName, Sex, Age),课程关系是C(CNo, CName, Teacher),学生选课关系是SC(SNo, CNo, Grade)。要查找选修4门以上(含4men)课程的学生总成绩（不统计不及格的课程），正确的SQL语句是( )。**

- [ ] Select Sno,SUM(Grade) from SC 
  Where Grade>=60
  Group by Sno
  Having Count(*)>=4
- [ ] Select Sno,Count(),SUM(Grade) from SC
  Group by Sno
  Having Grade>=60 and Count(*)>=4
- [x] Select Sno,Count(*),SUM(Grade) from SC
  Where Grade>=60
  Group by Sno
  Having Count()>=4
- [ ] Select Sno,SUM(Grade) from SC
  Where Grade>=60 and Count(*)>=4
  Group by Sno

**(8)有关系S(SNO, SNAME, SDEPT,SAGE)，C(CNO, CNAME)，SC(SNO, CNO, GRADE)。其中SNO是学生号，SNAME是学生姓名，SDEPT是系别,SAGE是学生年龄， CNO是课程号，CNAME是课程名称，GRADE是成绩。下列查询语句语法正确的是（ )。**

- [ ] Delete from S,SC
  Where S.Sno=SC.Sno AND Sname like '李%'
- [ ] Update from SC
  Set Grade=Grade+10
  Where Sno=(Select Sno from SC Where Grade<60)
- [ ] Select S1.Sno,Sname 
  from S1 Join SC on S1.Sno=SC.Sno
  Where Grade>=min(Grade)
- [x] Select S1.Sno,Sname from(
        Select Sno,Sname from S Where Sedpt='计算机') S1 Join SC on S1.Sno=SC.Sno
  Where Grade>60

注：A：Delete操作不能作用于多张表；B：不能保证成绩小于60的只有一个学生，故只能用in不能用=；C:Where语句中不能用聚合函数。

**(9)下列（   ）能查询出字段Col的第三个字母是R，但不以W结尾的字符串？**

- [x] Where Col Like '_ _ R%[(^)W]'   (无小括号，MD语法问题)

**（10）有关系S(SNO, SNAME, SDEPT,SAGE)，C(CNO, CNAME)，SC(SNO, CNO, GRADE)。其中SNO是学生号，SNAME是学生姓名，SDEPT是系别,SAGE是学生年龄， CNO是课程号，CNAME是课程名称，GRADE是成绩。查询每个系每门课程成绩前三的学生的姓名，系别，课程号，成绩。显示时按系别降序排列。（    ）**

- [x] Select Top 3 S.Sno,Sdept,Cno,Grade from S,SC
  Where S.Sno=SC.Sno
  Order by Sdept DESC



# 关系代数

**（1）在关系代数运算中，五种基本运算为（）。**

- [ ] 并、差、选择、投影、自然连接
- [ ] 并、差、交、选择、投影
- [ ] 并、差、交、选择、乘积
- [x] 并、差、选择、投影、乘积

**（2）下列关系运算中,( )运算不属于专门的关系运算。**

- [ ] 选择
- [x] 笛卡尔乘积
- [ ] 连接
- [ ] 投影

**（3）关系R与关系S只有一个公共属性，T1是R与S做θ连接的结果，T2是R与S自然连接的结果，则下列说法正确的是（）。**

- [ ] T1的属性个数大于或等于T2的属性个数
- [x] T1的属性个数大于T2的属性个数

注：θ连接不管起约束条件如何，结果属性个数均为n+m；而自然连接是特殊的等值连接，在连接完后重复的属性列要删除一列，故属性个数为n+m-1。

**（4）关系代数运算中，对某一关系做投影运算后将不会改变原有关系行数。**

- [ ] 对
- [x] 错

注：投影后会自动合并重复的行，故行数是有可能改变的



# 关系数据库规范化理论

**（1）在关系模式R(U，F)中，如果F是最小函数依赖集，则（）。**

- [x] R的规范化程度与F是否最小函数依赖集无关
- [ ] R∈2NF
- [ ] R∈3NF
- [ ] R∈BCNF

**(2)在关系模式R(U，F)中，R中任何非主属性对候选键完全函数依赖是R∈3NF的（）**

- [ ] 既不充分也不必要条件
- [ ] 充分条件
- [x] 必要条件
- [ ] 充分必要条件

**（3）规范化过程主要为克服数据库逻辑结构中的插入异常，删除异常，修改异常以及（   ） 的缺陷。**

- [ ] 数据的不一致性
- [ ] 结构不合理
- [ ] 数据丢失
- [x] 冗余度大

**（4）关系规范化中的删除异常是指（   ）。**

- [ ] 应该删除的数据未被删除
- [x] 不该删除的数据被删除

**（5）设有关系模式R（A，B，C，D），其数据依赖集：F＝{A→C，C→D}，则关系模式R的规范化程度最高达到（  ）。**

- [x] 1NF
- [ ] 2NF
- [ ] 3NF
- [ ] BCNF

