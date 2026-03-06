# 技能创作最佳实践

> 原文来源：[Claude Platform - 技能创作最佳实践](https://platform.claude.com/docs/zh-CN/agents-and-tools/agent-skills/best-practices)

学习如何编写有效的技能，使 Claude 能够发现和成功使用。

---

好的技能应该简洁、结构良好且经过真实使用测试。本指南提供实用的创作决策，帮助您编写 Claude 能够有效发现和使用的技能。

有关技能工作原理的概念背景，请参阅 [技能概述](https://platform.claude.com/docs/zh-CN/agents-and-tools/agent-skills/overview)。

## 核心原则

### 简洁是关键

[上下文窗口](https://platform.claude.com/docs/zh-CN/build-with-claude/context-windows) 是一种公共资源。您的技能与 Claude 需要了解的所有其他内容共享上下文窗口，包括：

- 系统提示
- 对话历史
- 其他技能的元数据
- 您的实际请求

技能中的每个token都没有直接成本。启动时，只有所有技能的元数据（名称和描述）被预加载。Claude 仅在技能变得相关时才读取 SKILL.md，并根据需要读取其他文件。但是，在 SKILL.md 中保持简洁仍然很重要：一旦 Claude 加载它，每个token都会与对话历史和其他上下文竞争。

**默认假设**：Claude 已经非常聪明

只添加 Claude 没有的上下文。质疑每一条信息：

- "Claude 真的需要这个解释吗？"
- "我能假设 Claude 知道这个吗？"
- "这段落值得它的token成本吗？"

**好的例子：简洁**（大约 50 个token）：

````markdown
## 提取 PDF 文本

使用 pdfplumber 进行文本提取：

```python
import pdfplumber

with pdfplumber.open("file.pdf") as pdf:
    text = pdf.pages[0].extract_text()
```
````

**不好的例子：过于冗长**（大约 150 个token）：

```markdown
## 提取 PDF 文本

PDF（便携式文档格式）文件是一种常见的文件格式，包含
文本、图像和其他内容。要从 PDF 中提取文本，您需要
使用一个库。有许多库可用于 PDF 处理，但我们
建议使用 pdfplumber，因为它易于使用且能处理大多数情况。
首先，您需要使用 pip 安装它。然后您可以使用下面的代码...
```

简洁版本假设 Claude 知道什么是 PDF 以及库如何工作。

### 设置适当的自由度

将具体程度与任务的脆弱性和可变性相匹配。

**高自由度**（基于文本的说明）：

使用场景：
- 多种方法都有效
- 决策取决于上下文
- 启发式方法指导方法

示例：

```markdown
## 代码审查流程

1. 分析代码结构和组织
2. 检查潜在的错误或边界情况
3. 建议改进可读性和可维护性
4. 验证是否遵守项目约定
```

**中等自由度**（伪代码或带参数的脚本）：

使用场景：
- 存在首选模式
- 某些变化是可以接受的
- 配置影响行为

示例：

````markdown
## 生成报告

使用此模板并根据需要自定义：

```python
def generate_report(data, format="markdown", include_charts=True):
    # 处理数据
    # 以指定格式生成输出
    # 可选地包含可视化
```
````

**低自由度**（特定脚本，很少或没有参数）：

使用场景：
- 操作脆弱且容易出错
- 一致性至关重要
- 必须遵循特定的序列

示例：

````markdown
## 数据库迁移

运行完全相同的脚本：

```bash
python scripts/migrate.py --verify --backup
```

不要修改命令或添加其他标志。
````

**类比**：将 Claude 视为探索路径的机器人：
- **两侧都是悬崖的狭窄桥**：只有一种安全的前进方式。提供具体的护栏和精确的说明（低自由度）。示例：必须按精确顺序运行的数据库迁移。
- **没有危险的开放田野**：许多路径都能成功。给出一般方向并相信 Claude 会找到最佳路线（高自由度）。示例：上下文决定最佳方法的代码审查。

### 使用您计划使用的所有模型进行测试

技能作为模型的附加功能，因此有效性取决于底层模型。使用您计划使用的所有模型测试您的技能。

**按模型的测试考虑**：
- **Claude Haiku**（快速、经济）：技能是否提供了足够的指导？
- **Claude Sonnet**（平衡）：技能是否清晰高效？
- **Claude Opus**（强大的推理）：技能是否避免过度解释？

对 Opus 完美有效的东西可能需要为 Haiku 提供更多细节。如果您计划在多个模型中使用您的技能，请针对所有模型都能很好地工作的说明。

## 技能结构

> **YAML 前置事项**：SKILL.md 前置事项需要两个字段：
>
> `name`：
> - 最多 64 个字符
> - 只能包含小写字母、数字和连字符
> - 不能包含 XML 标签
> - 不能包含保留字："anthropic"、"claude"
>
> `description`：
> - 必须非空
> - 最多 1024 个字符
> - 不能包含 XML 标签
> - 应描述技能的功能和使用时机
>
> 有关完整的技能结构详情，请参阅 [技能概述](https://platform.claude.com/docs/zh-CN/agents-and-tools/agent-skills/overview#skill-structure)。

### 命名约定

使用一致的命名模式使技能更容易引用和讨论。我们建议对技能名称使用**动名词形式**（动词 + -ing），因为这清楚地描述了技能提供的活动或能力。

请记住，`name` 字段必须仅使用小写字母、数字和连字符。

**好的命名示例（动名词形式）**：
- `processing-pdfs`
- `analyzing-spreadsheets`
- `managing-databases`
- `testing-code`
- `writing-documentation`

**可接受的替代方案**：
- 名词短语：`pdf-processing`、`spreadsheet-analysis`
- 面向行动：`process-pdfs`、`analyze-spreadsheets`

**避免**：
- 模糊的名称：`helper`、`utils`、`tools`
- 过于通用：`documents`、`data`、`files`
- 保留字：`anthropic-helper`、`claude-tools`
- 技能集合中的不一致模式

一致的命名使以下操作更容易：
- 在文档和对话中引用技能
- 一目了然地理解技能的功能
- 组织和搜索多个技能
- 维护专业、统一的技能库

### 编写有效的描述

`description` 字段启用技能发现，应包括技能的功能和使用时机。

> ⚠️ **始终用第三人称编写**。描述被注入到系统提示中，不一致的视角可能会导致发现问题。
>
> - **好的**："处理 Excel 文件并生成报告"
> - **避免**："我可以帮助您处理 Excel 文件"
> - **避免**："您可以使用此功能处理 Excel 文件"

**具体并包含关键术语**。包括技能的功能和使用它的具体触发器/上下文。

每个技能恰好有一个描述字段。描述对于技能选择至关重要：Claude 使用它从可能的 100+ 个可用技能中选择正确的技能。您的描述必须提供足够的细节，以便 Claude 知道何时选择此技能，而 SKILL.md 的其余部分提供实现细节。

有效的示例：

**PDF 处理技能**：

```yaml
description: 从 PDF 文件中提取文本和表格、填充表单、合并文档。在处理 PDF 文件或用户提及 PDF、表单或文档提取时使用。
```

**Excel 分析技能**：

```yaml
description: 分析 Excel 电子表格、创建数据透视表、生成图表。在分析 Excel 文件、电子表格、表格数据或 .xlsx 文件时使用。
```

**Git 提交助手技能**：

```yaml
description: 通过分析 git 差异生成描述性提交消息。当用户要求帮助编写提交消息或审查暂存更改时使用。
```

避免模糊的描述，如：

```yaml
description: 帮助处理文档
```

```yaml
description: 处理数据
```

```yaml
description: 对文件进行各种操作
```

### 渐进式披露模式

SKILL.md 作为概述，指向 Claude 根据需要查看的详细材料，就像入职指南中的目录一样。有关渐进式披露如何工作的解释，请参阅概述中的 [技能如何工作](https://platform.claude.com/docs/zh-CN/agents-and-tools/agent-skills/overview#how-skills-work)。

**实用指导**：
- 保持 SKILL.md 正文在 500 行以下以获得最佳性能
- 接近此限制时将内容拆分为单独的文件
- 使用下面的模式有效地组织说明、代码和资源

#### 完整的技能目录结构示例

```
pdf/
├── SKILL.md              # 主要说明（触发时加载）
├── FORMS.md              # 表单填充指南（根据需要加载）
├── reference.md          # API 参考（根据需要加载）
├── examples.md           # 使用示例（根据需要加载）
└── scripts/
    ├── analyze_form.py   # 实用脚本（执行，不加载）
    ├── fill_form.py      # 表单填充脚本
    └── validate.py       # 验证脚本
```

#### 模式 1：高级指南与参考

````markdown
---
name: pdf-processing
description: 从 PDF 文件中提取文本和表格、填充表单、合并文档。在处理 PDF 文件或用户提及 PDF、表单或文档提取时使用。
---

# PDF 处理

## 快速开始

使用 pdfplumber 提取文本：
```python
import pdfplumber
with pdfplumber.open("file.pdf") as pdf:
    text = pdf.pages[0].extract_text()
```

## 高级功能

**表单填充**：参阅 [FORMS.md](FORMS.md) 获取完整指南
**API 参考**：参阅 [REFERENCE.md](REFERENCE.md) 获取所有方法
**示例**：参阅 [EXAMPLES.md](EXAMPLES.md) 获取常见模式
````

Claude 仅在需要时加载 FORMS.md、REFERENCE.md 或 EXAMPLES.md。

#### 模式 2：特定领域组织

对于具有多个领域的技能，按领域组织内容以避免加载无关的上下文。

```
bigquery-skill/
├── SKILL.md (概述和导航)
└── reference/
    ├── finance.md (收入、计费指标)
    ├── sales.md (机会、管道)
    ├── product.md (API 使用、功能)
    └── marketing.md (活动、归因)
```

当用户询问销售指标时，Claude 只需要读取与销售相关的架构，而不是财务或营销数据。

#### 模式 3：条件详情

显示基本内容，链接到高级内容：

```markdown
# DOCX 处理

## 创建文档

使用 docx-js 创建新文档。参阅 [DOCX-JS.md](DOCX-JS.md)。

## 编辑文档

对于简单编辑，直接修改 XML。

**对于跟踪更改**：参阅 [REDLINING.md](REDLINING.md)
**对于 OOXML 详情**：参阅 [OOXML.md](OOXML.md)
```

Claude 仅在用户需要这些功能时读取 REDLINING.md 或 OOXML.md。

### 避免深层嵌套引用

保持引用距离 SKILL.md **一级**。所有参考文件应直接从 SKILL.md 链接，以确保 Claude 在需要时读取完整文件。

**不好的例子：太深**：

```markdown
# SKILL.md
参阅 [advanced.md](advanced.md)...

# advanced.md
参阅 [details.md](details.md)...

# details.md
这是实际信息...
```

**好的例子：一级深**：

```markdown
# SKILL.md

**基本使用**：SKILL.md 中的说明
**高级功能**：参阅 [advanced.md](advanced.md)
**API 参考**：参阅 [reference.md](reference.md)
**示例**：参阅 [examples.md](examples.md)
```

### 使用目录结构化较长的参考文件

对于超过 100 行的参考文件，在顶部包含目录。这确保 Claude 即使在部分读取时也能看到可用信息的完整范围。

## 工作流和反馈循环

### 对复杂任务使用工作流

将复杂操作分解为清晰的顺序步骤。对于特别复杂的工作流，提供一个清单，Claude 可以将其复制到其响应中并在进行时检查。

**示例：PDF 表单填充工作流**：

```markdown
## PDF 表单填充工作流

复制此清单并在完成项目时检查：

任务进度：
- [ ] 步骤 1：分析表单（运行 analyze_form.py）
- [ ] 步骤 2：创建字段映射（编辑 fields.json）
- [ ] 步骤 3：验证映射（运行 validate_fields.py）
- [ ] 步骤 4：填充表单（运行 fill_form.py）
- [ ] 步骤 5：验证输出（运行 verify_output.py）

**步骤 1：分析表单**
运行：`python scripts/analyze_form.py input.pdf`

**步骤 2：创建字段映射**
编辑 `fields.json` 为每个字段添加值。

**步骤 3：验证映射**
运行：`python scripts/validate_fields.py fields.json`

**步骤 4：填充表单**
运行：`python scripts/fill_form.py input.pdf fields.json output.pdf`

**步骤 5：验证输出**
运行：`python scripts/verify_output.py output.pdf`
```

### 实现反馈循环

**常见模式**：运行验证器 → 修复错误 → 重复

## 内容指南

### 避免时间敏感信息

不要包含会过时的信息。使用"旧模式"部分来提供历史背景。

### 使用一致的术语

选择一个术语并在整个技能中使用它。避免混合使用同义词（如"API 端点"、"URL"、"路径"）。

## 常见模式

### 模板模式

为输出格式提供模板。将严格程度与您的需求相匹配。

### 示例模式

对于输出质量取决于看到示例的技能，提供输入/输出对。

### 条件工作流模式

通过决策点指导 Claude，例如"创建新内容？→ 遵循创建工作流" 或 "编辑现有内容？→ 遵循编辑工作流"。

## 评估和迭代

### 首先构建评估

在编写大量文档之前创建评估。评估驱动的开发：

1. **识别差距**：在没有技能的情况下对代表性任务运行 Claude
2. **创建评估**：构建三个场景来测试这些差距
3. **建立基线**：测量没有技能的 Claude 的性能
4. **编写最少说明**：创建足够的内容来解决差距
5. **迭代**：执行评估、与基线比较并改进

### 与 Claude 一起迭代开发技能

与一个 Claude 实例（"Claude A"）合作创建技能，由另一个实例（"Claude B"）在真实任务中测试。根据 Claude B 的观察行为进行迭代改进。

## 要避免的反模式

- **避免 Windows 风格的路径**：使用 `scripts/helper.py`，而非 `scripts\helper.py`
- **避免提供太多选项**：提供默认值，带逃生舱口
- **解决，不要推卸**：编写脚本时处理错误条件
- **避免"巫毒常数"**：为配置参数提供理由
- **避免假设工具已安装**：在说明中明确列出依赖项

## 有效技能清单

### 核心质量

- [ ] 描述具体并包含关键术语
- [ ] 描述包括技能的功能和使用时机
- [ ] SKILL.md 正文在 500 行以下
- [ ] 文件引用一级深
- [ ] 整个技能中术语一致
- [ ] 示例具体，不抽象

### 代码和脚本

- [ ] 脚本解决问题而不是推卸给 Claude
- [ ] 错误处理明确且有帮助
- [ ] 所需的包在说明中列出
- [ ] 使用正斜杠路径

### 测试

- [ ] 至少创建了三个评估
- [ ] 使用多个模型进行了测试
- [ ] 使用真实使用场景进行了测试

---

## 后续步骤

- [开始使用代理技能](https://platform.claude.com/docs/zh-CN/agents-and-tools/agent-skills/quickstart) — 创建您的第一个技能
- [在 Claude Code 中使用技能](https://code.claude.com/docs/skills) — 在 Claude Code 中创建和管理技能
- [在代理 SDK 中使用技能](https://platform.claude.com/docs/zh-CN/agent-sdk/skills) — 在 TypeScript 和 Python 中以编程方式使用技能
- [使用 API 使用技能](https://platform.claude.com/docs/zh-CN/build-with-claude/skills-guide) — 以编程方式上传和使用技能
