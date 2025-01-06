# Lizard 代码复杂度分析工具使用指南

## 1. 简介
Lizard 是一个简单但功能强大的代码复杂度分析工具，支持多种编程语言（包括 C/C++、JavaScript、Python、Ruby 等）。它可以计算代码的圈复杂度（Cyclomatic Complexity）、代码行数等指标。

## 2. 安装方法

### 使用 pip 安装

```bash
pip install lizard
```

### 使用 conda 安装

```bash
conda install lizard
```

## 3. 使用方法

### 分析单个文件

```bash
lizard -a app_cco_clock_overproof_event.c
```

### 分析多个文件

```bash
lizard -a app_cco_clock_overproof_event.c app_cco_clock_overproof_event.h
```

### 分析指定目录下的所有文件

```bash
lizard -a ./
```

### 分析指定目录下的所有文件，并输出到指定文件

```bash
lizard -a ./ -o ./lizard_result.txt
```

### 分析指定目录下的所有文件，并输出到指定文件，并指定输出格式

```bash
lizard -a ./ -o ./lizard_result.txt -f json
```

### 分析指定目录下的所有文件，并输出到指定文件，并指定输出格式，并指定输出文件名

```bash
lizard -a ./ -o ./lizard_result.txt -f json -n ./lizard_result.json
```

### 分析指定目录下的所有文件，并输出到指定文件，并指定输出格式，并指定输出文件名，并指定输出文件路径

```bash
lizard -a ./ -o ./lizard_result.txt -f json -n ./lizard_result.json -p ./lizard_result.json
```


## 4. 常用参数说明

| 参数 | 说明 |
|------|------|
| -l, --language | 指定编程语言 |
| -V, --version | 显示版本信息 |
| -h, --help | 显示帮助信息 |
| -C, --CCN | 设置圈复杂度阈值（默认为15） |
| -L, --length | 设置函数长度阈值 |
| -a, --arguments | 设置函数参数数量阈值 |
| -w, --warnings_only | 只显示超过阈值的警告 |
| -x, --exclude | 排除特定文件或目录 |
| --xml | 输出 XML 格式报告 |
| --html | 输出 HTML 格式报告 |

## 5. 高级用法

### 5.1 生成 XML 报告

```bash
lizard -a ./ -o ./lizard_result.txt -f xml
```

### 5.2 生成 HTML 报告

```bash
lizard -a ./ -o ./lizard_result.txt -f html
```

### 5.3 生成 JSON 报告

```bash
lizard -a ./ -o ./lizard_result.txt -f json
```

### 5.4 排除特定目录

```bash
lizard -x "/test/" path/to/directory/
```

## 6. 输出结果说明

Lizard 的输出结果包含以下信息：
- NLOC：代码行数（不包括空行和注释）
- CCN：圈复杂度
- token count：标记数量
- parameter count：参数数量
- function length：函数长度

## 7. 最佳实践

### 推荐的阈值设置
- CCN (圈复杂度)：≤ 15
- 函数长度：≤ 100 行
- 参数数量：≤ 5 个

### 定期检查
建议将 Lizard 集成到持续集成（CI）流程中，定期检查代码质量。

## 8. 常见问题解决

### 8.1 解析错误
如果遇到解析错误，可以尝试：
- 检查文件编码
- 更新到最新版本
- 使用 --ignore_warnings 参数

### 8.2 性能问题
分析大型项目时，可以：
- 使用 -x 参数排除不需要分析的目录
- 只分析特定语言的文件
- 使用多线程模式（如果支持）

## 9. 参考资源
- [Lizard GitHub 仓库](https://github.com/terryyin/lizard)
- [官方文档](https://lizard.readthedocs.io/)
