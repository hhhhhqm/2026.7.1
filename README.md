# 西南财经大学培养方案查询系统

基于 2024 级本科人才培养方案，构建的课程数据预处理与查询系统。系统从 PDF 培养方案中提取结构化数据，存入 SQLite 关系数据库，并提供 6 项查询功能的 CLI 交互界面。

## 项目结构

```
curriculum-system/
├── data/                      # 预处理后的 JSON 数据
│   ├── colleges.json          # 学院数据 (5 个学院)
│   ├── majors.json            # 专业数据 (12 个专业)
│   ├── courses.json           # 课程数据 (118 门课程)
│   └── major_courses.json     # 专业-课程关联 (199 条记录)
├── db/
│   ├── schema.sql             # 数据库表结构定义
│   ├── init_db.py             # 数据库初始化脚本
│   ├── data_dump.sql          # 完整 SQL 数据导出 (可导入 DataGrip)
│   └── curriculum.db          # 生成的 SQLite 数据库文件
├── cli.py                     # CLI 查询界面 (6 项查询功能)
└── README.md
```

## 数据来源

- 原始文件：`2024级人才培养方案.pdf`（718 页）
- 覆盖学院：金融学院、计算机与人工智能学院、会计学院、法学院、统计学院
- 数据预处理工具：Python + PyMuPDF

## 数据库设计

### E-R 关系

```
college (1) ──── (N) major
major   (M) ──── (N) course  [通过 major_course 关联表]
```

### 表结构

| 表名 | 主键 | 主要字段 | 约束 |
|------|------|----------|------|
| college | id | name, name_en | name UNIQUE |
| major | id | name, code, college_id, total_credits, degree | college_id FK, UNIQUE(name, code) |
| course | code | name, name_en, credits, hours, nature, department, semester | nature CHECK, credits CHECK |
| major_course | id | major_id, course_code | 双外键, UNIQUE(major_id, course_code) |

## 快速开始

### 1. 初始化数据库

```bash
python db/init_db.py
```

输出示例：
```
==================================================
数据库初始化完成!
  数据库路径: .../db/curriculum.db
  学院数量:   5
  专业数量:   12
  课程数量:   118
  关联记录:   199
==================================================
```

### 2. 运行 CLI 查询系统

```bash
python cli.py
```

### 3. 在 DataGrip 中使用

- 方式一：新建 SQLite 连接，指向 `db/curriculum.db` 文件
- 方式二：新建空 SQLite 数据库，依次执行 `schema.sql` 和 `data_dump.sql`

## 查询功能

| 编号 | 功能 | 说明 |
|------|------|------|
| 1 | 查询某专业的必修课列表 | 输入专业名称，列出所有必修课程 |
| 2 | 查询某门课程的学分学时信息 | 模糊匹配课程名，显示详细信息 |
| 3 | 查询某专业的总学分要求 | 显示毕业学分要求及必修/选修分布 |
| 4 | 查询开设某门课程的所有专业 | 反向查询课程覆盖的专业 |
| 5 | 查询某学院下所有专业概览 | 汇总学院内各专业培养方案 |
| 6 | 关键词模糊搜索课程 | 支持中英文和课程代码搜索 |

## 技术栈

- **数据提取**：PyMuPDF (fitz)
- **数据库**：SQLite 3
- **编程语言**：Python 3
- **查询界面**：CLI (命令行交互)

## 数据统计

- 5 个学院
- 12 个专业
- 118 门独立课程
- 199 条专业-课程关联记录
