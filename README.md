# creditwhere-skill

`creditwhere-skill` 是一个面向 AI Agent 的 Git 贡献质量分析 Skill。它不依赖规则引擎、分类器或外部评估 API，而是让 Agent 自己运行 Git 命令收集证据，再用模型推理完成判断。

## 目标

用户只需要提出类似下面的问题：

- “帮我分析这个仓库谁贡献最多”
- “评估一下各个作者的贡献质量”
- “哪些作者改的是核心逻辑，哪些主要是边缘改动”

Agent 在加载本 Skill 后，应能自主完成：

1. 确认分析范围
2. 分批采集 Git 数据
3. 评估文件重要性
4. 逐作者深度分析
5. 做交叉对比
6. 输出结构化报告

## 设计原则

- 不写评分公式代码
- 不写规则引擎或正则分类器
- 不依赖外部 LLM API
- 每个判断都必须带证据
- 每次只分析一个作者，避免上下文失控

## 项目结构

```text
creditwhere-skill/
├── SKILL.md
├── prompts/
│   ├── file-importance.md
│   ├── author-deep-dive.md
│   └── cross-comparison.md
├── examples/
│   └── sample-report.md
├── README.md
└── PLAN.md
```

## 主要文件说明

- [SKILL.md](SKILL.md)：主 Skill 文件，包含触发描述、六步流程、约束和报告模板。
- [prompts/file-importance.md](prompts/file-importance.md)：文件重要性判断提示词。
- [prompts/author-deep-dive.md](prompts/author-deep-dive.md)：单作者五维度分析提示词。
- [prompts/cross-comparison.md](prompts/cross-comparison.md)：横向对比、异常标记与排序提示词。
- [examples/sample-report.md](examples/sample-report.md)：完整示例报告。

## 使用方式

将本目录作为一个 Skill 安装到支持 `SKILL.md` 的 Agent 环境中，然后在目标 Git 仓库里触发它。

推荐触发语句：

- `分析仓库贡献`
- `谁贡献最多`
- `评估贡献`
- `analyze contributions`

## 输出要求

最终报告应至少包含：

- 仓库概览
- 文件重要性地图
- 每位作者的五维度分析
- 交叉对比总表
- 异常标记汇总
- 局限性声明

并且每个关键判断都要附带 `commit hash`、文件路径或 `git blame` 证据。

## 当前状态

本仓库已完成 Skill 主体、提示词资源、示例报告和 README 文档。

仍需在真实仓库中继续做的事情：

- 多仓库试跑与人工校验
- 针对不同 Agent 的 tool 调用差异做兼容性验证
- 发布前的最终打磨
