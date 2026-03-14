# data-cleaner

Python 数据清洗自动化脚本，支持批量处理多个 CSV 文件，生成可直接导入 MySQL 的 SQL 文件。

## 技术栈

Python / pandas / MySQL / logging

## 功能

- 五步清洗流程：去重 → 缺失值填充 → 字符串标准化 → 格式校验 → 类型修正
- 主动防御处理空文件（if/else 预判），try/except 捕获格式异常，出错跳过不崩溃
- logging 同时输出控制台与文件，每步都有结构化日志
- 产出 cleaned.sql（含建表语句 + 批量 INSERT），清洗率 10%（200 行 → 180 行有效数据）

## 项目结构
```
data_cleaner/
├── data/              # 原始 CSV 文件
├── output/            # 清洗结果（.sql 文件）
├── data_cleaner.py    # 核心清洗类
├── batch_cleaner.py   # 批量处理脚本
├── generate_sample_data.py
└── cleaner.log
```

## 快速开始
```bash
pip install pandas
python batch_cleaner.py
```

## 核心亮点

主动防御 vs 被动兜底：能预判的错误（空文件）提前用 if/else 处理，意图更清晰；
意外异常用 try/except 兜底，批量处理时单个文件出错不影响整体流程。
