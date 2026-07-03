---
title: Day 26 错题复盘
type: mistake-review
status: active
project: 软考中级数据库系统工程师备考
area: Career and Certification
exam: 数据库系统工程师
exam_level: 软考中级
stage: mistake-review
category: wrong-answers
day: 26
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

# Day 26 错题复盘

> 本次错题复盘的核查依据：严格以 `Day 26.md` 中每道题的**实际作答内容**为准，并与“答案解析”区标准答案 / 参考答案 / 采分点逐题对比后整理。  
> 说明：  
> 1. 客观题以题目 `（ ）` 内填写的答案为准；  
> 2. 下午题 / 主观题以作答区中的实际文字答案为准；  
> 3. 只有真实写下的作答痕迹，才纳入正式核查；  
> 4. 本次 Day 26 共核查 **30 题**，其中客观题 **18 题**、下午题小问 **12 题**。

## 一、本次核查结果

- 总题数：30
- 已作答题数：30
- 正确题数：21（按正式判错口径）
- 错误题数：9
- 未作答题数：0
- 正确率：70.00%（按正式判错口径）

- 上午客观题总数：18
- 上午客观题正确数：17
- 上午客观题错误数：1
- 上午客观题正确率：94.44%

- 下午题作答组数：3（共 12 小问）
- 下午题采分点总数：38
- 下午题已命中采分点数：25
- 下午题采分点命中率：65.79%

> 结论：Day 26 的**上午基础题仍然比较稳**，18 题里只错了 **第10题**，说明你对 `NOT EXISTS`、`LEFT JOIN`、`HAVING`、视图、触发器等概念识别总体没有掉线。  
> 但下午题仍然暴露出几条比较集中的结构性问题：**统计题的结果粒度没有稳定带动 `GROUP BY`、视图题输出字段与视图属性没有完全对齐、最多 / 最大统计题的 `HAVING + 子查询` 外壳还不够稳、无记录查询虽方向对但语法壳子仍会漏别名或字段名**。  
> 与 Day 25 相比，Day 26 的下午题采分点命中率从 **60.53%** 提升到 **65.79%**，说明 SQL 句型纠偏**有进步，但还没到可以放心切专题的程度**；当前更像是“核心模板开始成形，但统计粒度与严谨书写还没完全锁死”。

---

## 二、错题明细

### A. 客观题正式错题

#### 第10题

- **你的实际作答：** A
- **正确答案：** B
- **错因：** 删除学生后需要引用的是**被删行的旧值**，即 `old row`，你把它误判成了 `new row`。
- **正确理解：**
  - `new row`：用于插入后的新行，或更新后的新值；
  - `old row`：用于删除前的旧行，或更新前的旧值；
  - 题目是“删除学生后自动减少学院人数”，因此必须知道**被删学生原来属于哪个学院**，要用 `old row`。
- **建议复习动作：**
  1. 把 `new row` / `old row` 的使用场景各口头复述 1 遍；
  2. 把第10题和第30题连起来看：客观题判断概念，下午题落实到触发器骨架；
  3. 强化一句话：**插入看 `new`，删除看 `old`，更新通常两者都可能要看。**

### B. 下午题 / 主观题正式错题

#### 第20题（下午题）

**题型归属：** `LEFT JOIN ... IS NULL` 判空查询  
**你的实际作答：**
```sql
select D.Dno,D.name from Dept D left jion Emp E on D.Dno=E.Dno where E.EmpNo is null;
```

**参考答案：**
```sql
SELECT D.Dno, D.Dname
FROM Dept D
LEFT JOIN Emp E ON D.Dno = E.Dno
WHERE E.EmpNo IS NULL;
```

**本题采分点：**
1. 从 `Dept` 出发做 `LEFT JOIN`；
2. 用右表关键字段 `E.EmpNo IS NULL` 判断无匹配；
3. 正确输出部门号与部门名。

**本次已命中的采分点：**
- 知道要从 `Dept` 出发保留全部部门；
- 知道要通过 `E.EmpNo IS NULL` 判断“没有员工”。

**本次漏掉的采分点：**
- `D.name` 字段名写错，标准应为 `D.Dname`；
- `left jion` 存在关键字拼写错误；
- 这题说明主线方向对了，但**字段名与 SQL 语法外壳还没完全锁死**。

**失分原因类型：**
- [ ] 不会知识点
- [x] 会概念但不会下手
- [x] 会分析但写不规范
- [ ] 只写结论没写依据
- [x] 漏步骤 / 漏条件 / 漏关键词
- [ ] 时间不足 / 表达仓促

**正确理解应为：**
- “没有员工的部门”这类题，核心不是统计，而是**保住左表后直接判空**；
- 一旦主线选成 `LEFT JOIN + 右表关键字段 IS NULL`，后面最容易丢分的就是字段名和语法细节；
- 现在不是思路错，而是**标准壳子还没写到不出手误**。

**建议复习动作：**
1. 重写第20题 2 遍；
2. 连写 3 个模板：
   - `LEFT JOIN ... WHERE 右表主键 IS NULL`
   - `WHERE NOT EXISTS (...)`
   - `GROUP BY ... HAVING ...`
3. 强化一句话：**判空题先保主表，再判右表关键字段为空。**

#### 第22题（下午题）

**题型归属：** `GROUP BY + HAVING` 统计查询  
**你的实际作答：**
```sql
select M.DeptNo,count(M.Sno) from Member M join JoinClub J on M.Sno=J.Sno group by J.Sno,J.ClubNo having count(distinct J.Sno) >= 2;
```

**参考答案：**
```sql
SELECT M.DeptNo, COUNT(DISTINCT M.Sno) AS StudentCount
FROM Member M
JOIN JoinClub J ON M.Sno = J.Sno
GROUP BY M.DeptNo
HAVING COUNT(DISTINCT M.Sno) >= 2;
```

**本题采分点：**
1. 按 `DeptNo` 分组；
2. 使用 `COUNT(DISTINCT M.Sno)` 去重计数；
3. 使用 `HAVING ... >= 2` 过滤。

**本次已命中的采分点：**
- 知道需要通过 `Member` 与 `JoinClub` 连接；
- 有“去重统计”和 `HAVING >= 2` 的意识。

**本次漏掉的采分点：**
- 结果粒度应是“**每个系别 1 行**”，你却按 `J.Sno, J.ClubNo` 分组了；
- `SELECT` 中写的是 `count(M.Sno)`，没有把去重计数稳定写在结果列里；
- 这题最大问题不是不会 `HAVING`，而是**结果粒度一偏，整个 `GROUP BY` 链就跟着漂了**。

**失分原因类型：**
- [ ] 不会知识点
- [x] 会概念但不会下手
- [x] 会分析但写不规范
- [ ] 只写结论没写依据
- [x] 漏步骤 / 漏条件 / 漏关键词
- [ ] 时间不足 / 表达仓促

**正确理解应为：**
- 题目问的是“按系别统计参加社团的学生人数”，所以第一步必须先写中文：**每个系别 1 行**；
- 一旦粒度锁成“系别”，`SELECT` 与 `GROUP BY` 就都要围绕 `DeptNo`；
- “按学号去重”是计数规则，不是分组规则。

**建议复习动作：**
1. 先用中文重写 1 遍：**按系别统计去重后的参加社团学生人数**；
2. 再翻译成 SQL：`SELECT DeptNo, COUNT(DISTINCT Sno)` → `GROUP BY DeptNo` → `HAVING >= 2`；
3. 再补做 1 题同型题：按课程统计选课学生人数（按学号去重）。

#### 第23题（下午题）

**题型归属：** 统计视图 / `CREATE VIEW + GROUP BY`  
**你的实际作答：**
```sql
create view SALES_Total as  
select S.VEMno,S.Gno,S.SDate,count(S.Sno) as  Amount from SALES S join GOODS G on S.Gno=G.Gno join Vem V on S.VEMno = V.VEMno  group by S.Gno,S.SDate;
```

**参考答案：**
```sql
CREATE VIEW SALES_Total (VEMno, Gno, SDate, Amount) AS
SELECT VEMno, Gno, SDate, COUNT(*) AS Amount
FROM SALES
GROUP BY VEMno, Gno, SDate;
```

**本题采分点：**
1. 使用 `CREATE VIEW`；
2. 视图字段与 `SELECT` 字段对应；
3. 按 `VEMno, Gno, SDate` 三维分组统计。

**本次已命中的采分点：**
- 知道要用 `CREATE VIEW`；
- 知道核心统计来自 `SALES` 表，并且要按销售数量做聚合。

**本次漏掉的采分点：**
- `GROUP BY` 漏掉了 `S.VEMno`，结果粒度不完整；
- 这题并不需要额外连接 `GOODS`、`VEM`，你把视图本该只做的统计层写复杂了；
- 视图字段列表没有显式写出，字段对应关系不够稳定。

**失分原因类型：**
- [ ] 不会知识点
- [x] 会概念但不会下手
- [x] 会分析但写不规范
- [ ] 只写结论没写依据
- [x] 漏步骤 / 漏条件 / 漏关键词
- [ ] 时间不足 / 表达仓促

**正确理解应为：**
- 这题的关键不是“多表连接”，而是先把**统计视图本身的最小骨架**写稳；
- 题干说“每台售货机每天每种商品”，所以分组维度必须完整包含 `VEMno + Gno + SDate`；
- 统计视图题先做统计层，再考虑后续明细视图，不要一上来把附属表全连进来。

**建议复习动作：**
1. 重写第23题时，先写中文粒度：**每台售货机 / 每天 / 每种商品 1 行**；
2. 再按最小骨架写：`CREATE VIEW ... AS SELECT ... FROM SALES GROUP BY ...`；
3. 与第24题对照：先做 `SALES_Total`，再做 `SALES_Detail`。

#### 第24题（下午题）

**题型归属：** 多表连接视图  
**你的实际作答：**
```sql
create view SALES_Detail as
select S.VEMno,V.Location,S.Gno,G.Brand,G.Price,S.Amount,S.Amount   from SALES_Total S join Vem V on  V.VEMno=S.VEMno join GOODS G on S.Gno = G.Gno group by VEMno,Gno;
```

**参考答案：**
```sql
CREATE VIEW SALES_Detail (VEMno, Location, Gno, Brand, Price, Amount, SDate) AS
SELECT V.VEMno, V.Location, G.Gno, G.Brand, G.Price, T.Amount, T.SDate
FROM SALES_Total T
JOIN VEM V ON T.VEMno = V.VEMno
JOIN GOODS G ON T.Gno = G.Gno;
```

**本题采分点：**
1. 使用 `CREATE VIEW`；
2. 正确连接 `SALES_Total`、`VEM`、`GOODS`；
3. 输出字段与视图属性对应。

**本次已命中的采分点：**
- 知道要从 `SALES_Total` 出发再连接 `VEM` 和 `GOODS`；
- 大体知道输出中应包含位置、品牌、价格、销量等信息。

**本次漏掉的采分点：**
- 最后一个字段应为 `SDate`，你写成了第二个 `S.Amount`；
- 这题本身不是统计题，不需要再加 `GROUP BY`；
- 视图字段列表没有明确写出，输出列与题目属性顺序还不够稳。

**失分原因类型：**
- [ ] 不会知识点
- [x] 会概念但不会下手
- [x] 会分析但写不规范
- [ ] 只写结论没写依据
- [x] 漏步骤 / 漏条件 / 漏关键词
- [ ] 时间不足 / 表达仓促

**正确理解应为：**
- `SALES_Detail` 是在 `SALES_Total` 统计结果之上做**补充属性连接**，不是再做一层聚合；
- 所以核心动作是“表连对、字段列对、顺序对”；
- 你已经把表关系方向抓住了，但**输出字段对齐**还没真正机械化。

**建议复习动作：**
1. 重写第24题时先把视图属性抄一遍：`(VEMno, Location, Gno, Brand, Price, Amount, SDate)`；
2. 再逐个把 `SELECT` 字段对上去；
3. 强化一句话：**统计视图先统计，明细视图再补属性，不要重复分组。**

#### 第25题（下午题）

**题型归属：** 最多统计 / `GROUP BY + HAVING`  
**你的实际作答：**
```sql
select G.Gno,G.Brand,count(S.Sno) from GOODS G join G.Gno = S.Gno group by S.Gno having S.SDate=GetDate desc limit 1;
```

**参考答案：**
```sql
SELECT G.Gno, G.Brand, COUNT(*) AS SaleCount
FROM GOODS G
JOIN SALES S ON G.Gno = S.Gno
WHERE S.SDate = GetDate()
GROUP BY G.Gno, G.Brand
HAVING COUNT(*) >= ALL (
    SELECT COUNT(*)
    FROM SALES S2
    WHERE S2.SDate = GetDate()
    GROUP BY S2.Gno
);
```

**本题采分点：**
1. 用 `WHERE SDate = GetDate()` 筛当天销售记录；
2. 外层按商品分组统计；
3. 用 `HAVING` 与内层最大 / 全部计数比较，支持并列最多。

**本次已命中的采分点：**
- 知道这题最终还是要统计商品销售次数；
- 有“找最多”这一层意识。

**本次漏掉的采分点：**
- `JOIN` 语法壳子写坏了，缺少 `SALES S ON G.Gno = S.Gno`；
- 当天日期条件应写在 `WHERE`，你却混进了 `HAVING`；
- 题目要求“若并列最多，应都输出”，不能用 `DESC LIMIT 1` 这种只保一条的思路；
- `HAVING + 子查询` 的最大值比较结构没有真正写稳。

**失分原因类型：**
- [ ] 不会知识点
- [x] 会概念但不会下手
- [x] 会分析但写不规范
- [ ] 只写结论没写依据
- [x] 漏步骤 / 漏条件 / 漏关键词
- [ ] 时间不足 / 表达仓促

**正确理解应为：**
- “最多 / 最大”统计题不是简单排序后拿第一条，而是要**显式支持并列最大**；
- 这类题固定句序应是：**先筛当天 → 外层分组统计 → 内层求各组计数 → `HAVING >= ALL (...)` 比较**；
- 现在最需要补的是这条“最大统计模板”。

**建议复习动作：**
1. 把第25题和第29题并排重写；
2. 固定背一行模板：`HAVING COUNT(*) >= ALL (SELECT COUNT(*) FROM ... GROUP BY ...)`；
3. 再补做 1 题“调度次数最多的汽车”同型题。

#### 第26题（下午题）

**题型归属：** 无记录查询 / `NOT EXISTS` 或 `NOT IN`  
**你的实际作答：**
```sql
select G.Gno,G.Brand from GOODS where G.Gno not in (select S.Gno from SALES S);
```

**参考答案：**
```sql
SELECT G.Gno, G.Brand
FROM GOODS G
WHERE NOT EXISTS (
    SELECT 1
    FROM SALES S
    WHERE S.Gno = G.Gno
);
```

也可写为：
```sql
SELECT Gno, Brand
FROM GOODS
WHERE Gno NOT IN (
    SELECT Gno
    FROM SALES
);
```

**本题采分点：**
1. 从 `GOODS` 出发；
2. 判断不存在销售记录；
3. 输出商品编号和品牌。

**本次已命中的采分点：**
- 知道这题要从 `GOODS` 出发；
- 知道可以用 `NOT IN` 判断“未售出”；
- 知道应输出商品编号和品牌。

**本次漏掉的采分点：**
- 主表没有给别名 `G`，却在 `SELECT` 和 `WHERE` 中直接使用了 `G.Gno`、`G.Brand`；
- 这说明思路是对的，但**实际写下的 SQL 壳子还不完整**；
- 如果要走最稳考试口径，`NOT EXISTS` 会比 `NOT IN` 更不容易受 `NULL` 影响。

**失分原因类型：**
- [ ] 不会知识点
- [x] 会概念但不会下手
- [x] 会分析但写不规范
- [ ] 只写结论没写依据
- [x] 漏步骤 / 漏条件 / 漏关键词
- [ ] 时间不足 / 表达仓促

**正确理解应为：**
- 你已经知道“没有售出”本质是主表中找不到对应子表记录；
- 现在的问题不是方向，而是**别名、字段、标准壳子没有一次写全**；
- 同类题最好练两套模板：`NOT EXISTS` 和 `NOT IN`，并优先把 `NOT EXISTS` 练成第一反应。

**建议复习动作：**
1. 把第19题和第26题放在一起重写，分别用 `NOT EXISTS` 与 `NOT IN`；
2. 每次写 SQL 前先确认：主表有没有起别名，后续字段是不是都和别名一致；
3. 强化一句话：**方向对不等于得分，别名漏了照样会失分。**

#### 第28题（下午题）

**题型归属：** 多表连接视图 / 条件过滤  
**你的实际作答：**
```sql
create view AudiSCHEDULE as 
select E.Eno,E.Ename,C.Cno,C.Brand,S.StartTime,S.EndTime from EMP E join SCHEDULE S on E.Eno = S.Eno join CAR C on S.Cno=C.Cno group by E.Eno,C.Cno having C.Brand='奥迪';
```

**参考答案：**
```sql
CREATE VIEW AudiSCHEDULE (Eno, Ename, Cno, Brand, StartTime, EndTime) AS
SELECT E.Eno, E.Ename, C.Cno, C.Brand, S.StartTime, S.EndTime
FROM EMP E
JOIN SCHEDULE S ON E.Eno = S.Eno
JOIN CAR C ON S.Cno = C.Cno
WHERE C.Brand = '奥迪';
```

**本题采分点：**
1. 使用 `CREATE VIEW`；
2. 三表连接关系正确；
3. 用 `Brand = '奥迪'` 过滤。

**本次已命中的采分点：**
- 知道要用 `CREATE VIEW`；
- 三张表的连接方向基本正确；
- 知道要筛出 `奥迪` 品牌。

**本次漏掉的采分点：**
- 这题不是统计题，不需要 `GROUP BY`；
- 品牌筛选应放在 `WHERE`，不应写成 `HAVING`；
- 视图字段列表没有显式给出，标准答案口径还不够完整。

**失分原因类型：**
- [ ] 不会知识点
- [x] 会概念但不会下手
- [x] 会分析但写不规范
- [ ] 只写结论没写依据
- [x] 漏步骤 / 漏条件 / 漏关键词
- [ ] 时间不足 / 表达仓促

**正确理解应为：**
- 只要题目没有统计量，就要警惕自己是不是把普通查询题误写成了 `GROUP BY / HAVING`；
- 第24题和第28题可以连着看：都是视图题，但本质上都是**连接 + 输出 + 条件**，不是统计题；
- 现在你对表关系已经有了，下一步要补的是**什么时候该用 `WHERE`，什么时候才用 `HAVING`**。

**建议复习动作：**
1. 把第24题和第28题并排重写；
2. 在每题开头先判断：有没有聚合函数、有没有分组需求；
3. 强化一句话：**没有统计量的视图题，先想 `WHERE`，不要手滑写 `HAVING`。**

#### 第29题（下午题）

**题型归属：** 最多统计 / `HAVING + 子查询`  
**你的实际作答：**
```sql
select C.Cno,C.Brand from CAR C join SCHEDULE S on  C.Cno=S.Cno group by C.con having count(* ) >= all (select count(* ) from SCHEDULE S group by S.Sno,S.cno)
```

**参考答案：**
```sql
SELECT C.Cno, C.Brand
FROM CAR C
JOIN SCHEDULE S ON C.Cno = S.Cno
GROUP BY C.Cno, C.Brand
HAVING COUNT(*) >= ALL (
    SELECT COUNT(*)
    FROM SCHEDULE
    GROUP BY Cno
);
```

**本题采分点：**
1. 按汽车分组统计调度次数；
2. 用 `HAVING` 比较最大调度次数；
3. 支持并列最多。

**本次已命中的采分点：**
- 知道要用 `HAVING >= ALL (...)` 处理“并列最多”；
- 知道应围绕 `CAR` 与 `SCHEDULE` 做统计。

**本次漏掉的采分点：**
- `group by C.con` 字段名写错，标准应为 `C.Cno`，且还应同时稳定输出 `C.Brand`；
- 内层子查询应按 `Cno` 分组，你却写成了 `S.Sno, S.cno`，导致“每车调度次数”的统计口径跑偏；
- 这题说明你已经知道“最大统计”要用子查询，但**内外层统计口径还没真正对齐**。

**失分原因类型：**
- [ ] 不会知识点
- [x] 会概念但不会下手
- [x] 会分析但写不规范
- [ ] 只写结论没写依据
- [x] 漏步骤 / 漏条件 / 漏关键词
- [ ] 时间不足 / 表达仓促

**正确理解应为：**
- 外层统计什么，内层就要按同一口径去比较最大值；
- 这里是“每辆车的调度次数”，所以内层只能按 `Cno` 分组，不能掺进 `Sno`；
- 这类题的关键不是背 `ALL`，而是**把内外层统计口径写成一套**。

**建议复习动作：**
1. 把第25题和第29题一起重写；
2. 每次写“最多统计”题时，先写中文：**外层按谁分组？内层比较的也是谁？**；
3. 再补做 1 题“销售数量最多的商品”同型题。

### C. 下午题采分点未打满但暂不列为正式错题的题目

以下题目整体方向基本正确，但仍存在补强空间，暂不直接当成“已经完全稳了”。

- **第21题：采分点命中未打满。** 你已经会写 `INSERT INTO ... SELECT ...` 的主壳，也写出了正确的数据来源与筛选条件；但目标字段列表没有显式写出。方向基本对，标准性还可以再提高。
- **第27题：整体结构正确。** 你已经按“主键 → 双外键 → `CHECK` 约束”的顺序写出了 `SCHEDULE` 表骨架，这比 Day 25 的 DDL 题明显更稳；后续可以把时间类型与书写格式再统一得更标准一些，但本题主干已经立住了。

### D. 下午题相对稳定的题目

- **第19题：结构稳定。** `WHERE NOT EXISTS (...)` 这一题终于写对了，说明 Day 25 暴露出的 `NOT EXISTS` 落位问题已经开始纠偏成功。
- **第30题：结构稳定。** 触发器题已经能把**触发表、触发时机、新旧行引用、插入加 1、删除减 1**完整写出来，说明触发器骨架这条线在 Day 26 已经明显成形。

---

## 三、本次练习暴露出的关键易错点

### 1. 统计题的“结果粒度 → `GROUP BY`”仍然最容易漂

Day 26 最集中的问题仍然在：
- 第22题按系别统计，却按学号 / 社团号分组；
- 第23题按售货机 / 商品 / 日期统计，却漏掉 `VEMno`；
- 第29题按汽车统计，却把内外层统计口径写散了。

一句话纠偏：

> **先用中文写“每一行代表谁”，再写 `GROUP BY`；分组字段跟着结果粒度走，不跟着你脑子里第一个想到的表字段走。**

### 2. 视图题已经知道“该连哪些表”，但“输出字段与视图属性对齐”还没机械化

Day 26 的第23 / 24 / 28题都说明：
- 你对视图题的大方向已经有了；
- 但一到真正落笔，容易出现：
  - 该只统计却连太多表；
  - 该直接连接补属性却又加 `GROUP BY`；
  - 视图最后一列写错，字段顺序没有完全对上。

一句话纠偏：

> **视图题先抄视图属性，再逐个回填 `SELECT` 字段；统计视图先统计，明细视图再补属性。**

### 3. “最多 / 最大”统计模板还没完全写顺

第25题和第29题暴露的是同一个核心问题：
- 你已经知道“最多”要用 `HAVING` 和子查询；
- 但还没把“日期筛选在哪、外层按谁分组、内层按谁比较、如何支持并列最多”写成固定模板。

一句话纠偏：

> **最多统计题固定五步：先筛范围 → 外层分组 → 内层计数 → `HAVING >= ALL (...)` → 检查是否支持并列。**

### 4. “没有记录”类题在进步，但语法壳子还会漏小口子

好消息是：
- 第19题 `NOT EXISTS` 已经写对了；
- 第20题、第26题方向也都选对了。

但问题仍是：
- 别名漏掉；
- 字段名写错；
- `JOIN` 拼写手误；
- 方向对了，最后壳子没扣严。

一句话纠偏：

> **无记录题不再是“不会”，而是“还没稳到零手误”。**

### 5. DDL 与触发器比 Day 25 明显更稳

这是 Day 26 的一个明确进步点：
- 第27题的主键 / 外键 / `CHECK` 主链已经立住；
- 第30题的触发器骨架已经能完整写出。

一句话判断：

> **Day 26 不是全面失守，而是 SQL 里“结构型题在变稳，统计型题还在拉扯”。**

---

## 四、Day 26 需要继续巩固的重点

1. 再练 **统计题结果粒度**：先写“每个谁 1 行”，再写 `GROUP BY`；
2. 再练 **统计视图最小骨架**：尤其是第23题、第24题；
3. 再练 **最多统计模板**：重点重写第25题、第29题；
4. 再练 **无记录题的标准壳子**：重点重写第20题、第26题；
5. 再练 **`WHERE` 与 `HAVING` 分工**：没有聚合时优先 `WHERE`，不要把普通筛选写进 `HAVING`；
6. 优先重写：**第10题、第20题、第22题、第23题、第24题、第25题、第26题、第28题、第29题**。

---

## 五、给 Day 27 的提醒

- 从 **上午题 17/18** 看，Day 26 的 SQL 基础识别仍然比较稳；
- 但从 **下午题采分点命中率 65.79%** 看，当前仍处于项目既定阈值里的 **“60%～74%：次日增加同类题 20～30 分钟”** 区间；
- 因此，**Day 27 不建议直接把 SQL 完全放下后切到恢复 / 并发专项**，更稳妥的安排是：
  1. 先二刷 Day 25～Day 26 的 SQL 高频句型；
  2. 优先重写第 **20 / 22 / 23 / 24 / 25 / 26 / 28 / 29** 题；
  3. 把 `NOT EXISTS`、`LEFT JOIN ... IS NULL`、`GROUP BY + HAVING`、统计视图、最多统计模板五条主线写顺；
  4. 若二刷后这些题能稳定写对，再切入恢复 / 并发会更稳。

---

## 六、一句话复盘

Day 26 的核心状态是：**SQL 句型纠偏已经比 Day 25 前进了一步，`NOT EXISTS`、DDL、触发器开始稳住，但统计视图、结果粒度和“最多统计”这三条线还没真正写成肌肉记忆；下一步最该补的不是新概念，而是把这些高频统计模板彻底写顺。**
