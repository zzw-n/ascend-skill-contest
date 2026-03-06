# Ascend Skill Contest

AGENT SKILL开发大比武。

[海报的位置]

## 如何写Skill

**参考文档**：[Agent Skill 创作最佳实践](docs/agent-skills-best-practices.md)

## 参赛流程

本次比赛分为推理和训练两个赛道。

1. fork并clone本仓库
2. 根据题目完成对应Skill的编写、自验证
3. 提交PR

## 提交要求

1. 所有题目在同一个PR中提交
2. PR模版参考题目要求
3. 目录结构要求：

```
ascend-skill-contest/
├── .agents/             
|    └── skills          # 选手提交的Skill的路径
|           └── skill-name       
├── training/            # 训练题目
├── inference/           # 推理题目
├── docs/                # 文档
│   └── agent-skills-best-practices.md  # Agent Skill 创作最佳实践
└── README.md
```

## 评测方式

在本项目中运行[opencode](https://opencode.ai)，然后输入题目中的prompt，检查是否完成题目要求。

选手编写时所用的AI工作不做限制