---
title: Day 26
type: study-note
status: active
project: 软考中级数据库系统工程师备考
area: Career and Certification
exam: 数据库系统工程师
exam_level: 软考中级
stage: daily-study
category: daily-learning
day: 26
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

# Day 26｜2026-05-06｜距离考试 17 天｜SQL 专项 2（纠偏推进版）：连接、分组、子查询、视图与触发器骨架

> 状态说明：**这是 Day 26 的待学习任务 / 学习资料**，用于继续推进数据库系统工程师备考内容，**不代表今日学习已经完成**。  
> 学习顺序：**先复盘 Day 25 的正式错题复盘结果，再进入 Day 26 新内容学习与练习。**  
> 题源规则：今日练习优先采用 **2016～2023 可靠真题池**中的 SQL 题型主线、真题改编题与真题风格整理题；**2024～2025** 题源在 OCR 补证前不作为已验证题源使用。  
> 今日训练定位：参考 `00_整体备考规划.md` 中 **SQL 专项 2：连接、分组、子查询、2016～2023 SQL 题抽做** 的安排；但由于 Day 25 下午题采分点命中率为 **60.53%**，处于“60%～74%：次日增加同类题 20～30 分钟”的区间，因此 Day 26 按“**先纠偏 Day 25 高频句型，再推进 SQL 综合表达**”执行。  
> 今日训练重心：把 Day 25 暴露出的 `NOT EXISTS`、`LEFT JOIN ... IS NULL`、`INSERT ... SELECT`、`GROUP BY` 粒度链补稳，再进入 2016～2019 文本化真题中反复出现的 **视图、统计查询、触发器骨架、`HAVING` 子查询、未出现记录查询**。

---

## 1. 今日学习目标

1. 先复盘 Day 25 的正式错题，尤其重写第 **19 / 20 / 21 / 22 / 24 / 26 / 28 / 29** 题，把 SQL 高频句型先补稳。
2. 理解 Day 26 为什么继续推进 SQL 专项 2：规划中 5/9 对应 SQL 专项 2，且 2016～2023 下午真题中 SQL 几乎每年稳定出现。
3. 能把 Day 25 的“输出粒度 → 主表 / 连接 → 行筛选 → 分组统计 → 结果检查”句序转化成可落笔 SQL。
4. 掌握今天的 SQL 综合核心链：**多表连接、`GROUP BY + HAVING`、`EXISTS / NOT EXISTS`、`NOT IN`、视图、触发器更新骨架**。
5. 完成“上午基础巩固 + 下午案例专项”的双轨训练，上午题保概念，下午题练写法。
6. 对 SQL 下午题形成至少 3 套可复写模板：**统计视图模板、无记录查询模板、触发器维护模板**。
7. 为 Day 27 是否进入“恢复与并发专项”提供依据：若今天 SQL 句型仍漂，Day 27 前必须先完成 SQL 二刷；若明显稳定，再按规划进入恢复 / 并发。

---

## 2. 学习内容

### 2.1 今日前置动作（先做，再学新内容）

> 根据项目正式规则，Day 2 及以后必须先复盘前一天错题，再进入当天新知识。

- [ ] 先打开并复习：`02_错题复盘/Day 25 错题复盘.md`
- [ ] 先重写 Day 25 的 8 道正式错题：
  - **第19题**：`CREATE TABLE` 中主键、外键、`CHECK` 的顺序
  - **第20题**：三表连接统一使用显式 `JOIN`，不要混写
  - **第21题**：输出姓名时，`GROUP BY` 也要稳定到对应粒度
  - **第22题**：`WHERE NOT EXISTS (...)` 的固定句型
  - **第24题**：`INSERT INTO 目标表(字段) SELECT ...` 的外壳
  - **第26题**：`LEFT JOIN ... WHERE 右表主键 IS NULL`，不要误写成 `HAVING`
  - **第28题**：`NOT EXISTS` 查询句型与主表别名
  - **第29题**：先定“每个系别 1 行”，再写 `GROUP BY DeptNo`
- [ ] 用 5 分钟默写以下 4 个 SQL 骨架：
  - `WHERE NOT EXISTS (SELECT 1 FROM 子表 WHERE 子表.外键 = 主表.主键)`
  - `LEFT JOIN 子表 ON ... WHERE 子表.主键 IS NULL`
  - `INSERT INTO 目标表(字段1, 字段2) SELECT 字段1, 字段2 FROM 来源表 WHERE 条件`
  - `SELECT 分组字段, COUNT(...) FROM ... GROUP BY 分组字段 HAVING COUNT(...) >= 条件`
- [ ] 若前置重写仍写不顺，今天 B 轨第19～24题必须优先完成，第25～30题可作为加练；不得为了推进新题而跳过 Day 25 纠偏。
- [ ] 检查今日题源口径：今日 SQL 题型参考 2016～2019 文本化下午真题中的车辆调度、销售统计、租车不良记录、俱乐部人数统计等 SQL 主线，并结合 2020～2023 SQL 高频方向做改编。

> 注意：以上是 Day 26 学习前置动作清单，不代表这些动作已经完成。

### 2.2 为什么今天进入 SQL 专项 2（纠偏推进）

`00_整体备考规划.md` 对阶段 B 的安排是：5/8 进入 SQL 专项 1，5/9 进入 SQL 专项 2，目标是把 SQL DDL / 查询 / 子查询 / 统计 / 视图等下午题高频写法做成模板。

Day 25 的真实复盘结果也给出了明确依据：

- 上午客观题 **18 / 18**，说明 SQL 基础概念识别没有明显问题；
- 下午题采分点 **23 / 38**，命中率 **60.53%**，说明 SQL 书写链还没有稳定；
- 主要问题不是“不知道用什么”，而是：
  - `NOT EXISTS` 放错位置；
  - `LEFT JOIN ... IS NULL` 与 `GROUP BY / HAVING` 混写；
  - `INSERT ... SELECT` 外壳不规范；
  - 分组字段没有跟着输出粒度完整写出。

所以 Day 26 的定位不是单纯“上新内容”，而是：

> **先把 Day 25 的 SQL 高频句型纠偏到可用，再用 2016～2019 文本化真题中的视图、统计、触发器、无记录查询主线做真题化推进。**

### 2.3 今日核心知识框架

今天要固定的是 SQL 下午题的五步执行链：

> **题型识别 → 输出粒度 → 连接 / 子查询 → 分组与筛选 → 结果边界检查**

#### 1）题型识别：先判断这题让你“写什么”

SQL 下午题常见任务包括：

- 补 `CREATE TABLE`：重点是主键、外键、`CHECK`、`NOT NULL`；
- 建视图：重点是 `CREATE VIEW ... AS SELECT ...`；
- 写查询：重点是连接、筛选、分组、排序；
- 写插入 / 删除：重点是目标表和筛选条件；
- 补触发器：重点是触发时机、触发表、引用新旧行、更新 / 插入动作。

第一步如果分错题型，后面的 SQL 再像也容易偏题。

#### 2）输出粒度：每一行到底代表什么

统计题必须先问：

- 每个驾驶员 1 行？
- 每辆车 1 行？
- 每个售货机 + 商品 + 日期 1 行？
- 每个社团 1 行？
- 每个学院 1 行？

一旦输出粒度确定，`SELECT` 与 `GROUP BY` 就必须保持一致。

#### 3）连接 / 子查询：先定主表，再定保留边界

- 只保留匹配记录：优先 `INNER JOIN`；
- 要保留左侧全部记录：优先 `LEFT JOIN`；
- 查询“没有出现过 / 没有参加 / 没有安排”：优先 `NOT EXISTS` 或 `NOT IN`；
- 若题干强调“无记录也显示为 0”：常见写法是 `LEFT JOIN + COUNT` 或 `UNION` 补 0。

#### 4）分组与筛选：`WHERE` 与 `HAVING` 分工不能乱

- `WHERE`：分组前筛选行；
- `GROUP BY`：按结果粒度分组；
- `HAVING`：分组后筛选组；
- 聚合函数条件如 `COUNT(*) >= 2`、`SUM(...) > 200` 应进入 `HAVING`。

#### 5）结果边界检查：SQL 题最后必须自查

写完 SQL 后至少检查 5 件事：

1. 输出字段是否和题干一致；
2. 连接条件是否漏了一条；
3. 是否错误丢掉了“没有匹配”的记录；
4. 分组字段是否与输出粒度一致；
5. 比较符号是否写准：`> / >=`、`< / <=` 不能混。

### 2.4 必备结论

- `NOT EXISTS` 的标准位置是 `WHERE NOT EXISTS (...)`，不是 `字段 NOT EXISTS (...)`。
- `LEFT JOIN ... IS NULL` 用于筛出左表中没有右表匹配记录的行。
- 统计题先确定“每一行代表谁”，再写 `GROUP BY`。
- `HAVING` 用于过滤分组后的聚合结果，不能替代普通行筛选。
- `CREATE VIEW 视图名 AS SELECT ...` 保存的是查询定义，不是复制数据。
- 触发器题要先写清触发时机：`AFTER INSERT / UPDATE / DELETE ON 表名`。
- `REFERENCING new row AS nrow` 用于引用新行，`old row AS orow` 用于引用旧行。
- `INSERT INTO ... SELECT ...` 中目标表字段列表要与 `SELECT` 字段顺序对应。
- “没有出现过”的题可以用 `NOT EXISTS`，也可以用 `NOT IN`，但 `NOT EXISTS` 更不容易受 `NULL` 干扰。
- SQL 下午题得分靠“结构完整 + 条件准确 + 边界不漏”，不是靠写得复杂。

### 2.5 高频考点拆解

#### 2.5.1 统计视图：`CREATE VIEW + GROUP BY`

**核心概念：** 视图本质是查询定义，统计视图通常把多表连接、分组统计封装起来。  
**常见考法：** 2017 销售记录详单视图、2018 不良记录视图、2019 俱乐部人数视图等都体现了“先统计，再封装”的思路。  
**高频误区：** 只写 `CREATE VIEW`，但 `SELECT` 里分组字段不完整；或者忘记把无记录项补为 0。  
**快速判断提示：** 先问“视图每一行代表谁”，再决定 `GROUP BY`。  
**小例子：** 每个社团人数视图：每个社团 1 行，所以应按社团号、社团名分组或用补 0 查询保留无成员社团。

#### 2.5.2 `HAVING` 子查询：找最大值 / 最多次数

**核心概念：** 当题目问“最多 / 最大 / 不少于某统计值”时，常见写法是 `GROUP BY + HAVING`，有时还要在 `HAVING` 中嵌套子查询。  
**常见考法：** 2016 查询调度次数最多的汽车，2017 查询当天销售最多的商品。  
**高频误区：** 把 `COUNT(*) = 最大值` 写进 `WHERE`；或者内外层分组字段不一致。  
**快速判断提示：** 一看到“最多”，先想：外层按对象分组，内层求各组计数，再用 `HAVING` 比较。  
**小例子：** `HAVING COUNT(*) >= ALL (SELECT COUNT(*) FROM ... GROUP BY ...)`。

#### 2.5.3 “没有出现过”查询：`NOT EXISTS` / `NOT IN`

**核心概念：** 本质是主表某对象在关联表中不存在匹配记录。  
**常见考法：** 查询没有安排过某品牌车辆的驾驶员、没有售出的商品、没有参加社团的学生。  
**高频误区：** 把 `NOT EXISTS` 放在字段后；或者 `NOT IN` 子查询字段选错。  
**快速判断提示：** 优先写成 `WHERE NOT EXISTS (SELECT 1 FROM 子表 WHERE 子表.外键 = 主表.主键 AND 条件)`。  
**小例子：** `WHERE NOT EXISTS (SELECT 1 FROM JoinClub J WHERE J.Sno = M.Sno)`。

#### 2.5.4 触发器骨架：触发条件 + 新旧行 + 维护动作

**核心概念：** 触发器用于在数据变化时自动执行维护动作，如更新汇总表、插入缺货记录、维护人数。  
**常见考法：** 2016 奖金维护触发器、2017 缺货单触发器、2018 不良记录触发器、2019 学院人数维护触发器。  
**高频误区：** 忘记触发表、触发时机或新旧行引用；只写业务描述，不写 SQL 骨架。  
**快速判断提示：** 先补 `CREATE TRIGGER ... AFTER ... ON ...`，再补 `REFERENCING new row / old row`，最后写 `UPDATE` 或 `INSERT`。  
**小例子：** 新增学生时 `UPDATE School SET stunum = stunum + 1 WHERE schno = nrow.schno`。

#### 2.5.5 `UNION` 补 0：保留无记录项

**核心概念：** 某些题要求“无记录也显示为 0”，除 `LEFT JOIN` 外，也可能通过 `UNION` 把没有匹配记录的对象补出来。  
**常见考法：** 2019 俱乐部人数视图中，需要对暂时没有学生参加的俱乐部人数显示为 0。  
**高频误区：** 只统计 JoinClub 中存在记录的社团，漏掉无成员社团。  
**快速判断提示：** 看到“没有……也要显示”，先检查自己有没有保留主表全量对象。  
**小例子：** 已有成员社团用统计查询，无成员社团用 `NOT IN / NOT EXISTS` 补 0，再 `UNION`。

### 2.6 下午题视角下的题型映射

| 下午题类型 | 常见问法 | 作答关键动作 | 常见丢分点 |
|---|---|---|---|
| 统计视图题 | “创建视图，按日期 / 对象统计数量” | 先定视图属性，再写 `SELECT`，再 `GROUP BY` | 视图名写错、分组字段不全、无记录项漏掉 |
| 最多 / 最大统计题 | “查询次数最多 / 销售最多的对象” | 外层分组统计，内层求最大统计值，`HAVING` 比较 | 把聚合条件写进 `WHERE`、内外层口径不一致 |
| 无记录查询题 | “查询没有安排 / 没有销售 / 没有参加的对象” | 优先 `NOT EXISTS`，或 `NOT IN` / `LEFT JOIN IS NULL` | `NOT EXISTS` 落位错误、子查询字段选错 |
| 触发器维护题 | “插入 / 删除后自动更新汇总字段” | 先写触发时机，再引用新旧行，再写 `UPDATE / INSERT` | 忘记 `new row / old row`、更新条件漏主键 |
| DDL 完整性题 | “补主码、外码、取值范围约束” | 先主键，再外键，再 `CHECK` / `NOT NULL` | 外键漏写、边界条件开闭区间写错 |
| 综合 SQL 纠错题 | “判断 SQL 哪里不稳定并改写” | 先找题型，再检查粒度、连接、筛选、分组 | 会说概念但改不出标准 SQL |

### 2.7 标准答题模板 / 采分点提示

#### 模板1：统计视图题
1. 先写：`CREATE VIEW 视图名(属性列表) AS`。
2. 再写 `SELECT`，字段必须与视图属性顺序对应。
3. 若有统计量，写 `COUNT / SUM / AVG` 并起别名。
4. 从主表和关联表出发写连接条件。
5. 按结果粒度写完整 `GROUP BY`。
6. 若题目要求无记录项也显示，补 `LEFT JOIN` 或 `UNION` 补 0。

#### 模板2：无记录查询题
1. 先确定主表：要输出谁，就从谁出发。
2. 再确定子表：哪张表记录了“是否出现过”。
3. 写标准骨架：`WHERE NOT EXISTS (SELECT 1 FROM 子表 WHERE 子表.外键 = 主表.主键 AND 附加条件)`。
4. 如果用 `NOT IN`，子查询只选一个字段，且注意 `NULL` 风险。
5. 最后检查输出字段是否只来自主表，避免多余连接。

#### 模板3：最多 / 最大统计题
1. 外层按目标对象分组，写出对象字段与统计量。
2. `GROUP BY` 必须与对象粒度一致。
3. `HAVING COUNT(*) >= ALL (...)` 或 `= (SELECT MAX(...))` 用于比较最大统计值。
4. 内层子查询也要按同一统计口径分组。
5. 最后检查是否存在并列最多情况，不能只默认一条。

#### 模板4：触发器维护题
1. 写触发器头：`CREATE TRIGGER 名称 AFTER INSERT/UPDATE/DELETE ON 表名`。
2. 写引用行：`REFERENCING new row AS nrow` 或 `old row AS orow`。
3. 写执行粒度：`FOR EACH ROW`。
4. 写维护动作：`UPDATE` 汇总表或 `INSERT INTO` 记录表。
5. 写准确条件：通常用 `WHERE 汇总表.键 = nrow.键 / orow.键`。

### 2.8 与前序知识的映射或衔接

Day 24～Day 27 可以这样串起来：

- **Day 24：** 范式与模式分解，解决表结构是否稳定；
- **Day 25：** SQL 专项 1，解决基础 DDL、连接、分组、无记录判断；
- **Day 26：** SQL 专项 2，在纠偏基础上推进视图、触发器、复杂统计与真题化 SQL；
- **Day 27：** 若 SQL 链稳定，再进入恢复 / 并发专项；若 SQL 仍漂，先做 SQL 二刷再切换主题。

也就是说，Day 26 是 SQL 模块从“会概念”到“能按真题空缺补全”的关键过渡日。

### 2.9 易混点速记卡片

- **`WHERE NOT EXISTS (...)`**：判断子查询不存在匹配行  
  **错误写法**：`字段 NOT EXISTS (...)`

- **`LEFT JOIN ... WHERE 右表主键 IS NULL`**：筛左表无匹配项  
  **`GROUP BY ... HAVING COUNT(...) = 0`**：统计后筛组，别乱混

- **`WHERE`**：分组前筛选行  
  **`HAVING`**：分组后筛选组

- **`CREATE VIEW`**：保存查询定义  
  **`CREATE TABLE`**：创建实际表结构

- **`INSERT INTO ... SELECT ...`**：批量插入查询结果  
  **`CREATE VIEW ... AS SELECT ...`**：封装查询，不插入数据

- **`new row`**：插入 / 更新后的新值  
  **`old row`**：删除 / 更新前的旧值

### 2.10 今天必须拿下的最小成果

- 能无误写出 `WHERE NOT EXISTS (...)` 标准骨架。
- 能无误写出 `LEFT JOIN ... WHERE 右表主键 IS NULL` 标准骨架。
- 能写出 1 个 `CREATE VIEW ... AS SELECT ... GROUP BY ...` 统计视图。
- 能写出 1 个 `GROUP BY + HAVING` 查询最大 / 最多对象的结构。
- 能看懂触发器题的触发时机、新旧行引用和维护动作。
- 能对 SQL 下午题先写中文粒度，再翻译成 SQL。

### 2.11 今日限时要求与产出要求

- **前置纠偏动作**：建议用 **25～30 分钟** 重写 Day 25 的 8 道正式错题。
- **A 轨上午基础巩固题（18 题）**：建议限时 **20～25 分钟**。
- **B 轨下午案例专项题（12 题）**：建议限时 **60～75 分钟**。
- **当日控制口径**：今天不是跳过 Day 25 问题直接扩新，而是“**先把 SQL 句型补稳，再接真题化综合 SQL**”。
- **保底完成顺序**：优先完成 **第19～24题**；若时间不足，第25～30题至少先写 SQL 骨架。
- **当日最低产出物**：完成 `Day 26.md` 的真实作答，并为后续 `Day 26 错题复盘.md` 提供可核查痕迹。
- **推进阈值提醒**：若 Day 26 下午题采分点命中率 ≥ 75%，Day 27 可按规划进入恢复 / 并发专项；若仍低于 70%，Day 27 先做 SQL 高频句型二刷。

---

## 3. 今日练习

> 题源说明：今日题目参考 `00_整体备考规划.md` 中 SQL 专项 2 的安排，并优先围绕 **2016 车辆调度 SQL、2017 销售统计视图、2018 租车不良记录视图、2019 俱乐部人数统计视图** 等已文本化真题主线做改编与整理；同时结合 2020～2023 SQL DDL / 插入 / 外连接 / 统计查询高频方向进行补弱。  
> 作答要求：  
> - 客观题请直接在题目括号 `（ ）` 内填写答案；  
> - 下午题请在“作答区”内作答；  
> - 后续正式错题复盘将严格以这些真实作答痕迹为准。

### A 轨｜上午基础巩固题（18 题）

#### 第1题

1. **题型：单选**  **来源：Day 25 错题纠偏 / SQL 句型整理**  
   `NOT EXISTS` 在 SQL 中最常见、最稳定的使用位置是（C ）  
   A. `SELECT NOT EXISTS 字段`  
   B. `FROM NOT EXISTS 表`  
   C. `WHERE NOT EXISTS (子查询)`  
   D. `GROUP BY NOT EXISTS 字段`

#### 第2题

2. **题型：单选**  **来源：2023 外连接主线改编**  
   查询“没有员工的部门”时，若使用 `LEFT JOIN`，最常见的判空条件应写在（A ）  
   A. `WHERE E.EmpNo IS NULL`  
   B. `HAVING E.EmpNo IS NULL`  
   C. `ORDER BY E.EmpNo IS NULL`  
   D. `CHECK E.EmpNo IS NULL`

#### 第3题

3. **题型：单选**  **来源：2017 / 2019 统计视图题型整理**  
   创建视图的基本语法骨架是（B ）  
   A. `CREATE TABLE 视图名 AS SELECT ...`  
   B. `CREATE VIEW 视图名 AS SELECT ...`  
   C. `INSERT VIEW 视图名 SELECT ...`  
   D. `GROUP VIEW 视图名 AS SELECT ...`

#### 第4题

4. **题型：单选**  **来源：SQL 分组统计高频题**  
   查询“每个部门的员工人数”，结果粒度是（B ）  
   A. 每个员工 1 行  
   B. 每个部门 1 行  
   C. 每个工资级别 1 行  
   D. 每个查询条件 1 行

#### 第5题

5. **题型：单选**  **来源：2016 / 2017 最多次数查询题型整理**  
   若题目要求“查询调度次数最多的车辆”，`COUNT(*)` 与最大次数比较的条件通常应放在（B ）  
   A. `WHERE`  
   B. `HAVING`  
   C. `FROM`  
   D. `CHECK`

#### 第6题

6. **题型：单选**  **来源：Day 25 错题纠偏 / INSERT SELECT**  
   `INSERT INTO HighSalary(EmpNo, Ename) SELECT EmpNo, Ename FROM Emp WHERE Salary > 8000` 的核心作用是（C ）  
   A. 建立视图  
   B. 删除低工资员工  
   C. 将查询结果批量插入目标表  
   D. 修改所有员工工资

#### 第7题

7. **题型：单选**  **来源：SQL 聚合边界题**  
   若题目要求“销售数量不少于 10 的商品”，其中 `COUNT(*) >= 10` 最适合写在（B ）  
   A. `WHERE`  
   B. `HAVING`  
   C. `VALUES`  
   D. `REFERENCES`

#### 第8题

8. **题型：单选**  **来源：2019 俱乐部人数视图主线改编**  
   若题目要求“没有成员的社团人数显示为 0”，下列哪种意识最重要（B ）  
   A. 只统计 `JoinClub` 中存在的社团即可  
   B. 必须保留 `Club` 中全部社团  
   C. 必须删除没有成员的社团  
   D. 只能使用 `INNER JOIN`

#### 第9题

9. **题型：单选**  **来源：触发器基础题**  
   触发器中 `new row AS nrow` 通常用于表示（B ）  
   A. 删除前的旧行  
   B. 插入或更新后的新行  
   C. 查询结果的第一行  
   D. 分组后的统计行

#### 第10题

10. **题型：单选**  **来源：触发器基础题**  
    若题目要求“删除学生后自动减少学院人数”，触发器中更可能需要引用的是（A ）  
    A. `new row`  
    B. `old row`  
    C. `GROUP BY`  
    D. `PRIMARY KEY`

#### 第11题

11. **题型：单选**  **来源：SQL 查询边界题**  
    关于 `WHERE` 与 `HAVING` 的区别，正确的是（C ）  
    A. `WHERE` 用于分组后筛选组  
    B. `HAVING` 用于分组前筛选行  
    C. `WHERE` 用于分组前筛选行，`HAVING` 用于分组后筛选组  
    D. 二者完全等价

#### 第12题

12. **题型：单选**  **来源：SQL 子查询易错点整理**  
    查询“没有参加任何社团的学生”时，最稳妥的思路是（B ）  
    A. 查询所有参加过社团的学生  
    B. 从学生表出发，判断不存在对应参加记录  
    C. 只查询社团表  
    D. 对所有表做笛卡尔积

#### 第13题

13. **题型：单选**  **来源：SQL 连接方式辨析题**  
    若题目只要求输出两张表中能匹配上的记录，通常使用（A ）  
    A. `INNER JOIN`  
    B. `LEFT JOIN`  
    C. `CHECK`  
    D. `TRIGGER`

#### 第14题

14. **题型：单选**  **来源：SQL 连接方式辨析题**  
    若题目要求“保留左表全部记录，即使右表无匹配也要保留”，通常使用（B ）  
    A. `INNER JOIN`  
    B. `LEFT JOIN`  
    C. `DELETE`  
    D. `HAVING`

#### 第15题

15. **题型：单选**  **来源：SQL 统计结果边界题**  
    查询“参加社团人数不少于 2 的系别”，最关键的分组字段应是（C ）  
    A. 学号  
    B. 姓名  
    C. 系别 / 部门号  
    D. 加入日期

#### 第16题

16. **题型：单选**  **来源：DDL 完整性纠偏题**  
    `FOREIGN KEY (Sno) REFERENCES Student(Sno)` 的主要作用是（B ）  
    A. 限制成绩在 0～100 之间  
    B. 保证 `Sno` 引用学生表中存在的学号  
    C. 对查询结果排序  
    D. 统计学生人数

#### 第17题

17. **题型：单选**  **来源：SQL 作答顺序题**  
    写 SQL 下午题前，最应优先确认的是（A ）  
    A. 结果每一行代表什么，以及需要输出哪些字段  
    B. 先把所有表随便连起来  
    C. 先写 `ORDER BY`  
    D. 先背一个复杂子查询

#### 第18题

18. **题型：单选**  **来源：Day 25 正确率闸门题**  
    若前一日 SQL 下午题采分点命中率约为 60%～74%，次日更稳妥的安排是（B ）  
    A. 完全跳过错题，直接进入新专题  
    B. 增加同类题 20～30 分钟，带纠偏推进  
    C. 只背概念，不再写 SQL  
    D. 停止下午题训练

### B 轨｜下午案例专项题（12 题）

#### 第19题（案例组 1 / 小问 1）

**题型归属：** Day 25 错题重写 / `NOT EXISTS` 查询  
**来源：Day 25 第28题纠偏 + 2019 SQL 无记录查询主线改编**

设有：
- `Member(Sno, Sname, DeptNo)`
- `JoinClub(Sno, ClubNo, JoinDate)`

请写出查询语句：输出**没有参加任何社团**的成员学号与姓名。

**作答区：**
- select M.Sno,M.Sname from Member M where not exists (select 1 from JoinClub J where M.Sno = J.Sno);
- 
- 

#### 第20题（案例组 1 / 小问 2）

**题型归属：** Day 25 错题重写 / `LEFT JOIN ... IS NULL`  
**来源：Day 25 第26题纠偏 + 2023 外连接主线改编**

设有：
- `Dept(Dno, Dname)`
- `Emp(EmpNo, Ename, Dno)`

请写出查询语句：输出**没有任何员工的部门号与部门名**。

**作答区：**
- select D.Dno,D.name from Dept D left jion Emp E on D.Dno=E.Dno where E.EmpNo is null;
- 
- 

#### 第21题（案例组 1 / 小问 3）

**题型归属：** Day 25 错题重写 / `INSERT ... SELECT`  
**来源：Day 25 第24题纠偏 + 2023 SQL 插入主线改编**

设有：
- `Emp(EmpNo, Ename, Salary)`
- `HighSalary(EmpNo, Ename)`

请写出插入语句：将 `Emp` 表中**工资大于 8000** 的员工号和姓名插入 `HighSalary` 表。

**作答区：**
- insert into HighSalary select EmpNo,Ename from Emp where Salary > 8000;
- 
- 

#### 第22题（案例组 1 / 小问 4）

**题型归属：** Day 25 错题重写 / 分组统计  
**来源：Day 25 第29题纠偏 + 2017 / 2019 SQL 统计主线改编**

设有：
- `Member(Sno, Sname, DeptNo)`
- `JoinClub(Sno, ClubNo, JoinDate)`

请写出查询语句：按系别统计**参加社团的学生人数（按学号去重）**，只输出**人数不少于 2** 的系别及人数。

**作答区：**
- select M.DeptNo,count(M.Sno) from Member M join JoinClub J on M.Sno=J.Sno group by J.Sno,J.ClubNo having count(distinct J.Sno) >= 2;
- 
- 

#### 第23题（案例组 2 / 小问 1）

**题型归属：** 统计视图 / `CREATE VIEW + GROUP BY`  
**来源：2017 销售记录详单视图主线改编**

设有：
- `VEM(VEMno, Location)`：售货机
- `GOODS(Gno, Brand, Price)`：商品
- `SALES(Sno, VEMno, Gno, SDate, STime)`：销售记录

请建立视图 `SALES_Total(VEMno, Gno, SDate, Amount)`，用于统计**每台售货机每天每种商品的销售数量**。

**作答区：**
- create view SALES_Total as  
- select S.VEMno,S.Gno,S.SDate,count(S.Sno) as  Amount from SALES S join GOODS G on S.Gno=G.Gno join Vem V on S.VEMno = V.VEMno  group by S.Gno,S.SDate;
- 

#### 第24题（案例组 2 / 小问 2）

**题型归属：** 统计视图 / 多表连接视图  
**来源：2017 销售记录详单视图主线改编**

继续上题。请建立视图 `SALES_Detail(VEMno, Location, Gno, Brand, Price, Amount, SDate)`，基于 `SALES_Total`、`VEM`、`GOODS` 输出售货机位置、商品品牌、价格、销售数量和销售日期。

**作答区：**
- create view SALES_Detail as
- select S.VEMno,V.Location,S.Gno,G.Brand,G.Price,S.Amount,S.Amount   from SALES_Total S join Vem V on  V.VEMno=S.VEMno join GOODS G on S.Gno = G.Gno group by VEMno,Gno;
- 

#### 第25题（案例组 2 / 小问 3）

**题型归属：** 最多统计 / `GROUP BY + HAVING`  
**来源：2017 销售最多商品查询主线改编**

设有：
- `GOODS(Gno, Brand, Price)`
- `SALES(Sno, VEMno, Gno, SDate, STime)`

请写出查询语句：输出**当天销售数量最多的商品编号、品牌和销售数量**。假设函数 `GetDate()` 返回当天日期。

**作答区：**
- select G.Gno,G.Brand,count(S.Sno) from GOODS G join G.Gno = S.Gno group by S.Gno having S.SDate=GetDate desc limit 1;
- 
- 

#### 第26题（案例组 2 / 小问 4）

**题型归属：** 无记录查询 / `NOT EXISTS` 或 `NOT IN`  
**来源：2017 一件都没有售出的商品查询主线改编**

设有：
- `GOODS(Gno, Brand)`
- `SALES(Sno, Gno, SDate)`

请写出查询语句：输出**一件都没有售出的所有商品编号和品牌**。

**作答区：**
- select G.Gno,G.Brand from GOODS where G.Gno not in (select S.Gno from SALES S);
- 
- 

#### 第27题（案例组 3 / 小问 1）

**题型归属：** DDL / 主键外键约束补全  
**来源：2016 车辆调度 SQL DDL 主线改编**

设有：
- `EMP(Eno, Ename)`：驾驶员
- `CAR(Cno, Brand)`：车辆
- `SCHEDULE(Sno, Eno, Cno, StartTime, EndTime)`：车辆调度记录

请写出 `SCHEDULE` 表的核心 `CREATE TABLE` 语句，要求：
- `Sno` 为主键；
- `Eno` 引用 `EMP(Eno)`；
- `Cno` 引用 `CAR(Cno)`；
- 发车时间 `StartTime` 的时刻应在 07:00:00 至 18:00:00 范围内（可用伪函数 `Get_time(StartTime)` 表示取时刻）。

**作答区：**
- `CREATE TABLE` SCHEDULE(
- Sno int,
- Eno int,
- Cno int,
- StartTime date check (Get_time(StartTime) between '07:00:00' and '18:00:00'),
- EndTime date,
- PRIMARY KEY (Sno),
- FOREIGN KEY (Eno) REFERENCES EMP(Eno),
- FOREIGN KEY (Cno) REFERENCES CAR(Cno)
- )
- 
- 

#### 第28题（案例组 3 / 小问 2）

**题型归属：** 视图 / 多表连接查询  
**来源：2016 奥迪品牌调度视图主线改编**

继续上题。请建立视图 `AudiSCHEDULE(Eno, Ename, Cno, Brand, StartTime, EndTime)`，输出所有 **奥迪品牌汽车** 的调度信息。

**作答区：**
- create view AudiSCHEDULE as 
- select E.Eno,E.Ename,C.Cno,C.Brand,S.StartTime,S.EndTime from EMP E join SCHEDULE S on E.Eno = S.Eno join CAR C on S.Cno=C.Cno group by E.Eno,C.Cno having C.Brand='奥迪';
- 

#### 第29题（案例组 3 / 小问 3）

**题型归属：** 最多统计 / `HAVING` 子查询  
**来源：2016 调度次数最多汽车查询主线改编**

继续上题。请写出查询语句：输出**调度次数最多的汽车车牌号和品牌**；若存在并列最多，应都输出。

**作答区：**
- select C.Cno,C.Brand from CAR C join SCHEDULE S on  C.Cno=S.Cno group by C.con having count(* ) >= all (select count(* ) from SCHEDULE S group by S.Sno,S.cno)  
- 

#### 第30题（案例组 3 / 小问 4）

**题型归属：** 触发器骨架 / 汇总字段维护  
**来源：2019 学院人数维护触发器主线改编**

设有：
- `School(SchNo, SchName, StuNum)`：学院表，`StuNum` 表示学生人数
- `Student(StuNo, StuName, SchNo)`：学生表

请补写一个触发器骨架：当 `Student` 表**插入或删除**学生记录后，自动维护对应学院的 `StuNum`。可使用伪语法表达，但必须体现：触发表、触发时机、新旧行引用、插入时加 1、删除时减 1。

**作答区：**
- CREATE TRIGGER STU_NUM_TRG
AFTER INSERT OR DELETE ON Student
REFERENCING new row AS nrow, old row AS orow
FOR EACH ROW
BEGIN
    IF INSERTING THEN
        UPDATE School
        SET StuNum = StuNum + 1
        WHERE SchNo = nrow.SchNo;
    END IF;

    IF DELETING THEN
        UPDATE School
        SET StuNum = StuNum - 1
        WHERE SchNo = orow.SchNo;
    END IF;
END;
- 
- 
- 

---

## 4. 答案解析（默认隐藏）

<details>
<summary>点击展开答案与解析</summary>

### 第1题
**答案：C**  
**解析：** `NOT EXISTS` 的稳定句型是 `WHERE NOT EXISTS (子查询)`。Day 25 第22、28题都暴露出把它误写成“字段 not exists”的问题。

### 第2题
**答案：A**  
**解析：** `LEFT JOIN` 后筛“没有匹配记录”，应在 `WHERE` 中判断右表关键字段为 `NULL`，如 `WHERE E.EmpNo IS NULL`。

### 第3题
**答案：B**  
**解析：** 视图使用 `CREATE VIEW 视图名 AS SELECT ...`。视图保存查询定义，不是实际新建物理表。

### 第4题
**答案：B**  
**解析：** “每个部门的员工人数”说明每个部门 1 行，因此分组字段应围绕部门号 / 部门名展开。

### 第5题
**答案：B**  
**解析：** `COUNT(*)` 是分组后的聚合结果，和最大次数比较应写在 `HAVING` 中。

### 第6题
**答案：C**  
**解析：** `INSERT INTO ... SELECT ...` 用于把查询结果批量插入目标表。

### 第7题
**答案：B**  
**解析：** `COUNT(*) >= 10` 是聚合条件，应放在 `HAVING` 中。

### 第8题
**答案：B**  
**解析：** 要让没有成员的社团人数显示为 0，必须保留 `Club` 中全部社团，不能只从 `JoinClub` 中统计已有记录。

### 第9题
**答案：B**  
**解析：** `new row` 表示插入或更新后的新行，常用于新增记录后更新汇总数据。

### 第10题
**答案：B**  
**解析：** 删除学生后需要知道被删学生原来属于哪个学院，因此需要引用 `old row`。

### 第11题
**答案：C**  
**解析：** `WHERE` 是分组前筛选行，`HAVING` 是分组后筛选组。

### 第12题
**答案：B**  
**解析：** “没有参加任何社团”应从成员 / 学生表出发，判断不存在对应参加记录。

### 第13题
**答案：A**  
**解析：** 只保留匹配记录，使用 `INNER JOIN`。

### 第14题
**答案：B**  
**解析：** 保留左表全部记录，使用 `LEFT JOIN`。

### 第15题
**答案：C**  
**解析：** “按系别统计”说明分组粒度是系别 / 部门号，而不是学号。

### 第16题
**答案：B**  
**解析：** 外键用于维护参照完整性，保证引用字段来自被引用表中的合法值。

### 第17题
**答案：A**  
**解析：** SQL 下午题最先确认输出粒度与输出字段，否则连接、筛选、分组都容易漂。

### 第18题
**答案：B**  
**解析：** 根据规划正确率闸门，下午采分点 60%～74% 时，次日应增加同类题 20～30 分钟，带纠偏推进。

### 第19题
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

**采分点：**
- 从 `Member` 出发；
- 使用 `WHERE NOT EXISTS (...)`；
- 内外层通过 `Sno` 正确关联。

**作答提醒：**
- 不能写成 `M.Sno NOT EXISTS (...)`；
- “没有参加任何社团”本质是“不存在 JoinClub 匹配记录”。

### 第20题
**参考答案：**
```sql
SELECT D.Dno, D.Dname
FROM Dept D
LEFT JOIN Emp E ON D.Dno = E.Dno
WHERE E.EmpNo IS NULL;
```

**采分点：**
- 从 `Dept` 出发做 `LEFT JOIN`；
- 用右表关键字段 `E.EmpNo IS NULL` 判断无匹配；
- 输出部门号与部门名。

**作答提醒：**
- 这题不是统计题，不需要 `GROUP BY / HAVING`；
- 关键是“保住部门表，再筛右表无匹配”。

### 第21题
**参考答案：**
```sql
INSERT INTO HighSalary (EmpNo, Ename)
SELECT EmpNo, Ename
FROM Emp
WHERE Salary > 8000;
```

**采分点：**
- 写出目标表和目标字段；
- `SELECT` 字段顺序与目标字段对应；
- 写出 `Salary > 8000` 筛选条件。

**作答提醒：**
- 不要写 `INSERT INTO HighSalary AS SELECT ...`；
- 先写来源查询，再套目标表外壳。

### 第22题
**参考答案：**
```sql
SELECT M.DeptNo, COUNT(DISTINCT M.Sno) AS StudentCount
FROM Member M
JOIN JoinClub J ON M.Sno = J.Sno
GROUP BY M.DeptNo
HAVING COUNT(DISTINCT M.Sno) >= 2;
```

**采分点：**
- 按 `DeptNo` 分组；
- 使用 `COUNT(DISTINCT M.Sno)` 去重计数；
- 使用 `HAVING ... >= 2`。

**作答提醒：**
- 结果粒度是“每个系别 1 行”；
- “不少于 2”是 `>= 2`，不是 `> 2`。

### 第23题
**参考答案：**
```sql
CREATE VIEW SALES_Total (VEMno, Gno, SDate, Amount) AS
SELECT VEMno, Gno, SDate, COUNT(*) AS Amount
FROM SALES
GROUP BY VEMno, Gno, SDate;
```

**采分点：**
- 使用 `CREATE VIEW`；
- 视图字段与 `SELECT` 字段对应；
- 按售货机、商品、日期三维分组统计。

**作答提醒：**
- 题干说“每台售货机每天每种商品”，所以 `GROUP BY` 必须包含 `VEMno, Gno, SDate`。

### 第24题
**参考答案：**
```sql
CREATE VIEW SALES_Detail (VEMno, Location, Gno, Brand, Price, Amount, SDate) AS
SELECT V.VEMno, V.Location, G.Gno, G.Brand, G.Price, T.Amount, T.SDate
FROM SALES_Total T
JOIN VEM V ON T.VEMno = V.VEMno
JOIN GOODS G ON T.Gno = G.Gno;
```

**采分点：**
- 使用 `CREATE VIEW`；
- 连接 `SALES_Total`、`VEM`、`GOODS`；
- 输出字段与视图属性对应。

**作答提醒：**
- 先确定视图输出字段，再倒推需要连接哪几张表。

### 第25题
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

**采分点：**
- 当天销售记录用 `WHERE SDate = GetDate()`；
- 外层按商品分组统计；
- `HAVING` 与内层最大 / 全部计数比较，支持并列最多。

**作答提醒：**
- “最多”题要允许并列；
- 聚合比较写在 `HAVING`，不是 `WHERE`。

### 第26题
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

**采分点：**
- 从 `GOODS` 出发；
- 判断不存在销售记录；
- 输出商品编号和品牌。

**作答提醒：**
- `NOT EXISTS` 更稳，尤其能避免 `NOT IN` 遇到 `NULL` 时的风险。

### 第27题
**参考答案：**
```sql
CREATE TABLE SCHEDULE (
    Sno CHAR(10) NOT NULL,
    Eno CHAR(10) NOT NULL,
    Cno CHAR(8) NOT NULL,
    StartTime DATETIME NOT NULL,
    EndTime DATETIME,
    PRIMARY KEY (Sno),
    FOREIGN KEY (Eno) REFERENCES EMP(Eno),
    FOREIGN KEY (Cno) REFERENCES CAR(Cno),
    CHECK (Get_time(StartTime) BETWEEN '07:00:00' AND '18:00:00')
);
```

**采分点：**
- `Sno` 主键；
- `Eno`、`Cno` 两个外键；
- `StartTime` 时间范围约束。

**作答提醒：**
- DDL 题先主键，再外键，再 `CHECK`；
- 时间边界要注意是否包含端点。

### 第28题
**参考答案：**
```sql
CREATE VIEW AudiSCHEDULE (Eno, Ename, Cno, Brand, StartTime, EndTime) AS
SELECT E.Eno, E.Ename, C.Cno, C.Brand, S.StartTime, S.EndTime
FROM EMP E
JOIN SCHEDULE S ON E.Eno = S.Eno
JOIN CAR C ON S.Cno = C.Cno
WHERE C.Brand = '奥迪';
```

**采分点：**
- 使用 `CREATE VIEW`；
- 三表连接关系正确；
- 用 `Brand = '奥迪'` 过滤。

**作答提醒：**
- 输出字段来自驾驶员、车辆、调度三张表；
- 不要漏连接条件。

### 第29题
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

**采分点：**
- 按汽车分组统计调度次数；
- 用 `HAVING` 比较最大调度次数；
- 支持并列最多。

**作答提醒：**
- “调度次数最多”不是简单排序第一条；
- 考试更稳写法是用 `HAVING` 处理并列最大。

### 第30题
**参考答案：**
```sql
CREATE TRIGGER STU_NUM_TRG
AFTER INSERT OR DELETE ON Student
REFERENCING new row AS nrow, old row AS orow
FOR EACH ROW
BEGIN
    IF INSERTING THEN
        UPDATE School
        SET StuNum = StuNum + 1
        WHERE SchNo = nrow.SchNo;
    END IF;

    IF DELETING THEN
        UPDATE School
        SET StuNum = StuNum - 1
        WHERE SchNo = orow.SchNo;
    END IF;
END;
```

**采分点：**
- 触发表为 `Student`；
- 触发时机为插入或删除后；
- 插入时引用 `new row` 加 1；
- 删除时引用 `old row` 减 1；
- 更新条件使用学院号对应。

**作答提醒：**
- 触发器题可以用伪语法，但必须体现触发时机、新旧行引用和维护动作。

</details>

---

## 5. 今日复盘

### 5.1 今天学了什么

- 如何把 Day 25 暴露出的 SQL 高频句型错误改成稳定模板
- `NOT EXISTS`、`LEFT JOIN ... IS NULL`、`INSERT ... SELECT` 的标准写法
- 统计视图题中如何从“视图属性”倒推 `SELECT` 与 `GROUP BY`
- “最多 / 最大”统计题为什么要用 `HAVING` 处理聚合比较
- 触发器题如何抓住触发表、触发时机、新旧行引用和维护动作

### 5.2 今天必须确认自己是否真的会了

- [ ] 我能无误写出 `WHERE NOT EXISTS (...)`
- [ ] 我能无误写出 `LEFT JOIN ... WHERE 右表主键 IS NULL`
- [ ] 我能无误写出 `INSERT INTO 目标表(字段) SELECT ...`
- [ ] 我能根据“每个谁 1 行”写出正确 `GROUP BY`
- [ ] 我能写出至少 1 个统计视图
- [ ] 我能看懂并补出触发器骨架

### 5.3 今天最容易混淆的点

- `NOT EXISTS` 的位置 vs `字段 NOT EXISTS` 错误写法
- `LEFT JOIN ... IS NULL` vs `GROUP BY ... HAVING COUNT = 0`
- `WHERE` vs `HAVING`
- `CREATE VIEW` vs `CREATE TABLE`
- `INSERT INTO ... SELECT ...` vs `CREATE VIEW ... AS SELECT ...`
- `new row` vs `old row`

### 5.4 哪些题值得二刷

- Day 25 错题：第19题、第20题、第21题、第22题、第24题、第26题、第28题、第29题
- Day 26 今日题：第19题、第20题、第21题、第22题、第23题、第25题、第26题、第27题、第28题、第29题、第30题

### 5.5 下午题今天暴露出的书写问题

- [ ] 是否仍把 `NOT EXISTS` 放在字段后面
- [ ] 是否仍把判空题误写成 `GROUP BY / HAVING`
- [ ] 是否仍忘记 `INSERT INTO` 的目标字段列表
- [ ] 是否仍先写 SQL，而不是先定输出粒度
- [ ] 是否仍在视图题中漏掉 `GROUP BY` 字段
- [ ] 是否仍看得懂触发器业务，但写不出触发器骨架

### 5.6 错题整理动作

- [ ] 已按真实作答在客观题括号内填写答案
- [ ] 已在下午题作答区留下可核查 SQL 或文字骨架
- [ ] 已标记自己最不稳的句型：`NOT EXISTS` / `LEFT JOIN IS NULL` / `GROUP BY HAVING` / `CREATE VIEW` / 触发器
- [ ] 学习完成后，基于真实作答结果生成：`02_错题复盘/Day 26 错题复盘.md`

### 5.7 明日衔接提醒

- 若 Day 26 下午题采分点命中率达到 **75% 以上**，Day 27 可按 `00_整体备考规划.md` 的阶段 B 节奏进入 **恢复与并发专项**。
- 若 Day 26 下午题采分点仍在 **60%～74%**，Day 27 开始前必须先二刷 Day 25～Day 26 的 SQL 高频句型，再推进新专题。
- 若 Day 26 下午题采分点低于 **60%**，Day 27 不建议直接切到恢复 / 并发，应先生成并完成一份 `SQL 高频写法模板.md` 或 SQL 纠偏专项。
- 无论明天是否切换主题，`NOT EXISTS`、`LEFT JOIN ... IS NULL`、`GROUP BY + HAVING` 这三条 SQL 链必须在后续真题日继续回刷。
