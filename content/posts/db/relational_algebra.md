---
title: 关系代数
date: 2023-12-26
categories:
  - 数据库
tags:
  - 数据库
summary: 关系代数小结
series:
  - 数据库系统原理
draft: false
---
## 1. 关系代数的基本表达式

### 1.1 选择操作（σ）

选择操作用符号 σ（Sigma）表示，它用于从关系中选择满足指定条件的行。例如，对关系 R 进行选择，其中属性 A 的值等于 1：σ A=1 ​(R)

### 1.2 投影操作（π）

投影操作用符号 π （pi）表示，它用于选择关系中的特定属性，去掉其它属性。例如，从关系 R 中选择属性 A 和 B：  π A,B ​(R)

### 1.3 并集操作（∪）

并集操作用符号∪表示，它用于合并两个关系的元组。例如，关系 R 和 S 的并集：R∪S

### 1.4 差集操作（-）

差集操作用符号-表示，它用于从一个关系中减去另一个关系。例如，关系 R 减去关系 S：R−S

### 1.5 笛卡尔积操作（×）

笛卡尔积操作用符号×表示，它用于生成两个关系的所有可能组合。例如，关系 R 和 S 的笛卡尔积：R×S

### 1.6 连接操作（⨝）

连接操作用符号⨝表示，它用于合并两个关系的元组，可以是等值连接或基于其他条件的连接。例如，关系 R 和 S 根据条件 a=b 进行连接：R ⋈ a=b ​S

## 2. 实际数据的应用举例

假设有一个学生数据库，包含学生的信息和选课情况。以下是一些实际数据和关系代数表达式的应用：

### 2.1 数据示例

- 学生表（Student）：学号（StudentID）、姓名（Name）、年龄（Age）
- 选课表（Enrollment）：学号（StudentID）、课程编号（CourseID）

### 2.2 表达式用法

- 选择操作：σ Age>20 ​(Student) 选择年龄大于 20 的学生。等价于：
``` SQL 
  select * from studen where age > 20
````
- 投影操作：π Name,Age ​(Student) 选择学生表中的姓名和年龄列。等价于：
``` SQL
select name, age from student
```
- 差集操作：Student − σ Age>20 ​(Student) 选择年龄不大于 20 的学生。等价于：
```SQL
select * from student where age <= 20
```
- 笛卡尔积操作：Student × Enrollment 生成学生表和选课表的所有可能组合。等价于：
``` SQL
select * from A,B 或 select * from A cross join B
```
- 连接操作：Student ⋈ Student.StudentID = Enrollment.StudentID ​Enrollment 根据学号连接学生表和选课表。等价于：
``` SQL
select * from student inner join enrollment ON student.studentID = enrollment.studentID
```
