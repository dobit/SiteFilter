# 网站筛选工具使用帮助

## 项目简介

网站筛选工具是一个智能网站内容分析系统，支持通过关键字表达式快速筛选和分析网站内容。

## 快速开始

### 运行程序
```bash
# 直接运行可执行文件
SiteFilter.exe

# 命令行模式
SiteFilter.exe --help
```

## 命令行参数

```
usage: SiteFilter.exe [-h] [--mode {import,analyze,export,export-exact}]
                      [--excel-file EXCEL_FILE] [--keywords KEYWORDS]
                      [--keywords-file KEYWORDS_FILE] [--threads THREADS]
                      [--output-file OUTPUT_FILE] [--config CONFIG]

网站筛选工具

options:
  -h, --help            show this help message and exit
  --mode {import,analyze,export,export-exact}
                        运行模式
  --excel-file EXCEL_FILE
                        文件路径 (支持 Excel、CSV、TXT)
  --keywords KEYWORDS   关键字表达式
  --keywords-file KEYWORDS_FILE
                        包含关键字表达式的文件路径
  --threads THREADS     线程数量
  --output-file OUTPUT_FILE
                        输出文件路径
  --config CONFIG       配置文件路径
```

## 使用模式

### 1. 导入模式 (import)

**功能**: 从文件导入 URL 列表到数据库

**支持的文件格式**:
- `.xlsx` - Excel 文件
- `.csv` - CSV 文件
- `.txt` - 文本文件（每行一个 URL）

**示例**:
```bash
# 从 Excel 文件导入
SiteFilter.exe --mode import --excel-file data/urls.xlsx

# 从 CSV 文件导入
SiteFilter.exe --mode import --excel-file data/urls.csv

# 从 TXT 文件导入
SiteFilter.exe --mode import --excel-file data/urls.txt
```

### 2. 分析模式 (analyze)

**功能**: 分析数据库中的网站，匹配关键字表达式

**关键字表达式语法**:
- `&&` - 逻辑与（同时包含）
- `||` - 逻辑或（包含任意一个）
- `()` - 括号优先级
- `""` - 引号包围的完整短语

**示例**:
```bash
# 基础关键字
SiteFilter.exe --mode analyze --keywords "event"

# 逻辑与
SiteFilter.exe --mode analyze --keywords "event && ticket"

# 逻辑或
SiteFilter.exe --mode analyze --keywords "event || concert"

# 组合使用
SiteFilter.exe --mode analyze --keywords "(event || ticket) && (creator) && (stripe)"

# 完整短语匹配
SiteFilter.exe --mode analyze --keywords '"event ticket"'

# 使用线程数
SiteFilter.exe --mode analyze --keywords "event" --threads 50

# 从文件读取关键字
SiteFilter.exe --mode analyze --keywords-file data/keywords.txt
```

**关键字文件格式** (keywords.txt):
```
# 每行一个关键字或表达式
(event || ticket) && (creator) && (stripe)
"event ticket"
concert && music
```

### 3. 导出模式 (export)

**功能**: 导出所有分析结果

**示例**:
```bash
# 导出到 Excel
SiteFilter.exe --mode export --output-file data/results.xlsx

# 导出到 CSV
SiteFilter.exe --mode export --output-file data/results.csv

# 导出到 TXT
SiteFilter.exe --mode export --output-file data/results.txt
```

### 4. 精确导出模式 (export-exact)

**功能**: 只导出完全匹配所有关键字的结果

**示例**:
```bash
SiteFilter.exe --mode export-exact --output-file data/exact_results.xlsx
```

## 线程设置

- **默认**: 5 线程（从配置文件读取）
- **最小**: 1 线程
- **最大**: 100 线程
- **推荐**: 10-50 线程（根据网络速度和系统配置）

**示例**:
```bash
# 10 线程
SiteFilter.exe --mode analyze --keywords "event" --threads 10

# 50 线程
SiteFilter.exe --mode analyze --keywords "event" --threads 50

# 100 线程
SiteFilter.exe --mode analyze --keywords "event" --threads 100
```

## 配置文件

**配置文件**: `config.ini`

**默认配置**:
```ini
[database]
db_name = site_filter.db

[analyzer]
max_threads = 5
request_timeout = 15
max_links = 10

[logger]
log_dir = logs
log_level = INFO
```

**使用自定义配置**:
```bash
SiteFilter.exe --config my_config.ini
```

## 功能特性

- ✅ **多格式导入** - 支持 Excel、CSV、TXT 文件
- ✅ **智能关键字匹配** - 支持逻辑表达式和完整短语
- ✅ **多线程分析** - 1-100 线程可配置
- ✅ **多格式导出** - 支持 Excel、CSV、TXT 输出
- ✅ **实时进度** - 显示分析进度和剩余时间
- ✅ **断点续分析** - 支持中断后继续
- ✅ **自定义配置** - 通过配置文件调整参数
- ✅ **详细日志** - 完整的分析日志记录

## 系统要求

- **操作系统**: Windows 7 或更高版本
- **内存**: 2GB RAM（推荐 4GB+）
- **磁盘空间**: 100MB
- **网络**: 互联网连接

## 日志文件

- **位置**: `logs/site_filter_YYYY-MM-DD.log`
- **内容**: 详细的分析日志，包含错误信息和分析结果

## 数据库

- **文件**: `site_filter.db`
- **类型**: SQLite 数据库
- **表结构**: 自动创建和更新

## 常见问题

### Q: 程序启动后没有反应？
**A**: 检查网络连接，程序需要访问互联网分析网站

### Q: 分析速度很慢？
**A**: 增加线程数，例如 `--threads 50`

### Q: 关键字匹配不准确？
**A**: 使用引号包围完整短语，例如 `--keywords '"event ticket"'`

### Q: 导出文件为空？
**A**: 检查数据库中是否有数据，先运行导入模式

### Q: 程序崩溃？
**A**: 查看 `logs/` 目录下的日志文件获取错误信息

### Q: 如何查看分析结果？
**A**: 使用导出模式将结果导出到 Excel 或 CSV 文件

## 示例工作流

1. **导入 URL**:
   ```bash
   SiteFilter.exe --mode import --excel-file data/urls.xlsx
   ```

2. **分析网站**:
   ```bash
   SiteFilter.exe --mode analyze --keywords "(event || ticket) && (creator) && (stripe)" --threads 50
   ```

3. **导出结果**:
   ```bash
   SiteFilter.exe --mode export --output-file data/results.xlsx
   ```

4. **查看结果**:
   打开 `data/results.xlsx` 文件查看分析结果

## 技术支持

- **日志文件**: `logs/site_filter_YYYY-MM-DD.log`
- **配置文件**: `config.ini`
- **命令帮助**: `SiteFilter.exe --help`

## 版本信息

- **版本**: 1.1.0.0
- **作者**: SiteFilter Team
- **更新日期**: 2026-03-05

## 版权信息

**Copyright © 2026 SiteFilter. All rights reserved.**
