---
title: MySQL如何查询各科成绩前三
date: 2023-12-18
summary: MySQL如何查询各科成绩前三
series:
  - 面试拾遗
  - SQL
draft: false
tags:
  - 数据库
  - SQL
categories:
---
面试被问过两次  
假设有一个成绩表，其中有姓名、科目、成绩三个字段，如何查询出各科成绩的前三名
## 使用窗口函数的解决方案

```SQL
SELECT
  subject,
  name,
  score
FROM (
  SELECT
    subject,
    name,
    score,
    DENSE_RANK() OVER (PARTITION BY subject ORDER BY score DESC) rk
  FROM
    test.subject_scores
) RankedScores
WHERE
  rk <= 3
ORDER BY
  subject,
  score DESC;
```

### 实现思路

1. 使用 `DENSE_RANK()` 窗口函数对每个科目的学生按照成绩降序排名。
2. 在外部查询中选择排名在前三的学生。
3. 最终的结果按科目升序和成绩降序排序。
### 优势

- 简洁性：窗口函数提供了更简洁的方式获取排名信息。
- 可读性：易读且直观，提高了查询的可读性。
## 使用子查询的解决方案

使用子查询涉及到两个独立的子查询，其中一个处理普通排名，另一个处理并列排名。
```SQL
SELECT
  subject,
  name,
  score
FROM test.subject_scores s1
WHERE (
  SELECT COUNT(DISTINCT score)
  FROM test.subject_scores s2
  WHERE s2.subject = s1.subject AND s2.score > s1.score
) < 3 OR (
  SELECT COUNT(DISTINCT score)
  FROM test.subject_scores s3
  WHERE s3.subject = s1.subject AND s3.score = s1.score
) >= 3
ORDER BY
  subject,
  score DESC;
```
### 实现思路

1. 第一个子查询计算当前学生在当前科目的成绩高于的学生数量，用于普通排名。
2. 第二个子查询计算与当前学生成绩相同的学生数量，用于处理并列排名。
3. 使用 OR 连接两个条件，确保选择前三名学生或处理并列排名的情况。

### 缺点

- 逻辑复杂性：包含两个子查询，增加了查询的逻辑复杂性。
## 总结  
版本支持情况下( MySQL>=8.0 )，优先选择窗口函数方案