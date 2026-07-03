---
title: Day 25 错题复盘
type: mistake-review
status: active
project: 软考中级数据库系统工程师备考
area: Career and Certification
exam: 数据库系统工程师
exam_level: 软考中级
stage: mistake-review
category: wrong-answers
day: 25
source_folder: 02_错题复盘
created: 2026-06-03
updated: 2026-06-03
tags:
  - type/project-note
  - status/active
  - exam/db-engineer
  - project/soft-exam-db-engineer
  - type/mistake-review
  - stage/mistake-review
---

# Day 25 错题复盘

> 本次错题复盘的核查依据：严格以 `Day 25.md` 中每道题的**实际作答内容**为准，并与“答案解析”区标准答案 / 参考答案 / 采分点逐题对比后整理。  
> 说明：  
> 1. 客观题以题目 `（ ）` 内填写的答案为准；  
> 2. 下午题 / 主观题以作答区中的实际文字答案为准；  
> 3. 只有真实写下的作答痕迹，才纳入正式核查；  
> 4. 本次 Day 25 共核查 **30 题**，其中客观题 **18 题**、下午题小问 **12 题**。

## 一、本次核查结果

- 总题数：30
- 已作答题数：30
- 正确题数：22（按正式判错口径）
- 错误题数：8
- 未作答题数：0
- 正确率：73.33%（按正式判错口径）

- 上午客观题总数：18
- 上午客观题正确数：18
- 上午客观题错误数：0
- 上午客观题正确率：100.00%

- 下午题作答组数：3（共 12 小问）
- 下午题采分点总数：38
- 下午题已命中采分点数：23
- 下午题采分点命中率：60.53%

> 结论：Day 25 的**上午 SQL 基础概念题全部答对**，说明你对 `PRIMARY KEY` / `FOREIGN KEY` / `LEFT JOIN` / `HAVING` / `NOT EXISTS` / 视图等高频概念的识别已经建立起来了。  
> 但下午题仍然暴露出比较明显的“**思路大体知道，但 SQL 壳子、连接粒度、分组字段与语法位置没完全锁死**”的问题：尤其集中在 **DDL 关键约束补全、三表连接、`NOT EXISTS` 正确落位、`LEFT JOIN ... IS NULL`、`INSERT ... SELECT` 外壳、按系分组统计** 这几条线上。当前更像是“概念懂了七成，落笔严谨度只到六成”，所以 Day 26 若要继续推进 SQL 专项 2，前提是先把 Day 25 这些核心书写链补稳。

---

## 二、错题明细

### A. 客观题正式错题

本次上午客观题 **无正式错题**。18 题全部答对。

### B. 下午题 / 主观题正式错题

#### 第19题（下午题）

**题型归属：** SQL DDL / 关键约束补全  
**你的实际作答：**
```sql
CREATE TABLE SYSDBA.SC
(
    SNO bigint NOT NULL,
    CNO bigint NOT NULL,
    GRADE numeric(100,0) , 
    CONSTRAINT SC_PK PRIMARY KEY (SNO, CNO), 
    CONSTRAINT SC_CHECK CHECK(((GRADE > 0) AND (GRADE < 100)))
)
```

**参考答案：**
```sql
CREATE TABLE SC (
    Sno CHAR(10) NOT NULL,
    Cno CHAR(10) NOT NULL,
    Grade INT CHECK (Grade BETWEEN 0 AND 100),
    PRIMARY KEY (Sno, Cno),
    FOREIGN KEY (Sno) REFERENCES Student(Sno),
    FOREIGN KEY (Cno) REFERENCES Course(Cno)
);
```

**本题采分点：**
1. 写出复合主键 `(Sno, Cno)`；
2. 写出两个外键引用关系；
3. 写出 `Grade` 的取值约束或非空意识。

**本次已命中的采分点：**
- 写出了复合主键；
- 意识到了 `Grade` 需要单独加约束。

**本次漏掉的采分点：**
- 漏写 `Sno` 和 `Cno` 的两个外键；
- `Grade > 0 AND Grade < 100` 把 0 和 100 排除掉了，不等于题目要求的 `0～100`；
- DDL 题最关键的“主键 → 外键 → 范围约束”顺序还没有完全写稳。

**失分原因类型：**
- [ ] 不会知识点
- [x] 会概念但不会下手
- [x] 会分析但写不规范
- [ ] 只写结论没写依据
- [x] 漏步骤 / 漏条件 / 漏关键词
- [ ] 时间不足 / 表达仓促

**正确理解应为：**
- 这类题先不要急着写数据类型，先把**谁是主键、谁引用谁、哪些字段要受范围约束**写稳；
- 题干已经明确给了 `SC.Sno → Student.Sno`、`SC.Cno → Course.Cno`，所以两个外键不能省；
- 范围题写 `CHECK` 时要特别注意是**闭区间**还是**开区间**。

**建议复习动作：**
1. 重写 1 遍第19题，强制按“主键 → 外键 → `CHECK`”顺序落笔；
2. 口头复述一句：**唯一标识靠主键，引用关系靠外键，取值边界靠 `CHECK`**；
3. 再补写 2 个类似骨架：一个带复合主键，一个带单主键 + 双外键。

#### 第20题（下午题）

**题型归属：** 基础连接查询  
**你的实际作答：**
```sql
select S.Sname,C.Cname,SC.Grade 
from Student S,Course C join SC on S.Sno=SC.Sno, C.Con=SC.Cno 
where SC.Grade > 80;
```

**参考答案：**
```sql
SELECT S.Sname, C.Cname, SC.Grade
FROM Student S
JOIN SC ON S.Sno = SC.Sno
JOIN Course C ON SC.Cno = C.Cno
WHERE SC.Grade >= 80;
```

**本题采分点：**
1. 输出字段来自三张表：学生名、课程名、成绩；
2. 写出两条连接条件；
3. 写出 `Grade >= 80` 的筛选条件。

**本次已命中的采分点：**
- 知道结果字段要从 `Student`、`Course`、`SC` 三张表取；
- 知道这题需要通过 `Sno`、`Cno` 两条线把三表串起来。

**本次漏掉的采分点：**
- 连接写法混用了旧式逗号连接和 `JOIN` 语法，结构不稳；
- `C.Con = SC.Cno` 字段名写错，应该是 `C.Cno = SC.Cno`；
- 条件应为 `>= 80`，你写成了 `> 80`。

**失分原因类型：**
- [ ] 不会知识点
- [x] 会概念但不会下手
- [x] 会分析但写不规范
- [ ] 只写结论没写依据
- [x] 漏步骤 / 漏条件 / 漏关键词
- [ ] 时间不足 / 表达仓促

**正确理解应为：**
- SQL 连接题要先定字段，再定连接，再定筛选；
- 同一题里不要把“逗号连接”和显式 `JOIN` 混着写；
- 比较符号是得分点，`>= 80` 与 `> 80` 不能混。

**建议复习动作：**
1. 把第20题用纯 `JOIN` 语法重写 2 遍；
2. 强化固定句序：**字段 → 主表 / 连接 → `WHERE` 条件**；
3. 再自己口头解释一遍：这题每个字段分别来自哪张表。

#### 第21题（下午题）

**题型归属：** `LEFT JOIN` + `GROUP BY` 统计查询  
**你的实际作答：**
```sql
select S.Sno,S.name,count(SC.Cno) 
from Student S left join SC on S.Sno = SC.Sno 
group by S.Sno ;
```

**参考答案：**
```sql
SELECT S.Sno, S.Sname, COUNT(SC.Cno) AS CourseCount
FROM Student S
LEFT JOIN SC ON S.Sno = SC.Sno
GROUP BY S.Sno, S.Sname;
```

**本题采分点：**
1. 从 `Student` 出发做 `LEFT JOIN`；
2. 结果粒度是“每个学生 1 行”；
3. 按学生分组并统计选课门数。

**本次已命中的采分点：**
- 选对了 `Student LEFT JOIN SC` 这条主线；
- 知道要用 `COUNT` 统计选课门数。

**本次漏掉的采分点：**
- `S.name` 字段名不对，题目需要的是 `S.Sname`；
- 既然结果要输出“学号 + 姓名 + 选课门数”，分组粒度也要稳定到 `S.Sno, S.Sname`；
- “每个学生 1 行”的结果意识已经有了，但还没彻底落到 SQL 外壳上。

**失分原因类型：**
- [ ] 不会知识点
- [x] 会概念但不会下手
- [x] 会分析但写不规范
- [ ] 只写结论没写依据
- [x] 漏步骤 / 漏条件 / 漏关键词
- [ ] 时间不足 / 表达仓促

**正确理解应为：**
- 这题的关键不是“会不会 `LEFT JOIN`”，而是**能不能把结果粒度一口气写完整**；
- 只要输出了姓名，就要考虑分组里是否也需要稳定地保留姓名；
- 统计题里“保留未选课学生”与“每个学生 1 行”要一起锁住。

**建议复习动作：**
1. 重写第21题时，先写中文：**每个学生 1 行，输出学号、姓名、门数**；
2. 再翻成 SQL：`SELECT` → `LEFT JOIN` → `GROUP BY`；
3. 补做 1 题“所有课程及选课人数（含 0 人课程）”的同型题。

#### 第22题（下午题）

**题型归属：** `DELETE` + `NOT EXISTS` 轻量子查询  
**你的实际作答：**
```sql
delete from Student S where S.Sno not exists 
(select SC.Sno from SC where S.Sno=SC.Sno);
```

**参考答案：**
```sql
DELETE FROM Student S
WHERE NOT EXISTS (
    SELECT 1
    FROM SC
    WHERE SC.Sno = S.Sno
);
```

**本题采分点：**
1. 使用 `DELETE FROM Student`；
2. 用 `NOT EXISTS` 表达“没有选课记录”；
3. 内外层通过 `Sno` 正确关联。

**本次已命中的采分点：**
- 知道主语句应从 `Student` 删除；
- 知道这题核心要用 `NOT EXISTS` 判断“没有匹配记录”；
- 内外层都围绕 `Sno` 关联。

**本次漏掉的采分点：**
- `NOT EXISTS` 的位置写错了，应该是 `WHERE NOT EXISTS (...)`，不能写成 `S.Sno not exists (...)`；
- 子查询存在感有了，但语法壳子没锁住。

**失分原因类型：**
- [ ] 不会知识点
- [x] 会概念但不会下手
- [x] 会分析但写不规范
- [ ] 只写结论没写依据
- [x] 漏步骤 / 漏条件 / 漏关键词
- [ ] 时间不足 / 表达仓促

**正确理解应为：**
- “没有相关记录”类题，真正的句型是 `WHERE NOT EXISTS (子查询)`；
- 内层只负责回答“有没有匹配行”，外层再决定删谁；
- 这类题的得分点常常不是思路，而是**把 `NOT EXISTS` 放在正确语法槽位里**。

**建议复习动作：**
1. 把第22题和第28题并排重写；
2. 固定背一行骨架：`WHERE NOT EXISTS (SELECT 1 FROM 子表 WHERE 子表.外键 = 主表.主键)`；
3. 再补做 1 题“删除没有加入社团的成员”。

#### 第24题（下午题）

**题型归属：** `INSERT ... SELECT`  
**你的实际作答：**
```sql
insert into HighSalary as select EmpNo,Ename from Emp where Salary > 8000;
```

**参考答案：**
```sql
INSERT INTO HighSalary (EmpNo, Ename)
SELECT EmpNo, Ename
FROM Emp
WHERE Salary > 8000;
```

**本题采分点：**
1. 写出 `INSERT INTO` 目标表；
2. `SELECT` 出符合条件的员工号与姓名；
3. 写出工资筛选条件。

**本次已命中的采分点：**
- 知道目标表是 `HighSalary`；
- 知道数据来源是 `Emp` 表中的 `EmpNo`、`Ename`；
- 知道筛选条件是 `Salary > 8000`。

**本次漏掉的采分点：**
- `INSERT INTO ... SELECT ...` 的语法外壳写坏了，多写了 `AS`；
- 没有把“目标表字段列表”和“来源查询结果”稳稳对齐写出来。

**失分原因类型：**
- [ ] 不会知识点
- [x] 会概念但不会下手
- [x] 会分析但写不规范
- [ ] 只写结论没写依据
- [x] 漏步骤 / 漏条件 / 漏关键词
- [ ] 时间不足 / 表达仓促

**正确理解应为：**
- `INSERT ... SELECT` 的重点不是只会写 `SELECT`，而是要把**目标表外壳**写对；
- 先写对 `SELECT`，再把它整体装进 `INSERT INTO 目标表 (字段...)` 里；
- `AS` 不是这里该出现的位置。

**建议复习动作：**
1. 重写第24题 2 遍；
2. 再手写 2 个骨架：`INSERT ... VALUES ...` 与 `INSERT ... SELECT ...`；
3. 口头复述一句：**先写来源 `SELECT`，再套目标表外壳。**

#### 第26题（下午题）

**题型归属：** `LEFT JOIN ... IS NULL` 无记录判断  
**你的实际作答：**
```sql
select D.Dno,D.Dname 
from Dept D left join Emp E on D.Dno=E.Dno 
group by D.Dno having Ename is null;
```

**参考答案：**
```sql
SELECT D.Dno, D.Dname
FROM Dept D
LEFT JOIN Emp E ON D.Dno = E.Dno
WHERE E.EmpNo IS NULL;
```

**本题采分点：**
1. 先 `LEFT JOIN`；
2. 再用 `WHERE E.EmpNo IS NULL` 判断无匹配；
3. 输出部门号与部门名。

**本次已命中的采分点：**
- 知道要先用 `LEFT JOIN` 保住部门表；
- 知道需要利用“右表为空”来判断没有员工。

**本次漏掉的采分点：**
- 这题不是统计题，不需要 `GROUP BY ... HAVING` 这一层；
- 应直接在 `WHERE` 里判断右表关键字段是否为 `NULL`；
- `Ename IS NULL` 不是最稳写法，标准口径应优先盯住右表主键 / 标识字段是否为空。

**失分原因类型：**
- [ ] 不会知识点
- [x] 会概念但不会下手
- [x] 会分析但写不规范
- [ ] 只写结论没写依据
- [x] 漏步骤 / 漏条件 / 漏关键词
- [ ] 时间不足 / 表达仓促

**正确理解应为：**
- 第23题和第26题要连着看：第23题是“保留全部部门并统计”，第26题是“从保留后的结果里筛出没有员工的部门”；
- 所以第26题的核心是 `LEFT JOIN` 之后**判空**，而不是继续做分组统计；
- “没有记录”类题一定先分清：到底是在做统计，还是在做空匹配筛选。

**建议复习动作：**
1. 把第23题和第26题并排重写；
2. 先用中文写：**保住所有部门 → 筛出右表没匹配的行**；
3. 再各自翻成 SQL，刻意区分“统计题”和“判空题”。

#### 第28题（下午题）

**题型归属：** `NOT EXISTS` 无记录判断  
**你的实际作答：**
```sql
select M.Sno,M.Sname from Member where M.Sno not exists 
(select J.Sno from JoinClub J where M.Sno=J.Sno)
```

**参考答案：**
```sql
SELECT M.Sno, M.Sname
FROM Member M
WHERE NOT EXISTS (
    SELECT 1
    FROM JoinClub J
    WHERE J.Sno = M.Sno
);
```

**本题采分点：**
1. 使用 `NOT EXISTS`；
2. 内外层通过 `Sno` 正确关联；
3. 输出成员学号与姓名。

**本次已命中的采分点：**
- 知道输出字段应该是成员学号与姓名；
- 知道这题本质还是“没有匹配记录”的 `NOT EXISTS`；
- 知道关联字段是 `Sno`。

**本次漏掉的采分点：**
- 主查询少了 `Member M` 的正确别名写法；
- `NOT EXISTS` 的落位仍然写错，和第22题是同一类语法问题；
- 这题说明你已经知道思路，但 **`WHERE NOT EXISTS (...)` 这个句型还没真正写顺**。

**失分原因类型：**
- [ ] 不会知识点
- [x] 会概念但不会下手
- [x] 会分析但写不规范
- [ ] 只写结论没写依据
- [x] 漏步骤 / 漏条件 / 漏关键词
- [ ] 时间不足 / 表达仓促

**正确理解应为：**
- 第22题和第28题本质同型，都是“主表中没有对应子表记录”的写法；
- 只要你把 `WHERE NOT EXISTS (...)` 模板写稳，这两题就应该一起拿分；
- 现在的问题不是方向错，而是**固定句型没写熟**。

**建议复习动作：**
1. 对照第22题一起重写；
2. 连写 3 个空壳模板：
   - `SELECT ... FROM 主表 别名 WHERE NOT EXISTS (...)`
   - `DELETE FROM 主表 别名 WHERE NOT EXISTS (...)`
   - `SELECT ... FROM 主表 LEFT JOIN 子表 ... WHERE 子表.主键 IS NULL`
3. 比较第22题与第28题：一个是删除，一个是查询，其他骨架几乎一样。

#### 第29题（下午题）

**题型归属：** `GROUP BY` + `HAVING` 统计查询  
**你的实际作答：**
```sql
select M.DeptNo 
from Member M join JoinClub J on M.Sno=J.Sno 
group by M.Sno having count(distinct(J.Sno))>2;
```

**参考答案：**
```sql
SELECT M.DeptNo, COUNT(DISTINCT M.Sno) AS MemberCount
FROM Member M
JOIN JoinClub J ON M.Sno = J.Sno
GROUP BY M.DeptNo
HAVING COUNT(DISTINCT M.Sno) >= 2;
```

**本题采分点：**
1. 按 `DeptNo` 分组；
2. 对参加社团的学生按学号去重计数；
3. 使用 `HAVING` 过滤人数不少于 2 的系别。

**本次已命中的采分点：**
- 知道这题要做 `JOIN`；
- 知道要用 `COUNT(DISTINCT ...)` 和 `HAVING` 这一类统计写法。

**本次漏掉的采分点：**
- 结果粒度应是“每个系别 1 行”，你却按 `M.Sno` 分组了；
- 题目要输出的是“系别 + 人数”，你只保留了 `DeptNo`；
- “不少于 2”应写 `>= 2`，你写成了 `> 2`。

**失分原因类型：**
- [ ] 不会知识点
- [x] 会概念但不会下手
- [x] 会分析但写不规范
- [ ] 只写结论没写依据
- [x] 漏步骤 / 漏条件 / 漏关键词
- [ ] 时间不足 / 表达仓促

**正确理解应为：**
- 统计题第一步永远是先锁结果粒度：这里是“每个系别 1 行”，不是“每个学生 1 行”；
- 粒度一错，后面的 `GROUP BY`、`COUNT`、`HAVING` 会连锁全漂；
- 这题比的不是会不会 `COUNT(DISTINCT)`，而是会不会先定“按谁分组”。

**建议复习动作：**
1. 重写第29题时先写中文：**按系别统计参加社团的学生人数**；
2. 再拆成 SQL：`SELECT 系别, COUNT(DISTINCT 学号)` → `GROUP BY 系别` → `HAVING >= 2`；
3. 再补做 1 题“按课程统计选课学生人数（去重）”。

### C. 下午题采分点未打满但暂不列为正式错题的题目

以下题目整体方向基本对，但严谨度仍不够，不能直接当成“已经完全稳了”。

- **第23题：采分点命中未打满。** 你已经选对了 `Dept LEFT JOIN Emp`，也知道要统计员工人数，整体逻辑方向是对的；但 `GROUP BY` 只写了 `D.Dno`，没有把 `D.Dname` 一起稳定下来。说明“保左表、再统计”这条主线已经接近可用，但书写严谨度还没完全锁死。
- **第25题：采分点命中未打满。** 你已经说明了 `LEFT JOIN` 能保留左表全部记录、`INNER JOIN` 会丢掉无匹配部门，这两点是核心得分点；但还少了“右表无匹配时字段为 `NULL`”这一层得分短句。属于方向对、表达还可以再更标准。
- **第27题：采分点命中未打满。** 你已经会写 `CREATE VIEW`、会从 `Club` 出发 `LEFT JOIN JoinClub`、也知道要统计社团人数并保留无成员社团；但 `GROUP BY` 只写了 `ClubNo`，没有把 `ClubName` 一起写稳。说明视图与外连接主线已经抓住，但分组粒度还不够严格。
- **第30题：采分点命中未打满。** 你已经按“输出粒度 → 关联表 → 行筛选 → 分组 / 排序 → 结果检查”的顺序写出来了，整体方向是正确的；但还可以再补一句“根据题意判断用 `INNER JOIN` 还是 `LEFT JOIN`”，以及更明确地区分 `WHERE` 与 `HAVING`。这题已经接近模板可用状态，但还差最后一点考试口径的精炼度。

### D. 本次下午题的整体状态说明

本次下午题**暂无完全满分且可直接视为“彻底写稳”的小问**。  
比较接近可用状态的是 **第23题、第25题、第27题、第30题**；而 **第19 / 20 / 21 / 22 / 24 / 26 / 28 / 29** 这 8 题仍然建议按正式错题处理并重写。

---

## 三、本次练习暴露出的关键易错点

### 1. 概念知道了，但 SQL 语法外壳还没锁死

Day 25 最突出的不是“完全不会”，而是：
- 知道 DDL 要有主键 / 外键 / 范围约束；
- 知道三表查询要连起来；
- 知道“没有记录”要用 `NOT EXISTS`；
- 知道“包括没有员工的部门”要先 `LEFT JOIN`；

但一到真正落笔，就容易出现：
- `NOT EXISTS` 放错位置；
- `INSERT ... SELECT` 外壳多写 `AS`；
- 连接写法混用；
- 字段名写错；
- 分组字段没跟着结果粒度一起补全。

一句话纠偏：

> **现在不是先补概念，而是先把高频 SQL 句型写熟。**

### 2. 结果粒度意识有了，但还没稳定带动 `GROUP BY`

第21题、第23题、第27题、第29题都反复出现同类问题：
- 你知道题目是在按“学生 / 部门 / 社团 / 系别”统计；
- 但分组时没有把这个粒度完整落到 SQL 里。

一句话纠偏：

> **先用中文说清“每一行代表谁”，再去写 `GROUP BY`。**

### 3. “没有记录”类题，两条模板还没真正写顺

第22题和第28题都说明了同一件事：
- 思路已经知道该用 `NOT EXISTS`；
- 但 `WHERE NOT EXISTS (...)` 这个壳子还不熟。

第26题又暴露出另一条模板也不稳：
- `LEFT JOIN ... WHERE 右表主键 IS NULL`

一句话纠偏：

> **“没有记录”类题只练两套模板：`NOT EXISTS` 和 `LEFT JOIN ... IS NULL`，直到不犹豫。**

### 4. DDL 与查询题的“先后顺序”还不够机械化

- DDL 题该按：**主键 → 外键 → 非空 / `CHECK`**；
- 查询题该按：**输出字段 / 粒度 → 主表 / 连接 → `WHERE` → `GROUP BY` → `HAVING` → 结果检查**。

现在你的问题不是不知道这些顺序，而是顺序还没到“几乎条件反射”的程度。

---

## 四、Day 25 需要继续巩固的重点

1. 再练 **DDL 关键约束模板**：主键、外键、`CHECK` 的顺序要写机械化；
2. 再练 **三表连接题**：字段来自哪张表、连接条件怎么写、筛选条件怎么落；
3. 再练 **`WHERE NOT EXISTS (...)` 固定句型**：至少把第22题、第28题各重写 2 遍；
4. 再练 **`LEFT JOIN ... WHERE 右表主键 IS NULL` 固定句型**：重点重写第26题；
5. 再练 **结果粒度 → `GROUP BY`** 的绑定关系：重点重写第21题、第23题、第27题、第29题；
6. 再练 **`INSERT ... SELECT` 标准外壳**：重点重写第24题；
7. 优先重写：**第19题、第20题、第21题、第22题、第24题、第26题、第28题、第29题**。

---

## 五、给明日学习的提醒

- 从 **上午客观题 18 / 18** 看，Day 25 的 SQL 基础概念没有掉线；
- 但从 **下午题采分点命中率 60.53%** 看，当前还不能算“SQL 书写链已经稳定”；
- 因此 Day 26 更稳妥的推进口径是：
  1. **先重写 Day 25 的 8 道正式错题**，尤其是第22题、第24题、第26题、第28题这四道句型题；
  2. 若重写后已经能稳定写出 `NOT EXISTS`、`LEFT JOIN ... IS NULL`、`INSERT ... SELECT` 和 `GROUP BY` 粒度链，再进入 SQL 专项 2；
  3. 若第21题、第26题、第29题仍反复漂移，则 Day 26 不要急着推更复杂的 SQL 综合题，先把 SQL 专项 1 二刷到位。

---

## 六、一句话复盘

Day 25 的核心状态是：**SQL 概念题已经稳住了，但下午题还停在“知道该怎么做、却还没把句型和粒度写成肌肉记忆”的阶段；下一步最该补的不是新知识，而是把 DDL、`NOT EXISTS`、外连接判空、分组统计这几条高频书写模板真正写顺。**
