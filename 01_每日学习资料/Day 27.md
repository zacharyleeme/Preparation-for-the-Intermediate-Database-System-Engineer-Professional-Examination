---
title: Day 27
type: study-note
status: active
project: 软考中级数据库系统工程师备考
area: Career and Certification
exam: 数据库系统工程师
exam_level: 软考中级
stage: daily-study
category: daily-learning
day: 27
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

# Day 27｜2026-05-07｜距离考试 16 天｜SQL 高频句型二刷 + 恢复并发轻量预热：统计视图、最多统计、判空查询与事务异常入口

> 状态说明：**这是 Day 27 的待学习任务 / 学习资料**，用于继续推进数据库系统工程师备考内容，**不代表今日学习已经完成**。  
> 学习顺序：**先复盘 Day 26 的正式错题复盘结果，再进入 Day 27 新内容学习与练习。**  
> 题源规则：今日练习优先采用 **2016～2023 可靠真题池**中的真题主线、真题改编题与真题风格整理题；**2024～2025** 题源在 OCR 补证前不作为已验证题源使用。  
> 今日训练定位：Day 26 下午题采分点命中率为 **25 / 38 = 65.79%**，处于项目规则中的 **“60%～74%：次日增加同类题 20～30 分钟”** 区间，因此 Day 27 不直接完全切换到恢复 / 并发专项，而是先完成 SQL 高频句型二刷，再轻量预热事务并发与恢复入口。  
> 今日训练重心：优先纠偏 Day 26 暴露出的 **统计粒度、视图字段对齐、`WHERE` / `HAVING` 分工、最多统计子查询、无记录查询别名与字段名**；完成后再建立恢复 / 并发专项的基础判断框架。

---

## 1. 今日学习目标

1. 先复盘 Day 26 的正式错题，尤其重写第 **10 / 20 / 22 / 23 / 24 / 25 / 26 / 28 / 29** 题。
2. 把 SQL 下午题最容易漂的 5 条链路写成固定模板：**判空查询、统计粒度、统计视图、最多统计、普通筛选 vs 聚合筛选**。
3. 能在写 SQL 前先用中文判断“每一行代表谁”，再决定 `SELECT`、`GROUP BY` 与 `HAVING`。
4. 能区分普通连接视图、统计视图、最多统计查询三类 SQL 题，不再把非统计题误写成 `GROUP BY / HAVING`。
5. 对恢复 / 并发专项做轻量预热：理解事务 ACID、并发异常、两段锁协议、Undo / Redo 的入口概念。
6. 完成“上午基础巩固 + 下午案例专项”的双轨训练；上午题保概念，下午题练 SQL 二刷与恢复并发起步。
7. 为 Day 28 是否正式进入恢复 / 并发专项提供依据：若 Day 27 SQL 二刷仍低于 70%，继续 SQL 模板纠偏；若明显稳定，再切恢复 / 并发。

---

## 2. 学习内容

### 2.1 今日前置动作（先做，再学新内容）

> 根据项目正式规则，Day 2 及以后必须先复盘前一天错题，再进入当天新知识。

- [ ] 先打开并复习：`02_错题复盘/Day 26 错题复盘.md`
- [ ] 重点复盘 Day 26 的 9 道正式错题 / 不稳题：
  - **第10题**：删除触发器引用 `old row`，不是 `new row`
  - **第20题**：`LEFT JOIN ... WHERE 右表关键字段 IS NULL`，字段名和 `JOIN` 拼写要稳
  - **第22题**：按系别统计时 `GROUP BY DeptNo`，不是按学生 / 社团分组
  - **第23题**：统计视图必须按 `VEMno, Gno, SDate` 三维分组
  - **第24题**：明细视图补属性，不需要再 `GROUP BY`
  - **第25题**：最多统计要支持并列，用 `HAVING COUNT(*) >= ALL (...)`
  - **第26题**：无记录查询要保持主表别名一致
  - **第28题**：普通筛选用 `WHERE`，不是 `HAVING`
  - **第29题**：最多统计内外层分组口径必须一致
- [ ] 用 8 分钟默写以下 5 个 SQL 骨架：
  - `SELECT ... FROM A LEFT JOIN B ON ... WHERE B.主键 IS NULL;`
  - `SELECT 分组字段, COUNT(DISTINCT ...) FROM ... GROUP BY 分组字段 HAVING COUNT(DISTINCT ...) >= 条件;`
  - `CREATE VIEW 视图名(字段列表) AS SELECT 字段列表 FROM 主表 GROUP BY 分组字段;`
  - `SELECT ... FROM ... WHERE 条件 GROUP BY ... HAVING COUNT(*) >= ALL (SELECT COUNT(*) FROM ... GROUP BY ...);`
  - `SELECT ... FROM 主表 M WHERE NOT EXISTS (SELECT 1 FROM 子表 S WHERE S.外键 = M.主键);`
- [ ] 若上述 5 个骨架任意 2 个写不顺，今日 B 轨第19～26题必须优先完成，第27～30题只作为恢复 / 并发预热。
- [ ] 检查今日题源口径：SQL 题参考 2016～2019 文本化下午真题中的车辆调度、销售统计、俱乐部人数统计等主线，并结合 2020～2023 SQL 高频方向做改编；恢复 / 并发预热参考 2017 抢红包、2018 超市销售库存、2019 银行账务、2020～2023 日志恢复 / 并发题型主线。

> 注意：以上是 Day 27 学习前置动作清单，不代表这些动作已经完成。

### 2.2 为什么今天不直接完全切到恢复 / 并发

`00_整体备考规划.md` 中阶段 B 的安排，原本在 SQL 专项之后进入恢复与并发专项。但 Day 26 的真实复盘结果显示：

- 上午客观题 **17 / 18**，基础概念仍然较稳；
- 下午题采分点 **25 / 38**，命中率 **65.79%**；
- 主要问题集中在 SQL 下午题的书写链：
  - 统计粒度没有带动 `GROUP BY`；
  - 统计视图字段与分组维度不完整；
  - 普通筛选误写成 `HAVING`；
  - 最多统计题不会稳定写出支持并列的子查询；
  - 无记录查询方向对，但别名 / 字段名 / 语法壳子有小口子。

因此 Day 27 的定位是：

> **先把 SQL 高频模板二刷到能落笔，再给恢复 / 并发专项开一个轻量入口。**

今天不追求恢复 / 并发一次吃完，只要求先建立四个入口判断：

1. 并发异常有哪些；
2. 两段锁协议解决什么；
3. 检查点恢复时谁 Redo、谁 Undo；
4. 下午题怎么按步骤写出可得分短句。

### 2.3 今日核心知识框架

今天分成两条线：

> **主线 A：SQL 二刷纠偏**  
> **主线 B：恢复 / 并发轻量预热**

#### 1）SQL 二刷纠偏：先定题型，再定句型

SQL 下午题先问这 5 个问题：

1. 这题是普通查询、统计查询、视图、插入删除，还是触发器？
2. 输出结果每一行代表谁？
3. 是否需要保留无匹配记录？
4. 是否存在聚合函数？
5. 筛选条件应放在 `WHERE` 还是 `HAVING`？

判断后再落到固定模板：

| 题型 | 第一反应模板 |
|---|---|
| 没有匹配记录 | `NOT EXISTS` 或 `LEFT JOIN ... IS NULL` |
| 按某对象统计 | `GROUP BY 对象字段` |
| 统计后筛选 | `HAVING COUNT/SUM/...` |
| 普通条件筛选 | `WHERE 条件` |
| 最多 / 最大 | `HAVING COUNT(*) >= ALL (...)` |
| 创建统计视图 | `CREATE VIEW ... AS SELECT ... GROUP BY ...` |
| 创建明细视图 | `CREATE VIEW ... AS SELECT ... JOIN ... WHERE ...` |

#### 2）结果粒度：统计题的第一采分点

统计题最常见的丢分，是题目问“每个系别 / 每辆车 / 每台售货机每天每种商品”，但 `GROUP BY` 写成了学号、销售编号、调度号等明细粒度。

固定自查句：

> **如果题目问“每个 X”，`GROUP BY` 至少要包含 X；如果还输出 X 的名称，也要把名称字段稳定纳入分组或由函数依赖保证。**

#### 3）恢复 / 并发入口：先识别问题类型

事务与并发题不要一上来背大段定义，先按异常类型判断：

| 异常 / 问题 | 快速判断 |
|---|---|
| 丢失修改 | 两个事务改同一数据，后写覆盖前写 |
| 脏读 | 读到了别人尚未提交的数据 |
| 不可重复读 | 同一事务两次读同一数据，结果不同 |
| 幻读 | 同一范围查询前后多出 / 少了记录 |
| 死锁 | 事务互相等待对方释放锁 |
| 不可串行化 | 冲突依赖形成环，不能等价于某个串行顺序 |

#### 4）恢复入口：Undo / Redo 先看事务状态

日志恢复题先看故障发生时事务状态：

- 已提交但修改可能未写回磁盘：需要 `Redo`；
- 未提交但已经写过数据：需要 `Undo`；
- 检查点之前已经提交且数据已稳定写回：一般不需要处理；
- 检查点之后活跃或提交的事务，是恢复分析重点。

#### 5）今日作答顺序

今天下午题建议固定按这个顺序写：

1. 先写中文粒度 / 事务状态；
2. 再写核心模板；
3. 再补条件、字段、分组；
4. 最后检查是否漏别名、漏字段、误用 `HAVING`。

### 2.4 必备结论

- `WHERE` 用于分组前筛选行，`HAVING` 用于分组后筛选组。
- 没有聚合函数时，除非题目明确分组，否则不要手滑写 `GROUP BY / HAVING`。
- 统计题先定“每一行代表谁”，再写 `GROUP BY`。
- `LEFT JOIN ... IS NULL` 判空题应盯住右表关键字段是否为空。
- `NOT EXISTS` 的标准位置是 `WHERE NOT EXISTS (...)`。
- 统计视图必须让视图字段、`SELECT` 字段、`GROUP BY` 粒度互相对齐。
- “最多 / 最大”题不能简单 `ORDER BY ... LIMIT 1`，考试更稳写法是支持并列最大。
- 插入看 `new row`，删除看 `old row`，更新题可能两者都要看。
- 并发异常先判断“读错了、写没了、前后不一致、互相等待”是哪一种。
- 恢复题先找检查点和事务状态，再列 `Redo` / `Undo`。

### 2.5 高频考点拆解

#### 2.5.1 SQL 判空查询：`LEFT JOIN ... IS NULL`

**核心概念：** 保留左表全部记录，再筛出右表没有匹配的行。  
**常见考法：** 查询没有员工的部门、没有销售记录的商品、没有参加社团的学生。  
**高频误区：** 误写成 `GROUP BY ... HAVING 字段 IS NULL`；字段名或 `JOIN` 拼写出错。  
**快速判断提示：** 题干说“没有任何……”且要输出主表对象，先想 `LEFT JOIN` 或 `NOT EXISTS`。  
**小例子：** `FROM Dept D LEFT JOIN Emp E ON D.Dno = E.Dno WHERE E.EmpNo IS NULL`。

#### 2.5.2 统计视图：字段列表、输出字段、分组字段三对齐

**核心概念：** 视图保存查询定义，统计视图通过 `GROUP BY` 固化统计结果。  
**常见考法：** 销售记录详单视图、俱乐部人数视图、按对象统计数量。  
**高频误区：** 视图字段列表不写；`SELECT` 字段顺序对不上；`GROUP BY` 漏维度。  
**快速判断提示：** 先读视图属性列表，再逐个回填 `SELECT`。  
**小例子：** `SALES_Total(VEMno, Gno, SDate, Amount)` 必须按 `VEMno, Gno, SDate` 分组。

#### 2.5.3 最多统计：支持并列最大

**核心概念：** “最多 / 最大”本质是每组统计值与所有组统计值比较。  
**常见考法：** 查询调度次数最多的车辆、当天销售最多的商品。  
**高频误区：** 用 `ORDER BY COUNT(*) DESC LIMIT 1` 导致并列最多时漏结果；内外层分组口径不一致。  
**快速判断提示：** 看到“若并列也输出”，优先写 `HAVING COUNT(*) >= ALL (...)`。  
**小例子：** 外层按 `C.Cno, C.Brand` 分组，内层按 `Cno` 分组。

#### 2.5.4 并发异常：先识别现象，再说术语

**核心概念：** 并发控制题考的是多个事务同时访问同一数据时是否破坏一致性。  
**常见考法：** 抢红包、超市库存、银行转账与汇总等业务场景。  
**高频误区：** 只写“并发问题”，不指出是丢失修改、脏读、不可重复读还是死锁。  
**快速判断提示：** 看是否出现“读未提交”“覆盖别人的写”“同一数据前后读不同”“互相等待”。  
**小例子：** 两个事务都读取库存 10，分别扣减后都写回，后写覆盖前写，属于丢失修改。

#### 2.5.5 日志恢复：检查点 + 事务状态

**核心概念：** 故障恢复要根据日志判断哪些事务需要重做，哪些事务需要撤销。  
**常见考法：** 给出检查点、事务开始 / 写 / 提交日志，要求列 `Redo` / `Undo`。  
**高频误区：** 只看是否出现过写操作，不看是否已经提交；或者忽略检查点。  
**快速判断提示：** 已提交看 Redo，未提交看 Undo；检查点后活跃事务重点分析。  
**小例子：** 故障时 `T1` 已提交、`T2` 未提交，则通常 `T1` 进 Redo，`T2` 进 Undo。

### 2.6 下午题视角下的题型映射

| 下午题类型 | 常见问法 | 作答关键动作 | 常见丢分点 |
|---|---|---|---|
| SQL 判空查询 | “查询没有……的对象” | 从主表出发，写 `NOT EXISTS` 或 `LEFT JOIN ... IS NULL` | 主表别名漏写、右表字段判错 |
| SQL 统计视图 | “创建视图，统计每个……数量” | 先写视图字段，再按结果粒度 `GROUP BY` | 字段顺序错、分组字段漏 |
| SQL 最多统计 | “查询次数最多 / 销售最多” | 外层分组，内层同口径统计，`HAVING` 比较 | 用 `LIMIT 1` 漏并列、内外口径不一致 |
| SQL 普通视图 | “创建某类信息视图” | 多表连接 + `WHERE` 条件筛选 | 非统计题误写 `GROUP BY / HAVING` |
| 并发异常分析 | “判断调度结果 / 异常类型” | 列读写顺序，判断异常类型 | 只写结论，不写现象依据 |
| 日志恢复题 | “列 Redo / Undo 事务” | 找检查点、事务状态、提交情况 | 把未提交事务误 Redo |

### 2.7 标准答题模板 / 采分点提示

#### 模板1：SQL 判空查询

1. 先确定要输出谁：主表是谁。
2. 再确定哪张表记录了“是否存在”。
3. 若用 `LEFT JOIN`：
   ```sql
   SELECT 主表字段
   FROM 主表 A
   LEFT JOIN 子表 B ON A.主键 = B.外键
   WHERE B.关键字段 IS NULL;
   ```
4. 若用 `NOT EXISTS`：
   ```sql
   SELECT 主表字段
   FROM 主表 A
   WHERE NOT EXISTS (
       SELECT 1
       FROM 子表 B
       WHERE B.外键 = A.主键
   );
   ```
5. 最后检查：主表别名是否写了，子查询字段是否与主表关联。

#### 模板2：SQL 统计视图

1. 抄出视图名和字段列表。
2. 判断视图每一行代表什么。
3. `SELECT` 字段顺序必须与视图属性顺序一致。
4. 有统计量时写 `COUNT / SUM / AVG`。
5. `GROUP BY` 必须覆盖非聚合输出字段或结果粒度字段。

#### 模板3：最多 / 最大统计题

1. 外层按目标对象分组。
2. 外层写出对象字段与统计量。
3. 内层按同一目标对象统计每组数量。
4. 用 `HAVING COUNT(*) >= ALL (...)` 支持并列最大。
5. 若有日期 / 条件范围，外层和内层都要保持同一过滤口径。

#### 模板4：恢复 / 并发入口答题

1. 先判断题目类型：并发异常、锁协议、日志恢复。
2. 并发题先列冲突或异常现象，再写术语。
3. 恢复题先找检查点，再列故障时事务状态。
4. 已提交事务考虑 Redo，未提交事务考虑 Undo。
5. 最后用一句话说明原因，而不是只列名单。

### 2.8 与前序知识的映射或衔接

- Day 25：SQL 基础句型起步，暴露 `NOT EXISTS`、`LEFT JOIN`、`INSERT SELECT`、分组统计不稳。
- Day 26：SQL 综合推进，`NOT EXISTS` 和触发器开始变稳，但统计视图、最多统计、字段对齐仍不稳。
- Day 27：先二刷 SQL 高频模板，再轻量预热恢复 / 并发。
- Day 28：若 Day 27 SQL 二刷稳定，可正式进入恢复 / 并发专项；若 SQL 仍漂，先补 `SQL 高频写法模板.md` 或继续二刷。

### 2.9 易混点速记卡片

- **普通筛选：** `WHERE C.Brand = '奥迪'`  
  **聚合筛选：** `HAVING COUNT(*) >= 2`

- **统计视图：** 要 `GROUP BY`  
  **明细视图：** 多数只要 `JOIN + WHERE`

- **没有匹配：** `LEFT JOIN ... IS NULL` / `NOT EXISTS`  
  **已有匹配：** `INNER JOIN`

- **最多统计：** `HAVING COUNT(*) >= ALL (...)`  
  **普通排序：** `ORDER BY` 可能不能处理并列最大

- **插入触发器：** 看 `new row`  
  **删除触发器：** 看 `old row`

- **Redo：** 已提交但可能未落盘  
  **Undo：** 未提交但可能已写入

### 2.10 今天必须拿下的最小成果

- 能无误写出 `LEFT JOIN ... WHERE 右表关键字段 IS NULL`。
- 能无误写出 `WHERE NOT EXISTS (...)`。
- 能对统计题先写出“每一行代表谁”，再写正确 `GROUP BY`。
- 能写出 1 个统计视图和 1 个明细视图，并让字段列表对齐。
- 能写出 1 个支持并列最大结果的 `HAVING >= ALL` 查询。
- 能区分 `new row` 与 `old row`。
- 能说出丢失修改、脏读、不可重复读、死锁、Redo、Undo 的最小判断句。

### 2.11 今日限时要求与产出要求

- **前置纠偏动作**：建议用 **25～30 分钟** 重写 Day 26 的 SQL 错题。
- **A 轨上午基础巩固题（18 题）**：建议限时 **20～25 分钟**。
- **B 轨下午案例专项题（12 题）**：建议限时 **70～80 分钟**，其中 SQL 二刷优先，恢复 / 并发预热其次。
- **当日控制口径**：今天不是完全切新专题，而是“**SQL 二刷 + 恢复并发入口**”。
- **保底完成顺序**：优先完成 **第19～26题**；若时间不足，第27～30题至少完成概念判断与作答骨架。
- **当日最低产出物**：完成 `Day 27.md` 的真实作答，并为后续 `Day 27 错题复盘.md` 提供可核查痕迹。
- **推进阈值提醒**：若 Day 27 下午 SQL 采分点仍低于 70%，Day 28 继续 SQL 二刷；若 SQL 部分明显稳定，再正式进入恢复 / 并发专项。

---

## 3. 今日练习

> 题源说明：今日题目参考 `00_整体备考规划.md` 与 Day 26 错题复盘结论，优先围绕 **2016 车辆调度 SQL、2017 销售统计视图、2019 俱乐部人数统计视图、2020～2023 SQL 与恢复 / 并发高频方向** 做真题化改编与整理。  
> 作答要求：  
> - 客观题请直接在题目括号 `（ ）` 内填写答案；  
> - 下午题请在“作答区”内作答；  
> - 后续正式错题复盘将严格以这些真实作答痕迹为准。

### A 轨｜上午基础巩固题（18 题）

#### 第1题

1. **题型：单选**  **来源：Day 26 错题纠偏 / 触发器基础题**  
   删除一条学生记录后，如果触发器需要知道该学生原来属于哪个学院，应优先引用（B ）  
   A. `new row`  
   B. `old row`  
   C. `GROUP BY`  
   D. `CREATE VIEW`

#### 第2题

2. **题型：单选**  **来源：Day 26 错题纠偏 / `LEFT JOIN` 判空题**  
   查询“没有任何员工的部门”时，最稳妥的 SQL 骨架是（A ）  
   A. `Dept LEFT JOIN Emp ... WHERE Emp.EmpNo IS NULL`  
   B. `Dept JOIN Emp ... WHERE Emp.EmpNo IS NOT NULL`  
   C. `Dept GROUP BY Emp.EmpNo`  
   D. `Emp HAVING Dept.Dno IS NULL`

#### 第3题

3. **题型：单选**  **来源：Day 26 错题纠偏 / 统计粒度题**  
   “按系别统计参加社团的学生人数”时，最关键的 `GROUP BY` 字段应是（C ）  
   A. 学号  
   B. 社团号  
   C. 系别 / 部门号  
   D. 加入日期

#### 第4题

4. **题型：单选**  **来源：2017 销售统计视图主线改编**  
   `SALES_Total(VEMno, Gno, SDate, Amount)` 表示每台售货机每天每种商品的销售数量，则最核心的分组字段是（B ）  
   A. `Sno`  
   B. `VEMno, Gno, SDate`  
   C. `Location, Brand`  
   D. `Price`

#### 第5题

5. **题型：单选**  **来源：Day 26 错题纠偏 / 明细视图题**  
   已有统计视图 `SALES_Total` 后，再建立 `SALES_Detail` 补充售货机位置、商品品牌和价格，通常最关键的是（B ）  
   A. 继续对所有字段 `GROUP BY`  
   B. 连接 `SALES_Total`、`VEM`、`GOODS` 并对齐输出字段  
   C. 删除 `SALES_Total`  
   D. 只查询 `GOODS` 表

#### 第6题

6. **题型：单选**  **来源：2016 / 2017 最多统计题型整理**  
   若题目要求“调度次数最多的汽车，若并列最多也全部输出”，最稳妥的写法是（B ）  
   A. `ORDER BY COUNT(*) DESC LIMIT 1`  
   B. `HAVING COUNT(*) >= ALL (SELECT COUNT(*) ... GROUP BY ...)`  
   C. `WHERE COUNT(*) = MAX(*)`  
   D. `CHECK COUNT(*)`

#### 第7题

7. **题型：单选**  **来源：SQL 筛选位置辨析题**  
   查询“奥迪品牌车辆调度信息”时，`C.Brand = '奥迪'` 这类普通行筛选通常应写在（A ）  
   A. `WHERE`  
   B. `HAVING`  
   C. `GROUP BY`  
   D. `REFERENCES`

#### 第8题

8. **题型：单选**  **来源：SQL 聚合筛选辨析题**  
   查询“销售数量不少于 10 的商品”时，`COUNT(*) >= 10` 应写在（B ）  
   A. `WHERE`  
   B. `HAVING`  
   C. `FROM`  
   D. `PRIMARY KEY`

#### 第9题

9. **题型：单选**  **来源：无记录查询题型整理**  
   查询“一件都没有售出的商品”时，最稳妥的出发主表是（B ）  
   A. `SALES`  
   B. `GOODS`  
   C. `VEM`  
   D. `SCHEDULE`

#### 第10题

10. **题型：单选**  **来源：事务并发基础题**  
    两个事务都读取同一数据项的旧值，分别修改后写回，后写回的结果覆盖了前一个事务的修改，这类异常通常称为（A ）  
    A. 丢失修改  
    B. 脏读  
    C. 幻读  
    D. 介质故障

#### 第11题

11. **题型：单选**  **来源：事务并发基础题**  
    一个事务读取了另一个事务尚未提交的数据，后来该事务回滚，前者读到的数据就是（A ）  
    A. 脏读  
    B. 死锁  
    C. 无损连接  
    D. 视图更新

#### 第12题

12. **题型：单选**  **来源：事务隔离基础题**  
    同一事务中两次读取同一数据项，但两次读到的值不同，通常对应的并发问题是（A ）  
    A. 不可重复读  
    B. 函数依赖  
    C. 参照完整性  
    D. 级联删除

#### 第13题

13. **题型：单选**  **来源：两段锁协议基础题**  
    两段锁协议中，事务在释放某个锁之后，通常不允许再（A ）  
    A. 申请新的锁  
    B. 提交事务  
    C. 读取日志  
    D. 执行查询

#### 第14题

14. **题型：单选**  **来源：日志恢复基础题**  
    系统故障时，某事务已经提交，但其修改可能尚未全部写回数据库，恢复时通常需要（A ）  
    A. `Redo`  
    B. `Undo`  
    C. 删除日志  
    D. 删除检查点

#### 第15题

15. **题型：单选**  **来源：日志恢复基础题**  
    系统故障时，某事务尚未提交，但已经对数据库执行过写操作，恢复时通常需要（B ）  
    A. `Redo`  
    B. `Undo`  
    C. 创建视图  
    D. 建立索引

#### 第16题

16. **题型：单选**  **来源：恢复题作答顺序整理**  
    日志恢复题最先应关注的是（A ）  
    A. 检查点位置、事务开始 / 提交状态和故障时刻  
    B. 表名是否好听  
    C. 是否使用 `GROUP BY`  
    D. 是否存在视图字段列表

#### 第17题

17. **题型：单选**  **来源：Day 26 正确率闸门题**  
    若前一日 SQL 下午题采分点命中率为 65.79%，次日更合理的安排是（B ）  
    A. 完全跳过 SQL，直接做全新专题  
    B. 先增加 SQL 同类题二刷，再轻量进入新专题  
    C. 不再做下午题  
    D. 只背概念，不写题

#### 第18题

18. **题型：单选**  **来源：SQL 作答顺序题**  
    写统计 SQL 前，最应该先确认的是（A ）  
    A. 每一行结果代表谁  
    B. 先写最复杂的子查询  
    C. 先写 `ORDER BY`  
    D. 先随便选一个字段分组

### B 轨｜下午案例专项题（12 题）

#### 第19题（案例组 1 / 小问 1）

**题型归属：** SQL 判空查询 / `LEFT JOIN ... IS NULL`  
**来源：Day 26 第20题纠偏 + 2023 外连接主线改编**

设有：
- `Dept(Dno, Dname)`
- `Emp(EmpNo, Ename, Dno)`

请写出查询语句：输出**没有任何员工的部门号与部门名**。

**作答区：**
- select D.Dno,D.Dname from Dept D left join Emp E on D.Dno=E.Dno where Ename is null;
- 
- 

#### 第20题（案例组 1 / 小问 2）

**题型归属：** SQL 无记录查询 / `NOT EXISTS`  
**来源：Day 26 第26题纠偏 + 2017 一件都没有售出的商品查询主线改编**

设有：
- `GOODS(Gno, Brand)`
- `SALES(Sno, Gno, SDate)`

请使用 `NOT EXISTS` 写出查询语句：输出**一件都没有售出的所有商品编号和品牌**。

**作答区：**
- select Gno,Brand from GOODS where not EXISTS (select * from SALES);
- 
- 

#### 第21题（案例组 1 / 小问 3）

**题型归属：** SQL 分组统计 / `GROUP BY + HAVING`  
**来源：Day 26 第22题纠偏 + 2019 俱乐部人数统计主线改编**

设有：
- `Member(Sno, Sname, DeptNo)`
- `JoinClub(Sno, ClubNo, JoinDate)`

请写出查询语句：按系别统计**参加社团的学生人数（按学号去重）**，只输出**人数不少于 2** 的系别及人数。

**作答区：**
- select M.DeptNo,count(distinct M.Sno) from Member M left join JoinClub J on M.Sno=J.Sno group by M.DeptNo having count(distinct M.Sno) >=2;
- 
- 

#### 第22题（案例组 1 / 小问 4）

**题型归属：** SQL 插入查询 / `INSERT ... SELECT`  
**来源：Day 25～Day 26 高频句型二刷 / 2023 SQL 插入主线改编**

设有：
- `Emp(EmpNo, Ename, Salary)`
- `HighSalary(EmpNo, Ename)`

请写出插入语句：将 `Emp` 表中**工资大于 8000** 的员工号和姓名插入 `HighSalary` 表，要求显式写出目标字段列表。

**作答区：**
- insert into HighSalary (EmpNo, Ename, Salary) select EmpNo, Ename, Salary from Emp where Salary > 8000;
- 
- 

#### 第23题（案例组 2 / 小问 1）

**题型归属：** 统计视图 / `CREATE VIEW + GROUP BY`  
**来源：2017 销售记录详单视图主线改编 + Day 26 第23题纠偏**

设有：
- `SALES(Sno, VEMno, Gno, SDate, STime)`：销售记录

请建立视图 `SALES_Total(VEMno, Gno, SDate, Amount)`，用于统计**每台售货机每天每种商品的销售数量**。

**作答区：**
- create view SALES_Total(VEMno, Gno, SDate, Amount) as select VEMno, Gno, SDate,count(Sno) as Amount from SALES where SDate=getdate() group by Gno;
- 
- 

#### 第24题（案例组 2 / 小问 2）

**题型归属：** 明细视图 / 多表连接  
**来源：2017 销售记录详单视图主线改编 + Day 26 第24题纠偏**

继续上题。设有：
- `SALES_Total(VEMno, Gno, SDate, Amount)`
- `VEM(VEMno, Location)`
- `GOODS(Gno, Brand, Price)`

请建立视图 `SALES_Detail(VEMno, Location, Gno, Brand, Price, Amount, SDate)`，输出售货机位置、商品品牌、价格、销售数量和销售日期。

**作答区：**
- create view SALES_Detail(VEMno, Location, Gno, Brand, Price, Amount, SDate) as
- select V.VEMno,V.Location,G.Gno,G.Brand,G.Price,S.Amount,S.SDate from VEM V join SALES_Total S on V.VEMno=S.VEMno join GOODS G on G.Gno=S.Gno ;
- 
- 

#### 第25题（案例组 2 / 小问 3）

**题型归属：** 最多统计 / `HAVING >= ALL`  
**来源：2017 销售最多商品查询主线改编 + Day 26 第25题纠偏**

设有：
- `GOODS(Gno, Brand, Price)`
- `SALES(Sno, VEMno, Gno, SDate, STime)`

请写出查询语句：输出**当天销售数量最多的商品编号、品牌和销售数量**。假设函数 `GetDate()` 返回当天日期；若存在并列最多，应全部输出。

**作答区：**
- select G.Gno,G.Brand,count(S.Sno) from GOODS G join SALES S on G.Gno=S.Gno where S.SDate=GetDate() group by G.Gno,G.Brand having count(S.Sno) >= all (select count(Sno) from SALES group by Gno);
- 
- 

#### 第26题（案例组 2 / 小问 4）

**题型归属：** 普通视图 / 多表连接 + `WHERE` 筛选  
**来源：2016 车辆调度 SQL 主线改编 + Day 26 第28题纠偏**

设有：
- `EMP(Eno, Ename)`：驾驶员
- `CAR(Cno, Brand)`：车辆
- `SCHEDULE(Sno, Eno, Cno, StartTime, EndTime)`：调度记录

请建立视图 `AudiSCHEDULE(Eno, Ename, Cno, Brand, StartTime, EndTime)`，输出所有 **奥迪品牌汽车** 的调度信息。

**作答区：**
- create view AudiSCHEDULE(Eno, Ename, Cno, Brand, StartTime, EndTime) as select E.Eno,E.Ename,C.Cno,C.Brand,S.StartTime,S.EndTime from EMP E join SCHEDULE S on E.Eno = S.Eno join CAR C on S.Cno = C.Cno where Brand='奥迪';
- 
- 

#### 第27题（案例组 3 / 小问 1）

**题型归属：** 最多统计 / 调度次数最多  
**来源：2016 调度次数最多汽车查询主线改编 + Day 26 第29题纠偏**

继续上题。请写出查询语句：输出**调度次数最多的汽车车牌号和品牌**；若存在并列最多，应都输出。

**作答区：**
- select C.Cno,C.Brand from CAR C join SCHEDULE S on C.Cno = S.Cno group by C.Cno, C.Brand having count(S.Sno) >= all (select count(Sno) from SCHEDULE group by Cno);
- 
- 

#### 第28题（案例组 3 / 小问 2）

**题型归属：** 事务并发 / 异常类型判断  
**来源：2018 超市销售库存并发主线改编**

设商品库存数据项 `X` 初值为 10。两个事务并发执行：

- `T1`：读 `X=10`，准备销售 2 件，计算新值 `X=8`；
- `T2`：读 `X=10`，准备销售 3 件，计算新值 `X=7`；
- `T1` 写回 `X=8`；
- `T2` 写回 `X=7`。

请回答：最终数据库中 `X` 的值是多少？该调度主要暴露出哪类并发异常？为什么？

**作答区：**
- 最终X 的值为7
- 该调度暴露的并发异常为丢失修改，因为事务T1修改的X=8 被事务T2修改的 X=7覆盖了

#### 第29题（案例组 3 / 小问 3）

**题型归属：** 两段锁协议 / 调度改写入口  
**来源：2017 抢红包并发主线改编 + 2018 库存并发主线改编**

仍以上题库存 `X` 为例。请用文字说明：若要避免两个事务对 `X` 的并发修改互相覆盖，应该在读写 `X` 前后如何使用排他锁 `Xlock(X)` 与解锁 `Unlock(X)`？要求体现“两段锁协议”的基本思想。

**作答区：**
- 在事务T1 读取到X=10时，就需要创建排它锁，此时事务T2只允许查询X值，不能修改X值
- 在事务T1写入 X=8，并提交了修改结果后，就可以进行解锁。允许T2 查询并修改X值。
- 

#### 第30题（案例组 3 / 小问 4）

**题型归属：** 日志恢复 / Redo 与 Undo 判断  
**来源：2020～2023 日志恢复题型主线改编**

某系统在检查点后有如下事务状态：

- `T1`：检查点后开始，已经写过数据，并在故障前提交；
- `T2`：检查点后开始，已经写过数据，但故障前尚未提交；
- `T3`：检查点前已经提交，且不在检查点后的活跃事务列表中。

系统发生故障后，请判断 `T1`、`T2`、`T3` 分别应进入 `Redo`、`Undo`，还是通常不需要处理，并说明理由。

**作答区：**
- T1 在检查点后写入数据，并在故障前完成了提交，那么故障后，应该对事务T1做redo 操作，确保T1事务正确落盘。
- T2事务在检查点后写入数据，并在故障前未提交，所以故障后需要对事务T2做undo 操作，避免T2事务落盘
- T3 事务在检查点前已经提交，并且不在检查点后的活跃事务列表中，所以不需要做处理。

---

## 4. 答案解析（默认隐藏）

<details>
<summary>点击展开答案与解析</summary>

### 第1题
**答案：B**  
**解析：** 删除后需要知道被删行原来的值，因此引用 `old row`。插入后才主要引用 `new row`。

### 第2题
**答案：A**  
**解析：** 查询没有员工的部门，要从 `Dept` 出发保留全部部门，再判断右表 `Emp` 无匹配，即 `Emp.EmpNo IS NULL`。

### 第3题
**答案：C**  
**解析：** 题目要求“按系别统计”，结果粒度是每个系别 1 行，因此核心分组字段是系别 / 部门号。

### 第4题
**答案：B**  
**解析：** 每台售货机、每天、每种商品三个维度共同决定一行统计结果，因此 `GROUP BY` 应包含 `VEMno, Gno, SDate`。

### 第5题
**答案：B**  
**解析：** `SALES_Detail` 是基于统计视图补充售货机位置和商品属性，关键是连接三张表并对齐输出字段，不是重新分组。

### 第6题
**答案：B**  
**解析：** `HAVING COUNT(*) >= ALL (...)` 可以支持并列最大；`LIMIT 1` 可能漏掉并列最多的对象。

### 第7题
**答案：A**  
**解析：** `C.Brand = '奥迪'` 是普通行筛选条件，应放在 `WHERE`。

### 第8题
**答案：B**  
**解析：** `COUNT(*) >= 10` 是聚合后筛选组，应放在 `HAVING`。

### 第9题
**答案：B**  
**解析：** 查询没有售出的商品，输出对象是商品，因此应从 `GOODS` 出发。

### 第10题
**答案：A**  
**解析：** 后写回覆盖前写回，导致前一个事务的修改丢失，属于丢失修改。

### 第11题
**答案：A**  
**解析：** 读取另一个事务未提交的数据，若对方回滚，就形成脏读。

### 第12题
**答案：A**  
**解析：** 同一事务两次读取同一数据项结果不同，属于不可重复读。

### 第13题
**答案：A**  
**解析：** 两段锁协议要求事务分为加锁阶段和解锁阶段，一旦开始释放锁，不能再申请新锁。

### 第14题
**答案：A**  
**解析：** 已提交事务的修改需要保证最终反映到数据库中，因此可能需要 `Redo`。

### 第15题
**答案：B**  
**解析：** 未提交事务的修改不能保留，需要撤销，因此需要 `Undo`。

### 第16题
**答案：A**  
**解析：** 日志恢复题先看检查点、事务开始 / 提交状态和故障时刻，再判断 Redo / Undo。

### 第17题
**答案：B**  
**解析：** 下午题采分点 60%～74% 时，次日应增加同类题 20～30 分钟，带纠偏推进。

### 第18题
**答案：A**  
**解析：** 统计 SQL 最先确认结果粒度，也就是每一行代表谁。

### 第19题
**参考答案：**
```sql
SELECT D.Dno, D.Dname
FROM Dept D
LEFT JOIN Emp E ON D.Dno = E.Dno
WHERE E.EmpNo IS NULL;
```

**采分点：**
- 从 `Dept` 出发；
- 使用 `LEFT JOIN` 保留全部部门；
- 用 `E.EmpNo IS NULL` 判断无员工；
- 输出 `Dno, Dname`。

**作答提醒：** 不要写成 `HAVING`；注意 `JOIN` 拼写和 `Dname` 字段名。

### 第20题
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

**采分点：**
- 从 `GOODS` 出发；
- 使用 `WHERE NOT EXISTS (...)`；
- 内外层通过 `Gno` 正确关联。

**作答提醒：** 主表起了别名 `G`，后续字段就要一致使用 `G.Gno`、`G.Brand`。

### 第21题
**参考答案：**
```sql
SELECT M.DeptNo, COUNT(DISTINCT M.Sno) AS StudentCount
FROM Member M
JOIN JoinClub J ON M.Sno = J.Sno
GROUP BY M.DeptNo
HAVING COUNT(DISTINCT M.Sno) >= 2;
```

**采分点：**
- 结果粒度是“每个系别 1 行”；
- 按 `M.DeptNo` 分组；
- 使用 `COUNT(DISTINCT M.Sno)` 去重计数；
- `HAVING ... >= 2`。

**作答提醒：** “按学号去重”是计数规则，不是分组规则。

### 第22题
**参考答案：**
```sql
INSERT INTO HighSalary (EmpNo, Ename)
SELECT EmpNo, Ename
FROM Emp
WHERE Salary > 8000;
```

**采分点：**
- 写出目标表字段列表；
- `SELECT` 字段顺序对应目标字段；
- 写出工资筛选条件。

**作答提醒：** 不要写 `AS SELECT`，不要省略目标字段列表。

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
- 按 `VEMno, Gno, SDate` 三维统计。

**作答提醒：** 本题只需 `SALES` 即可，不要多连表导致粒度复杂化。

### 第24题
**参考答案：**
```sql
CREATE VIEW SALES_Detail (VEMno, Location, Gno, Brand, Price, Amount, SDate) AS
SELECT T.VEMno, V.Location, T.Gno, G.Brand, G.Price, T.Amount, T.SDate
FROM SALES_Total T
JOIN VEM V ON T.VEMno = V.VEMno
JOIN GOODS G ON T.Gno = G.Gno;
```

**采分点：**
- 使用 `CREATE VIEW`；
- 正确连接三张表；
- 输出字段与视图属性顺序一致；
- 不额外 `GROUP BY`。

**作答提醒：** 这是明细视图，不是重新统计视图。

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
- 外层筛当天销售；
- 按商品分组统计；
- 内层同样只统计当天；
- `HAVING >= ALL` 支持并列最多。

**作答提醒：** 外层和内层的日期过滤口径必须一致。

### 第26题
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
- 三表连接正确；
- 普通品牌筛选写在 `WHERE`；
- 输出字段顺序对应视图属性。

**作答提醒：** 这题没有统计量，不要写 `GROUP BY / HAVING`。

### 第27题
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
- 按汽车分组；
- 外层输出车牌号与品牌；
- 内层按同一汽车口径统计；
- 支持并列最多。

**作答提醒：** 内层不要按 `Sno` 分组；`Sno` 是调度记录号，不是车辆粒度。

### 第28题
**参考答案：**
- 最终 `X` 的值为 **7**；
- 该调度暴露出 **丢失修改**；
- 原因是 `T1` 和 `T2` 都基于旧值 `10` 计算，`T2` 后写回 `7` 覆盖了 `T1` 写回的 `8`，导致 `T1` 的修改效果丢失。

**采分点：**
- 写出最终值；
- 写出异常类型；
- 说明覆盖关系。

**作答提醒：** 不要只写“并发错误”，要指出具体异常类型。

### 第29题
**参考答案：**
一种可接受的文字表达：

1. `T1` 在读取 / 修改 `X` 前申请排他锁：`Xlock(X)`；
2. `T1` 完成读、计算、写回并提交后，再 `Unlock(X)`；
3. `T2` 必须等待 `T1` 释放 `X` 上的排他锁后，才能申请 `Xlock(X)`；
4. `T2` 再读取最新的 `X` 值、计算、写回、提交并解锁；
5. 整个过程中，事务在释放锁之前完成所有需要的加锁动作，符合两段锁协议思想。

**采分点：**
- 使用排他锁保护写操作；
- 后一事务等待前一事务释放锁；
- 体现加锁阶段与解锁阶段；
- 能说明避免覆盖修改。

**作答提醒：** 锁协议题不一定要写完整 SQL，但必须体现锁的申请、等待、释放与互斥。

### 第30题
**参考答案：**
- `T1`：进入 **Redo**。因为它在故障前已经提交，恢复时要保证其修改最终反映到数据库中。
- `T2`：进入 **Undo**。因为它在故障前尚未提交，已经执行过的修改不能保留，需要撤销。
- `T3`：通常**不需要处理**。因为它在检查点前已经提交，且不在检查点后的活跃事务列表中，通常可视为已稳定处理。

**采分点：**
- 正确区分已提交与未提交；
- 正确列 Redo / Undo；
- 说明检查点前已提交事务通常不再处理。

**作答提醒：** 恢复题不要只背 `Redo/Undo`，要结合检查点和事务状态判断。

</details>

---

## 5. 今日复盘

### 5.1 今天学了什么

- Day 26 的 SQL 高频错点如何二刷纠偏
- `LEFT JOIN ... IS NULL` 与 `NOT EXISTS` 的判空查询模板
- 统计视图中视图字段、`SELECT` 字段、`GROUP BY` 字段如何对齐
- 最多统计题如何用 `HAVING >= ALL` 支持并列最大
- 普通筛选应放 `WHERE`，聚合筛选才放 `HAVING`
- 恢复 / 并发题的入口概念：丢失修改、脏读、不可重复读、两段锁、Redo、Undo

### 5.2 今天必须确认自己是否真的会了

- [ ] 我能无误写出 `LEFT JOIN ... WHERE 右表关键字段 IS NULL`
- [ ] 我能无误写出 `WHERE NOT EXISTS (...)`
- [ ] 我能先写“每个谁 1 行”，再写 `GROUP BY`
- [ ] 我能写出 `CREATE VIEW ... AS SELECT ... GROUP BY ...`
- [ ] 我能写出 `HAVING COUNT(*) >= ALL (...)`
- [ ] 我能区分 `WHERE` 和 `HAVING`
- [ ] 我能说出 `Redo` / `Undo` 的基本判断依据

### 5.3 今天最容易混淆的点

- `LEFT JOIN ... IS NULL` vs `NOT EXISTS`
- 普通视图 vs 统计视图
- `WHERE` vs `HAVING`
- 输出粒度 vs 明细记录粒度
- `ORDER BY LIMIT 1` vs `HAVING >= ALL` 支持并列
- `new row` vs `old row`
- `Redo` vs `Undo`

### 5.4 哪些题值得二刷

- Day 26 错题：第10题、第20题、第22题、第23题、第24题、第25题、第26题、第28题、第29题
- Day 27 今日题：第19题、第20题、第21题、第23题、第24题、第25题、第26题、第27题、第28题、第29题、第30题

### 5.5 下午题今天暴露出的书写问题

- [ ] 是否仍把普通筛选写进 `HAVING`
- [ ] 是否仍在统计视图中漏 `GROUP BY` 维度
- [ ] 是否仍没有先判断结果粒度
- [ ] 是否仍在最多统计题中只写 `LIMIT 1`
- [ ] 是否仍存在别名漏写、字段名写错、`JOIN` 拼写问题
- [ ] 是否能把并发异常写成具体术语，而不是泛泛写“并发错误”
- [ ] 是否能按事务状态判断 Redo / Undo

### 5.6 错题整理动作

- [ ] 已按真实作答在客观题括号内填写答案
- [ ] 已在下午题作答区留下可核查 SQL 或文字骨架
- [ ] 已标记今日最不稳的句型：`LEFT JOIN IS NULL` / `NOT EXISTS` / `GROUP BY HAVING` / `CREATE VIEW` / `HAVING >= ALL` / `Redo Undo`
- [ ] 学习完成后，基于真实作答结果生成：`02_错题复盘/Day 27 错题复盘.md`

### 5.7 明日衔接提醒

- 若 Day 27 SQL 二刷采分点明显稳定，Day 28 可正式进入 **恢复与并发专项**。
- 若 Day 27 SQL 题仍在统计视图、最多统计、`WHERE/HAVING` 上失分，Day 28 不建议完全切新专题，应先生成或补强 `SQL高频写法模板.md`。
- 恢复 / 并发专项开始前，至少要能说清：
  - 丢失修改、脏读、不可重复读的区别；
  - 两段锁协议的基本思想；
  - `Redo` 与 `Undo` 的事务状态判断。
- 后续真题日仍要回刷 SQL：`NOT EXISTS`、`LEFT JOIN ... IS NULL`、统计视图、最多统计是 SQL 模块的保底主线。
