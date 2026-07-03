---
title: 下午题 SQL 核心语法高频挖空与终极答题模板
type: answer-template
status: active
project: 软考中级数据库系统工程师备考
area: Career and Certification
exam: 数据库系统工程师
exam_level: 软考中级
stage: stage-review
category: answer-template
source_folder: 04_阶段总结
created: 2026-06-03
updated: 2026-06-03
tags:
  - type/project-note
  - status/active
  - exam/db-engineer
  - project/soft-exam-db-engineer
  - type/template
  - use/exam-answer-template
  - stage/stage-review
---

# 软考数据库系统工程师｜下午题 SQL 核心语法高频挖空与终极答题模板

> 适用范围：软考中级《数据库系统工程师》下午案例分析题。  
> 使用目标：考前快速背诵、SQL 填空题临场校准、错题复盘时查漏补缺。  
> 内容来源：由原文件 `下午题 SQL 核心语法高频挖空与终极答题模板.txt` 整理为知识库 Markdown 格式。

## 一、使用说明

本模板围绕下午题高频 SQL 与数据库程序设计挖空点整理，重点覆盖：

- `CREATE TABLE` 与三大完整性约束
- 高级 `SELECT` 查询与视图 `VIEW`
- 数据更新与删除
- 存储过程与游标
- 行级触发器与 `NEW` / `OLD` 伪记录
- 临考默写检测

> 复习建议：先背“标准语法模板”，再背“真题挖空映射表”。做题时先识别题干关键词，例如“最低为 0”“总使用量”“未使用”“降序”“行级触发器”。

---

## 二、核心考点一：`CREATE TABLE` 与三大完整性约束

软考对 `CREATE TABLE` 的考查常出现在大题第一问，主要挖空点集中在：

- 主键：实体完整性
- 外键：参照完整性
- `CHECK` 约束：用户定义完整性

### 2.1 标准语法填空模板

```sql
CREATE TABLE 表名 (
    -- 1. 主键定义
    主键列名 数据类型 PRIMARY KEY, 
    列名2 数据类型 NOT NULL,
    
    -- =================================================================
    -- 【外键定义版本 A】：列级外键（最常考，直接在列名后面写，无 FOREIGN KEY 关键字）
    -- =================================================================
    外键列名1 数据类型 REFERENCES 父表名1 (父表主键列1),
    
    -- 2. 普通检查约束
    数值列名 INT CHECK (数值列名 >= 0),
    
    -- =================================================================
    -- 【外键定义版本 B】：表级外键（独立成行，多用于复合外键或显式命名约束）
    -- =================================================================
    -- 语法：FOREIGN KEY (本表外键列) REFERENCES 父表名 (父表主键列)
    FOREIGN KEY (外键列名2) REFERENCES 父表名2 (父表主键列2)
    
    -- 【级联拓展】：若题目要求“主表删除时从表跟着删除”或“设置为空”，需在末尾追加：
    -- ON DELETE CASCADE   (级联删除)
    -- ON DELETE SET NULL  (级联置空)
);

-- =================================================================
-- 【复合主外键特例】：如果是多对多关系表（如2022真题的“供货”表），主外键需要这样写：
-- =================================================================
CREATE TABLE 供货 (
    供应商名称 CHAR(20),
    医院名称 CHAR(20),
    供货内容 VARCHAR(100),
    -- 1. 独立定义复合主键
    PRIMARY KEY (供应商名称, 医院名称),
    -- 2. 独立定义多个表级外键
    FOREIGN KEY (供应商名称) REFERENCES 供应商 (供应商名称),
    FOREIGN KEY (医院名称) REFERENCES 医院 (医院名称)
);
```

### 2.2 历年真题高频挖空映射表

| 挖空位置 / 典型代码上下文                     | 标准正确答案            | 避坑提示                                                                   |
| ---------------------------------- | ----------------- | ---------------------------------------------------------------------- |
| `CREATE ____ PART (`               | `TABLE`           | 基础建表关键字，大小写均可。                                                         |
| `Pno CHAR(10) ____`                | `PRIMARY KEY`     | 定义单列主键，体现实体完整性。                                                        |
| `Sno CHAR(4) ____ STORE(Sno)`      | `REFERENCES`      | 参照完整性关键字，后接被引用父表及其主键。                                                  |
| `Stock INT ____;`                  | `CHECK(stock>=0)` | 题目要求“库存量最低为 0”时，必须显式使用 `CHECK`。                                        |
| `____ (Sno) REFERENCES STORE(Sno`) | `FOREIGN KEY`     | **表级外键**：空缺独立成行，后面紧跟括号包裹的本表列名，此时必须填 `FOREIGN KEY` 关键字。                 |
| `____ FK_Sno FOREIGN KEY ...`      | `CONSTRAINT`      | **约束命名**：如果外键前面还有一个自定义的别名（如 FK_xxx），那最前面的空缺百分之百是声明约束的关键字 `CONSTRAINT`。 |

### 2.3 速记规则

- “唯一标识一行” → `PRIMARY KEY`
- “参照另一个表” → `REFERENCES`
- “最低、最高、范围、只能取某些值” → `CHECK`
- “不能为空” → `NOT NULL`

---

## 三、核心考点二：高级 `SELECT` 查询与 `VIEW` 视图

下午题 SQL 填空常给出复杂业务查询，重点考查：

- 聚合函数
- 分组查询
- 集合操作：`UNION` / `UNION ALL`
- 子查询：`IN` / `NOT IN`
- 排序：`ORDER BY ... DESC`

### 3.1 创建视图标准模板

```sql
CREATE VIEW 视图名 AS
SELECT 分组列, SUM(数量列) AS 别名
FROM 源表
GROUP BY 分组列;
```

> 只要 `SELECT` 中同时出现普通列和聚合函数，通常必须配合 `GROUP BY`。

### 3.2 集合与嵌套查询标准模板

```sql
SELECT A.列1, A.列2, (A.列3 + B.列4) AS 总量
FROM 表A, 表B
WHERE 表A.关联键 = 表B.关联键

UNION ALL

SELECT 列1, 列2, 列3 AS 总量
FROM 表A
WHERE 关联键 NOT IN (
    SELECT DISTINCT 关联键
    FROM 表C
)

ORDER BY 总量 DESC;
```

### 3.3 `UNION` 与 `UNION ALL` 区别

| 写法 | 含义 | 常见考点 |
|---|---|---|
| `UNION` | 合并两个查询结果，并去重 | 题干强调“合并且去重”时使用。 |
| `UNION ALL` | 合并两个查询结果，保留重复行 | 题干需要完整保留两部分结果时使用。 |

### 3.4 排序方向

| 写法 | 含义 |
|---|---|
| `ASC` | 升序，默认排序方向。 |
| `DESC` | 降序。题干出现“降序排列”必须写 `DESC`。 |

### 3.5 历年真题高频挖空映射表

| 挖空位置 / 典型代码上下文 | 标准正确答案 | 避坑提示 |
|---|---|---|
| `SELECT Pno, ____ AS Usage FROM PJ` | `SUM(Qty)` | 求总量用 `SUM`；统计条数才用 `COUNT`。 |
| `FROM PJ ____ BY Pno;` | `GROUP` | 聚合函数配合普通列时必须 `GROUP BY`。 |
| `WHERE PART.Pno=PART_USED.Pno ____ SELECT ...` | `UNION ALL` | 需要保留完整结果时常用 `UNION ALL`。 |
| `WHERE Pno ____ (SELECT DISTINCT Pno FROM PJ)` | `NOT IN` | 查询“未参与 / 未使用”数据常用 `NOT IN`。 |
| `ORDER BY Total ____` | `DESC` | 题目要求“降序排列”必须写 `DESC`。 |

---

## 四、核心考点三：数据更新与删除

### 4.1 修改数据：`UPDATE`

```sql
UPDATE 表名
SET 列名 = 新值
WHERE 条件;
```

示例：

```sql
UPDATE part
SET sno = 'A002'
WHERE sno = 'A006';
```

### 4.2 删除记录：`DELETE`

```sql
DELETE FROM 表名
WHERE 条件;
```

示例：

```sql
DELETE FROM store
WHERE sno = 'A006';
```

### 4.3 常见业务语义

| 题干描述 | 常用 SQL |
|---|---|
| 将某仓库中的零件转入另一个仓库 | `UPDATE part SET sno='新仓库' WHERE sno='旧仓库';` |
| 迁移完成后删除旧仓库记录 | `DELETE FROM store WHERE sno='旧仓库';` |
| 查询记录 | `SELECT ... FROM ... WHERE ...;` |

> 避坑：题干说“拆除仓库，该仓库中的零件转入其他仓库”，通常先更新零件表，再删除仓库表记录。不要把 `DELETE` 写成 `SELECT`。

---

## 五、核心考点四：PL/SQL 存储过程与游标

软考下午题对存储过程的考查，常集中在游标流程：

1. 游标声明
2. 打开游标
3. 抓取数据
4. 判断退出
5. 关闭游标
6. 提交或异常回滚

### 5.1 标准语法填空模板

```sql
CREATE PROCEDURE 存储过程名 (
    参数名 IN/OUT 数据类型
)

DECLARE
    v_variable1 数据类型;
    v_variable2 数据类型;

    -- 1. 游标声明
    CURSOR 游标名 IS
        SELECT 字段1, 字段2
        FROM 表名;

BEGIN
    -- 2. 打开游标
    OPEN 游标名;

    LOOP
        -- 3. 抓取数据 INTO 变量
        FETCH 游标名
        INTO v_variable1, v_variable2;

        -- 4. 退出条件：游标属性 %NOTFOUND
        IF 游标名%NOTFOUND THEN
            EXIT;
        END IF;

        -- 5. 核心业务处理
        -- INSERT / UPDATE / DELETE ...
    END LOOP;

    -- 6. 关闭游标与事务提交
    CLOSE 游标名;

    COMMIT;

EXCEPTION
    WHEN OTHERS THEN
        ROLLBACK;
END;
```

### 5.2 游标流程速记

| 步骤 | 关键字 | 作用 |
|---|---|---|
| 声明游标 | `CURSOR ... IS SELECT ...` | 定义结果集。 |
| 打开游标 | `OPEN 游标名` | 准备读取结果集。 |
| 抓取数据 | `FETCH 游标名 INTO 变量` | 将当前行放入变量。 |
| 判断结束 | `游标名%NOTFOUND` | 判断是否已经无数据可取。 |
| 退出循环 | `EXIT` | 跳出循环。 |
| 关闭游标 | `CLOSE 游标名` | 释放游标资源。 |

> 避坑：`FETCH` 是取数据，不是 `INSERT`；正常循环结束用 `EXIT`，不是 `ROLLBACK`。

---

## 六、核心考点五：行级触发器与伪记录 `NEW` / `OLD`

触发器常考核心：

- 触发时机：`BEFORE` / `AFTER`
- 触发事件：`INSERT` / `UPDATE` / `DELETE`
- 作用范围：`FOR EACH ROW`
- 伪记录：`NEW` / `OLD`

### 6.1 标准语法填空模板

```sql
CREATE TRIGGER 触发器名

{BEFORE | AFTER}
{INSERT | UPDATE | DELETE}
ON 表名

FOR EACH ROW

WHEN (NEW.状态列 = 1)

DECLARE
    v_temp VARCHAR(30);

BEGIN
    -- BEGIN...END 中引用新行字段
    -- 标准 PL/SQL 中通常需要使用 :NEW 或 :OLD
    SELECT 手机号
    INTO v_temp
    FROM 用户表
    WHERE 用户ID = :NEW.用户ID;
END;
```

### 6.2 `NEW` 与 `OLD` 选择指南

| 触发事件 | 可使用伪记录 | 含义与使用场景 |
|---|---|---|
| `INSERT` | 只允许使用 `NEW` | `NEW.字段名` 表示刚插入的新数据；此时 `OLD` 为空。 |
| `DELETE` | 只允许使用 `OLD` | `OLD.字段名` 表示被删除的旧数据；此时 `NEW` 为空。 |
| `UPDATE` | `NEW` 和 `OLD` 均可 | `OLD` 表示修改前；`NEW` 表示修改后，常用于计算差额。 |

### 6.3 触发器高频填空提示

| 挖空点 | 常见答案 | 判断依据 |
|---|---|---|
| 触发时机 | `BEFORE` / `AFTER` | 看业务是在操作前校验，还是操作后联动处理。 |
| 触发对象 | `ON 表名` | 看题干要求监控哪张表。 |
| 行级触发器 | `FOR EACH ROW` | 每插入/更新/删除一行都触发一次。 |
| 新行字段 | `NEW.字段名` 或 `:NEW.字段名` | 插入或更新后的字段值。 |
| 旧行字段 | `OLD.字段名` 或 `:OLD.字段名` | 删除或更新前的字段值。 |

> 避坑：`NEW` 不要拼错；字段名前是否加冒号取决于题目给出的 SQL 方言和上下文，但 PL/SQL 中常见写法是 `:NEW.字段名`。

---

## 七、临考模拟演练：闭眼默写检测

请尝试在脑海中或草稿纸上完成以下填空。

### 7.1 练习题

1. 声明外键参照完整性的关键字是：`______`
2. 约束取值范围，例如“工作年限 >= 0”的关键字是：`______`
3. 把两个 `SELECT` 查询结果集合并并且去重的关键字是：`______`
4. 存储过程中抓取游标数据的动作是：`______ 游标名 INTO 变量;`
5. 声明行级触发器的固定短语是：`______ ______ ______`

### 7.2 标准答案

| 题号 | 答案 |
|---:|---|
| 1 | `REFERENCES` |
| 2 | `CHECK` |
| 3 | `UNION` |
| 4 | `FETCH` |
| 5 | `FOR EACH ROW` |

---

## 八、临场答题模板

### 8.1 SQL 填空题检查顺序

1. 先看题干业务动词：创建、查询、统计、更新、删除、触发。
2. 再看约束关键词：唯一、参照、最低、范围、不能为空。
3. 看 `SELECT` 中是否同时存在普通列和聚合函数。
4. 看题目要“已使用”还是“未使用”。
5. 看结果是否要求升序或降序。
6. 看是否涉及每一行操作，若是，多半需要 `FOR EACH ROW`。

### 8.2 高频关键词到答案映射

| 题干关键词 | 优先联想答案 |
|---|---|
| 主键、唯一标识 | `PRIMARY KEY` |
| 外键、引用、参照 | `REFERENCES` |
| 最低为 0、范围限制 | `CHECK(...)` |
| 总量、合计 | `SUM(...)` |
| 分组统计 | `GROUP BY` |
| 未出现、未使用、未参与 | `NOT IN` |
| 合并结果并保留重复 | `UNION ALL` |
| 合并结果并去重 | `UNION` |
| 降序 | `DESC` |
| 抓取游标数据 | `FETCH ... INTO ...` |
| 游标无数据 | `%NOTFOUND` |
| 行级触发 | `FOR EACH ROW` |
| 插入后的新值 | `NEW` / `:NEW` |
| 删除前的旧值 | `OLD` / `:OLD` |

## 九、最小背诵版

```text
PRIMARY KEY：主键，实体完整性。
REFERENCES：外键，参照完整性。
CHECK：取值范围约束。
SUM：求总量；COUNT：数记录条数。
GROUP BY：普通列 + 聚合函数时使用。
NOT IN：未参与、未使用。
UNION：合并并去重。
UNION ALL：合并且保留重复。
DESC：降序。
CURSOR：声明游标。
FETCH INTO：抓取游标数据。
%NOTFOUND：游标无数据。
EXIT：退出循环。
FOR EACH ROW：行级触发器。
NEW：新行。
OLD：旧行。
```
