---
title: Day 4
type: study-note
status: active
project: 软考中级数据库系统工程师备考
area: Career and Certification
exam: 数据库系统工程师
exam_level: 软考中级
stage: daily-study
category: daily-learning
day: 4
source_folder: 01_每日学习资料
created: 2026-06-03
updated: 2026-06-03
tags:
  - type/project-note
  - status/active
  - exam/db-engineer
  - project/soft-exam-db-engineer
  - type/study-note
  - stage/daily-study
---

# Day 4｜聚合函数、分组与分组筛选

> 状态说明：**这是 Day 4 的待学习任务**，用于继续推进数据库系统工程师备考内容，**不是已完成学习记录**。  
> 学习顺序：**先复盘 Day 3 的唯一正式错题，再进入 Day 4 新内容学习与练习。**

---

## 今日学习目标

1. 复盘 Day 3 唯一正式错题，纠正 `LIKE '张%'` 的理解偏差。
2. 理解 SQL 中常用聚合函数的含义与使用场景：
   - `COUNT()`
   - `SUM()`
   - `AVG()`
   - `MAX()`
   - `MIN()`
3. 掌握 `GROUP BY` 的基本语法与执行逻辑。
4. 区分 `WHERE` 与 `HAVING` 的作用对象和使用时机。
5. 能写出基础的分组统计 SQL，并理解其执行结果。
6. 为后续更复杂的统计分析、连接查询、子查询打好基础。

---

## 学习内容

### 一、进入 Day 4 前先复盘 Day 3 唯一正式错题

#### 1. 错题回顾
Day 3 唯一正式错题是**第23题**，核心知识点是：

```sql
WHERE 姓名 LIKE '张%'
```

#### 2. 错因核心
当时的错误在于把：

```sql
LIKE '张%'
```

误解成了“**包含张**”。

但实际上，它表示的是：

- **以“张”开头**
- 换成自然语言理解，就是“**姓张**”或“名字以张字起始”

#### 3. 正确认识通配符
- `%`：表示任意长度的任意字符
- `_`：表示单个任意字符

因此：

- `LIKE '张%'`：以“张”开头
- `LIKE '%张%'`：包含“张”
- `LIKE '_张%'`：第二个字是“张”

#### 4. 今日提醒
从 Day 4 开始，做 SQL 题时要特别注意：

- 条件是“以……开头”，还是“包含……”
- 通配符 `%` 在前、在后、还是两边都有
- 不要把字符串匹配条件想当然地按生活语言模糊理解，必须按 SQL 语法准确判断

---

### 二、什么是聚合函数

聚合函数（Aggregate Functions）用于**对多行数据进行统计计算，并返回一个结果**。

常见聚合函数如下：

#### 1. COUNT()
用于统计记录数量。

常见写法：

```sql
SELECT COUNT(*) FROM Student;
SELECT COUNT(学号) FROM Student;
```

说明：

- `COUNT(*)`：统计表中的总行数
- `COUNT(列名)`：统计该列**非空值**的个数

#### 2. SUM()
用于求和，通常用于数值型字段。

```sql
SELECT SUM(成绩) FROM SC;
```

#### 3. AVG()
用于求平均值。

```sql
SELECT AVG(成绩) FROM SC;
```

#### 4. MAX()
用于求最大值。

```sql
SELECT MAX(成绩) FROM SC;
```

#### 5. MIN()
用于求最小值。

```sql
SELECT MIN(成绩) FROM SC;
```

---

### 三、分组查询 GROUP BY

如果要“按某个字段分类统计”，就需要用 `GROUP BY`。

例如：统计每门课程的选课人数

```sql
SELECT 课程号, COUNT(*)
FROM SC
GROUP BY 课程号;
```

这表示：

- 先按“课程号”进行分组
- 每组再进行 `COUNT(*)` 统计

#### 1. 理解分组的本质
`GROUP BY` 的作用是把原本一整张表的数据，按指定字段拆成若干组，然后对每一组分别做统计。

#### 2. 使用规则
当 `SELECT` 子句里既出现普通列，又出现聚合函数时，普通列通常必须出现在 `GROUP BY` 中。

例如：

```sql
SELECT 课程号, AVG(成绩)
FROM SC
GROUP BY 课程号;
```

这是合法的，因为 `课程号` 出现在 `GROUP BY` 中。

而下面这种通常不合法：

```sql
SELECT 学号, 课程号, AVG(成绩)
FROM SC
GROUP BY 课程号;
```

因为 `学号` 没有分组，也没有聚合。

---

### 四、分组后的筛选 HAVING

#### 1. HAVING 的作用
`HAVING` 用于**对分组后的结果进行条件筛选**。

例如：查询平均成绩大于 80 分的课程

```sql
SELECT 课程号, AVG(成绩)
FROM SC
GROUP BY 课程号
HAVING AVG(成绩) > 80;
```

这表示：

1. 先按课程号分组
2. 计算每组平均分
3. 再筛选出平均分大于 80 的组

#### 2. WHERE 和 HAVING 的区别

- `WHERE`：对**分组前的原始记录**进行筛选
- `HAVING`：对**分组后的结果**进行筛选

例如：

```sql
SELECT 课程号, AVG(成绩)
FROM SC
WHERE 成绩 >= 60
GROUP BY 课程号
HAVING AVG(成绩) >= 80;
```

含义是：

- 先把不及格记录去掉（`WHERE`）
- 再按课程号分组
- 再筛选平均分至少 80 的课程（`HAVING`）

#### 3. 一个常见误区
不能把聚合条件随便写进 `WHERE`，例如：

```sql
WHERE AVG(成绩) > 80
```

这是错误写法。  
因为 `WHERE` 发生在分组之前，不能直接对聚合结果做判断。  
涉及聚合结果筛选时，应优先考虑 `HAVING`。

---

### 五、典型基础模板

#### 1. 统计总人数
```sql
SELECT COUNT(*) FROM Student;
```

#### 2. 统计某门课的平均成绩
```sql
SELECT AVG(成绩)
FROM SC
WHERE 课程号 = 'C1';
```

#### 3. 统计每门课的最高分
```sql
SELECT 课程号, MAX(成绩)
FROM SC
GROUP BY 课程号;
```

#### 4. 查询选课人数大于 30 的课程
```sql
SELECT 课程号, COUNT(*)
FROM SC
GROUP BY 课程号
HAVING COUNT(*) > 30;
```

#### 5. 查询每个系的学生人数
```sql
SELECT 所在系, COUNT(*)
FROM Student
GROUP BY 所在系;
```

---

### 六、今天要重点记住的结论

1. 聚合函数是对**多行数据做统计**。
2. `COUNT(*)` 统计总行数，`COUNT(列名)`统计该列非空值个数。
3. `SUM`、`AVG`、`MAX`、`MIN` 常用于数值统计。
4. `GROUP BY` 是“按某字段分组，再分别统计”。
5. `HAVING` 是“分组之后再筛选”。
6. `WHERE` 在前，`HAVING` 在后。
7. 涉及聚合结果条件判断时，优先考虑 `HAVING`。
8. 做题时先判断：题目是统计整体、统计每组，还是筛选某些组。

---

## 今日练习（30 道题）

> 说明：以下为 Day 4 待学习练习题。  
> 所有题目题干中的括号均按要求统一保留为：**（ ）**。  
> 请先独立作答，再查看后面的“答案解析（默认隐藏）”。

### 第1题
SQL 中用于统计记录数量的聚合函数是（C ）。

A. SUM  
B. AVG  
C. COUNT  
D. GROUP

### 第2题
在 SQL 中，用于求某列数值总和的函数是（B ）。

A. COUNT  
B. SUM  
C. MAX  
D. MIN

### 第3题
在 SQL 中，用于计算某列平均值的函数是（ A）。

A. AVG  
B. SUM  
C. COUNT  
D. DISTINCT

### 第4题
在 SQL 中，用于求某列最大值的函数是（B ）。

A. MIN  
B. MAX  
C. AVG  
D. COUNT

### 第5题
在 SQL 中，用于求某列最小值的函数是（C ）。

A. MAX  
B. SUM  
C. MIN  
D. GROUP

### 第6题
语句 `SELECT COUNT(*) FROM Student;` 的功能是（A ）。

A. 统计 Student 表的总行数  
B. 统计 Student 表中学号不重复的个数  
C. 统计 Student 表中姓名不为空的个数  
D. 统计 Student 表中的字段数

### 第7题
关于 `COUNT(*)` 与 `COUNT(列名)` 的说法，正确的是（D ）。

A. 两者都只统计非空值  
B. `COUNT(*)` 统计总行数，`COUNT(列名)`统计该列非空值个数  
C. `COUNT(*)` 不能用于整表统计  
D. `COUNT(列名)`会统计空值

### 第8题
若要按“所在系”统计学生人数，应使用的关键字是（ B）。

A. ORDER BY  
B. GROUP BY  
C. HAVING  
D. DISTINCT

### 第9题
语句 `SELECT 所在系, COUNT(*) FROM Student GROUP BY 所在系;` 的作用是（ C）。

A. 统计 Student 表总人数  
B. 查询每个学生所在系  
C. 统计每个系的学生人数  
D. 删除重复的所在系

### 第10题
`GROUP BY` 的作用是（C ）。

A. 对查询结果排序  
B. 对记录进行删除  
C. 按指定字段分组后再进行统计  
D. 修改表结构

### 第11题
在分组查询中，若 `SELECT` 中出现普通列和聚合函数，则普通列通常应（B ）。

A. 出现在 WHERE 中  
B. 出现在 GROUP BY 中  
C. 出现在 ORDER BY 中  
D. 出现在 DISTINCT 中

### 第12题
语句 `SELECT 课程号, AVG(成绩) FROM SC GROUP BY 课程号;` 的含义是（B ）。

A. 统计每个学生的平均成绩  
B. 统计每门课程的平均成绩  
C. 统计整张 SC 表的平均成绩  
D. 统计每门课程的总分

### 第13题
若要查询“平均成绩大于 80 分的课程”，应优先使用（C ）。

A. WHERE  
B. ORDER BY  
C. HAVING  
D. DISTINCT

### 第14题
在 SQL 中，`HAVING` 子句的作用是（B ）。

A. 对原始记录进行筛选  
B. 对分组后的结果进行筛选  
C. 对字段进行重命名  
D. 对结果进行排序

### 第15题
下列关于 `WHERE` 和 `HAVING` 的说法，正确的是（ C）。

A. `WHERE` 用于分组后筛选，`HAVING` 用于分组前筛选  
B. `WHERE` 和 `HAVING` 完全等价  
C. `WHERE` 用于分组前筛选，`HAVING` 用于分组后筛选  
D. `HAVING` 只能单独使用，不能和 `GROUP BY` 连用

### 第16题
下列语句中，能够正确表示“查询每门课程选课人数”的是（B ）。

A. `SELECT COUNT(*) FROM SC;`  
B. `SELECT 课程号, COUNT(*) FROM SC GROUP BY 课程号;`  
C. `SELECT 课程号 FROM SC HAVING COUNT(*) > 0;`  
D. `SELECT AVG(*) FROM SC GROUP BY 课程号;`

### 第17题
若要查询 SC 表中最高成绩，应使用（B ）。

A. `SELECT MIN(成绩) FROM SC;`  
B. `SELECT MAX(成绩) FROM SC;`  
C. `SELECT AVG(成绩) FROM SC;`  
D. `SELECT COUNT(成绩) FROM SC;`

### 第18题
若要查询 SC 表中最低成绩，应使用（ A）。

A. `SELECT MIN(成绩) FROM SC;`  
B. `SELECT MAX(成绩) FROM SC;`  
C. `SELECT SUM(成绩) FROM SC;`  
D. `SELECT COUNT(*) FROM SC;`

### 第19题
若要查询 SC 表中所有成绩的平均值，应使用（B ）。

A. `SELECT SUM(成绩) FROM SC;`  
B. `SELECT AVG(成绩) FROM SC;`  
C. `SELECT COUNT(成绩) FROM SC;`  
D. `SELECT MAX(成绩) FROM SC;`

### 第20题
若要查询“选课人数大于 30 的课程”，正确写法中必须出现（C ）。

A. `ORDER BY`  
B. `DISTINCT`  
C. `HAVING`  
D. `UPDATE`

### 第21题
下列语句中，正确的是（ B）。

A. `SELECT 课程号, COUNT(*) FROM SC HAVING COUNT(*) > 2;`  
B. `SELECT 课程号, COUNT(*) FROM SC GROUP BY 课程号 HAVING COUNT(*) > 2;`  
C. `SELECT 课程号, COUNT(*) FROM SC WHERE COUNT(*) > 2 GROUP BY 课程号;`  
D. `SELECT COUNT(*) FROM SC GROUP BY COUNT(*);`

### 第22题
若题目要求“先筛选出成绩大于等于 60 分的记录，再统计每门课平均成绩”，应优先在分组前使用（ C）。

A. HAVING  
B. ORDER BY  
C. WHERE  
D. DISTINCT

### 第23题
下列关于 SQL 执行逻辑的理解，正确的是（B ）。

A. 先 `HAVING`，后 `GROUP BY`  
B. `WHERE` 通常作用于分组前的原始记录  
C. `HAVING` 用于排序  
D. `GROUP BY` 用于删除重复行

### 第24题
语句  
`SELECT 课程号, COUNT(*) FROM SC GROUP BY 课程号 HAVING COUNT(*) >= 2;`  
的含义是（ C）。

A. 查询成绩大于等于 2 的课程  
B. 查询课程号大于等于 2 的课程  
C. 查询选课人数不少于 2 人的课程  
D. 查询每门课平均成绩不少于 2 分

### 第25题
若要按“所在系”分组，查询每个系的最高年龄，应使用（A ）。

A. `SELECT 所在系, MAX(年龄) FROM Student GROUP BY 所在系;`  
B. `SELECT MAX(年龄) FROM Student;`  
C. `SELECT 所在系, AVG(年龄) FROM Student;`  
D. `SELECT 所在系 FROM Student ORDER BY 年龄 DESC;`

### 第26题
若要按“所在系”分组，查询每个系的平均年龄，应使用（ B）。

A. `SELECT 所在系, SUM(年龄) FROM Student GROUP BY 所在系;`  
B. `SELECT 所在系, AVG(年龄) FROM Student GROUP BY 所在系;`  
C. `SELECT AVG(年龄) FROM Student;`  
D. `SELECT 所在系, COUNT(年龄) FROM Student;`

### 第27题
下列写法中，错误的是（C ）。

A. `SELECT 所在系, COUNT(*) FROM Student GROUP BY 所在系;`  
B. `SELECT 课程号, AVG(成绩) FROM SC GROUP BY 课程号;`  
C. `SELECT 学号, AVG(成绩) FROM SC GROUP BY 课程号;`  
D. `SELECT 课程号, MAX(成绩) FROM SC GROUP BY 课程号;`

### 第28题
若要统计 Student 表中“姓名”字段非空的记录个数，应使用（B ）。

A. `COUNT(*)`  
B. `COUNT(姓名)`  
C. `SUM(姓名)`  
D. `AVG(姓名)`

### 第29题
下列关于聚合函数的说法，正确的是（B ）。

A. 聚合函数只能用于整数类型字段  
B. 聚合函数是对多行数据进行统计计算  
C. 聚合函数只能和 `GROUP BY` 一起使用  
D. 每条 SQL 语句只能使用一个聚合函数

### 第30题
在 Day 4 的知识链路中，继 SQL 基础查询之后继续学习的重点是（ C）。

A. 事务并发控制  
B. 数据库恢复  
C. 聚合函数、分组与分组筛选  
D. 存储过程优化

---

## 答案解析（默认隐藏）

<details>
<summary>点击展开答案与解析</summary>

### 第1题答案：C
**解析：** `COUNT()` 用于统计记录数量，是最基础的计数聚合函数。

### 第2题答案：B
**解析：** `SUM()` 用于求和，常用于成绩、金额、数量等数值字段。

### 第3题答案：A
**解析：** `AVG()` 用于计算平均值。

### 第4题答案：B
**解析：** `MAX()` 表示最大值。

### 第5题答案：C
**解析：** `MIN()` 表示最小值。

### 第6题答案：A
**解析：** `COUNT(*)` 统计整张表的总行数，不关心某列是否为空。

### 第7题答案：B
**解析：**
- `COUNT(*)`：统计总行数
- `COUNT(列名)`：统计该列非空值个数

这是二者最常考的区别。

### 第8题答案：B
**解析：** “按所在系统计学生人数”本质上是分组统计，因此应使用 `GROUP BY`。

### 第9题答案：C
**解析：** 按“所在系”分组，再对每组做 `COUNT(*)`，得到每个系的人数。

### 第10题答案：C
**解析：** `GROUP BY` 的本质就是“按字段分组后，再对各组分别进行统计”。

### 第11题答案：B
**解析：** 在分组查询中，`SELECT` 里的普通列通常要出现在 `GROUP BY` 中，否则语义不完整或 SQL 非法。

### 第12题答案：B
**解析：** 按课程号分组，计算每组 `AVG(成绩)`，就是每门课程的平均成绩。

### 第13题答案：C
**解析：** “平均成绩大于 80 分”是对聚合结果的限制，因此使用 `HAVING`。

### 第14题答案：B
**解析：** `HAVING` 是对分组后的统计结果进行筛选。

### 第15题答案：C
**解析：**
- `WHERE`：分组前筛选原始记录
- `HAVING`：分组后筛选结果组

这是 SQL 统计题中的高频考点。

### 第16题答案：B
**解析：** 查询每门课程的选课人数，必须按课程号分组并统计每组数量。

### 第17题答案：B
**解析：** 最高成绩对应 `MAX(成绩)`。

### 第18题答案：A
**解析：** 最低成绩对应 `MIN(成绩)`。

### 第19题答案：B
**解析：** 平均值应使用 `AVG(成绩)`。

### 第20题答案：C
**解析：** “选课人数大于 30”是对分组统计结果进行筛选，因此必须用 `HAVING`。

### 第21题答案：B
**解析：**
- A 错在缺少 `GROUP BY`
- B 正确：先分组，再用 `HAVING COUNT(*) > 2` 筛组
- C 错在 `WHERE` 里不能直接写聚合条件
- D 写法明显不合逻辑

### 第22题答案：C
**解析：** “先筛选出成绩大于等于 60 分的记录”是对原始记录筛选，所以要用 `WHERE`。

### 第23题答案：B
**解析：** SQL 的常见理解方式是：
先对原始记录进行 `WHERE` 筛选，再 `GROUP BY`，最后用 `HAVING` 筛选分组结果。

### 第24题答案：C
**解析：** `HAVING COUNT(*) >= 2` 表示每组记录数不少于 2，因此是“选课人数不少于 2 人的课程”。

### 第25题答案：A
**解析：** 按所在系分组后，求每组最大年龄，应使用：
`SELECT 所在系, MAX(年龄) FROM Student GROUP BY 所在系;`

### 第26题答案：B
**解析：** 按所在系分组，计算每组平均年龄，标准写法就是：
`SELECT 所在系, AVG(年龄) FROM Student GROUP BY 所在系;`

### 第27题答案：C
**解析：** `SELECT 学号, AVG(成绩) FROM SC GROUP BY 课程号;`
错误在于 `学号` 既没有聚合，也没有出现在 `GROUP BY` 中。

### 第28题答案：B
**解析：** `COUNT(姓名)` 统计的是“姓名非空”的记录个数，而不是总行数。

### 第29题答案：B
**解析：** 聚合函数的本质是对多行数据做统计计算。  
A、C、D 都过于绝对，属于错误说法。

### 第30题答案：C
**解析：** Day 4 在 Day 3 SQL 基础查询之后，继续推进的核心内容就是：
**聚合函数、分组、分组筛选（COUNT / SUM / AVG / MAX / MIN / GROUP BY / HAVING）**。

</details>

---

## 今日复盘

> 注意：本部分是 Day 4 学习任务中的“学习后自查区”，用于学习完成后自行填写或对照，**不表示今天已经完成学习**。

### 1. 今天开始前必须记住的旧错
Day 3 唯一正式错题是第23题，错误核心不是不会写 SQL，而是把：

```sql
LIKE '张%'
```

理解错了。

必须牢牢记住：

- `LIKE '张%'` = **以张开头**
- `LIKE '%张%'` = **包含张**

以后遇到字符串匹配题，先看 `%` 在哪里，再下结论。

### 2. 今天新内容的核心总结
如果今天要给自己只留 5 句话，那就是：

1. `COUNT / SUM / AVG / MAX / MIN` 都是聚合函数。
2. 聚合函数是对**多行**做统计，不是对单行做修改。
3. `GROUP BY` 表示先分组，再分别统计。
4. `WHERE` 管原始记录，`HAVING` 管分组结果。
5. 看到“每个系”“每门课”“每类数据”这类字眼时，优先想到 `GROUP BY`。

### 3. 今天最容易混淆的点
- `COUNT(*)` 和 `COUNT(列名)` 的区别
- `WHERE` 和 `HAVING` 的适用阶段不同
- `SELECT` 中普通列是否已经放进 `GROUP BY`
- “整体统计”与“分组统计”不是一回事

### 4. 学习后自检清单
完成 Day 4 学习后，检查自己是否能独立回答：

- 我能否说清楚 `COUNT(*)` 和 `COUNT(列名)` 的区别？
- 我能否写出“每门课程平均成绩”的 SQL？
- 我能否写出“选课人数大于 30 的课程”的 SQL？
- 我能否区分什么时候用 `WHERE`，什么时候用 `HAVING`？
- 我是否已经彻底纠正 `LIKE '张%'` 的理解错误？

如果以上还有卡顿，说明 Day 4 还需要继续回看，不要急着进入下一天。

### 5. 给明天的衔接提醒
Day 4 的目标不是死记语法，而是建立“统计型 SQL”的基本思路。  
只有把聚合、分组、分组筛选打稳，后面遇到多表查询、子查询、综合题时才不容易乱。

