# data-cleaner

Python 数据清洗自动化脚本，支持批量处理多个 CSV 文件，生成可直接导入 MySQL 的 SQL 文件。

## 技术栈

Python / pandas / MySQL / logging

## 真实数据集处理结果

使用 Kaggle Telco Customer Churn 数据集（7,043 行，21 列）验证：

| 指标 | 结果 |
|------|------|
| 原始行数 | 7,043 行 |
| 发现问题类型 | 3 类 |
| 数据可用率 | 100% |
| 字段完整率 | 100% |

**发现的真实脏数据问题：**
1. `TotalCharges` 字段类型错误：字符串类型存储数值，需强制转换
2. `TotalCharges` 含11行空格伪装的空值：`isnull()` 无法直接检测，需先 `str.strip()` 才能暴露
3. 17列字符串字段存在首尾空格，影响数据库精确匹配

## 功能

- 五步清洗流程：去重 → 缺失值处理 → 类型修正 → 字符串标准化 → 业务逻辑校验
- 主动防御处理空文件（if/else 预判），try/except 捕获格式异常，批量处理出错跳过不崩溃
- logging 同时输出控制台与文件，每步都有结构化日志
- 自动生成含业务指标的清洗报告（可用率、完整率）

## 项目结构
```
data_cleaner/
├── archive/                          # 原始数据集
├── output/                           # 清洗结果
├── telco_cleaner.py                  # 真实数据集清洗脚本
├── data_cleaner.py                   # 核心清洗类
├── batch_cleaner.py                  # 批量处理脚本
└── telco_cleaner.log                 # 清洗日志
```

## 快速开始
```bash
pip install pandas
python telco_cleaner.py
```

## 核心亮点

**隐蔽空值处理**：`TotalCharges` 字段空格伪装的空值无法被 `isnull()` 直接检测，需三步处理：`str.strip()` 剥离空格 → `replace('', None)` 转真实空值 → `pd.to_numeric()` 类型转换。这是真实工业数据集中典型的隐蔽质量问题。
