---
title: 软考中级数据库系统工程师备考 - 笔记属性规范
type: project-schema
status: active
project: 软考中级数据库系统工程师备考
area: Career and Certification
exam: 数据库系统工程师
exam_level: 软考中级
created: 2026-06-03
updated: 2026-06-03
tags:
  - type/schema
  - exam/db-engineer
  - project/soft-exam-db-engineer
---

# 软考中级数据库系统工程师备考 - 笔记属性规范

本文件定义本项目下 Markdown 笔记的 Obsidian Properties / YAML frontmatter 规范，便于 Dataview 查询、复盘和后续归档。

## 通用字段

```yaml
title:
type:
status:
project: 软考中级数据库系统工程师备考
area: Career and Certification
exam: 数据库系统工程师
exam_level: 软考中级
stage:
category:
created:
updated:
tags:
```

## stage 取值

- `planning`：整体规划
- `tracking`：进度追踪 / Dashboard
- `daily-study`：每日学习资料
- `mistake-review`：错题复盘
- `stage-review`：阶段总结
- `past-paper-library`：真题库
- `mock-exam`：模拟考
- `workflow`：生成框架 / 工作流
- `past-paper-index`：真题索引

## type 取值

- `project`
- `project-dashboard`
- `study-note`
- `mistake-review`
- `summary`
- `answer-template`
- `cheatsheet`
- `past-paper`
- `mock-exam-note`
- `grading-report`
- `wrong-answer-rewrite`
- `answer-extraction`
- `solution-extraction`
- `workflow`
- `resource-index`

## 专用字段

- `day`：每日学习 / Day 错题复盘编号
- `mock_date`：模拟考日期目录，例如 `20260519`
- `paper_year`：真题年份，例如 `2023`
- `paper_session`：`上午` 或 `下午`
- `source_folder`：原始所在项目内目录

## 推荐 Dataview

### 每日学习

```dataview
TABLE day, status, updated
FROM "01 Projects/软考中级数据库系统工程师备考"
WHERE stage = "daily-study"
SORT day ASC
```

### 错题复盘

```dataview
TABLE day, category, updated
FROM "01 Projects/软考中级数据库系统工程师备考"
WHERE type = "mistake-review"
SORT day ASC
```

### 模拟考记录

```dataview
TABLE mock_date, paper_year, paper_session, category, updated
FROM "01 Projects/软考中级数据库系统工程师备考"
WHERE stage = "mock-exam"
SORT mock_date DESC, paper_year DESC
```

### 真题库

```dataview
TABLE paper_year, paper_session, status, updated
FROM "01 Projects/软考中级数据库系统工程师备考"
WHERE type = "past-paper"
SORT paper_year ASC, paper_session ASC
```
