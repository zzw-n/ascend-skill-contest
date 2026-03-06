# 题目1: 推理框架(vllm/sglang/xllm/mindie)部署

## 题目概览

| 项目 | 说明 |
|------|------|
| 难度 | ⭐ 初等 |
| 预估时长 | 60 分钟 |

---

## 使用场景

- 对于框架部署不熟悉的新人，用于自动化部署或者指导部署
- 在Agentic Coding场景，使用AI端到端完成搭建开发环境

## 任务描述

使用vllm/sglang/xllm/mindie等推理框架中的**其中一个**部署[Qwen3-0.6B](https://www.modelscope.cn/models/Qwen/Qwen3-0.6B)模型，建议使用镜像部署而不是源码构建部署。

具体要求：

| 项目 | 说明 |
|------|------|
| Prompt | 使用vllm/sglang/xllm/mindie部署Qwen3-0.6B模型 |
| 执行时间 | 30 分钟以内 |

## 输出要求

参赛者需提交：

**目录结构**
   ```
   skill-name/
   ├── SKILL.md        # 必须
   ├── reference/      # 可选
   └── scripts/        # 可选
   ```

## 评分标准

参考 [Agent Skill 创作最佳实践](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)：

| 维度 | 权重 | 说明 |
|------|------|------|
| 功能完整性 | 60% | 是否能成功引导完成 Qwen3-0.6B 部署，覆盖关键步骤 |
| description 质量 | 225% | 是否包含 WHAT（做什么）和 WHEN（何时触发）；是否具体、含关键词；是否用第三人称 |
| 指令与结构 | 10% | SKILL.md 是否简洁（建议 500 行以内）；指令步骤是否清晰可执行；是否合理使用渐进式披露 |
| 代码与脚本 | 10% | 脚本是否明确列出依赖；路径是否使用正斜杠；错误处理是否清晰；是否避免推卸给 Agent |


## PR模板

### 题目1: 推理框架(vllm/sglang/xllm/mindie)部署

#### 推理框架
vllm/sglang/xllm/mindie选一个

#### Prompt

#### 测试结果（截图）
