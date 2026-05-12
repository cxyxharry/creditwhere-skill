# creditwhere-skill

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Skill Type](https://img.shields.io/badge/Skill-AI%20Agent-green)](SKILL.md)
[![Lang: ZH](https://img.shields.io/badge/README-%E4%B8%AD%E6%96%87-red)](README.md)
[English](README_en.md)

> Give credit where credit is due.  
> 一个给 AI Agent 用的 Git 贡献质量分析 Skill。不写规则引擎，不写评分代码——Agent 自己跑 Git 命令搜集证据，用模型推理完成全部判断。

---

## 这是什么

`creditwhere-skill` 是一个 `SKILL.md` 规格的 AI Agent Skill。把它安装到 Hermes、Codex 或任何支持 `SKILL.md` 的 Agent 后，Agent 就能对任意 Git 仓库执行完整的贡献质量分析。

跟传统工具的差别：

| | 传统 Git 统计工具 | creditwhere-skill |
|---|---|---|
| 做法 | 写死规则 / 正则 / 评分公式 | Agent 用模型推理 |
| 判断力 | 只能按预设规则机械分类 | 能理解代码语义、commit message 意图 |
| 证据 | 数字，没有可追溯的证据链 | 每个判断附带 commit hash + diff + blame |
| 维护 | 改规则、调阈值、适配新语言 | 改提示词即可 |
| 依赖 | Python 包、CLI 工具 | 只要 Agent 能跑 `git` |

## 快速开始

### 安装到 Hermes

```bash
# 克隆到 skills 目录
git clone https://github.com/cxyxharry/creditwhere-skill.git ~/.hermes/skills/creditwhere-skill
```

### 安装到 Codex

```bash
git clone https://github.com/cxyxharry/creditwhere-skill.git ~/.codex/skills/creditwhere-skill
```

### 触发

在任意 Git 仓库中，对 Agent 说：

```
分析仓库贡献
谁贡献最多
评估这个项目的贡献质量
analyze contributions
who contributed most
```

Agent 会用 shell 工具执行 `git log` / `git diff` / `git blame`，按 Skill 里定义的六步流程完成分析，输出结构化报告。

## 分析流程

```
确认范围 → 采集 Git 数据 → 评估文件重要性 → 逐作者深度分析 → 交叉对比 → 输出报告
```

每一步的详细指令都在 [SKILL.md](SKILL.md) 里，包括：
- 分批采集策略（避免上下文溢出）
- 单作者五维度分析（实质度、核心模块、存活率、提交模式、异常标记）
- 强制性证据格式
- 完整报告模板

## 项目结构

```text
creditwhere-skill/
├── SKILL.md                       # 主文件：触发条件 + 六步流程 + 报告模板
├── prompts/
│   ├── file-importance.md          # 文件重要性评估提示词
│   ├── author-deep-dive.md         # 单作者深度分析提示词
│   └── cross-comparison.md         # 交叉对比 + 异常标记提示词
├── examples/
│   └── sample-report.md            # 完整示例报告（演示格式与证据写法）
├── README.md                       # 本文件
├── README_en.md                    # English README
└── PLAN.md                         # 项目计划书
```

## 设计原则

- **不写评分公式代码** — 所有判断由 Agent 推理完成
- **不写规则引擎或正则分类器** — 分类依赖模型语义理解
- **不依赖外部 LLM API** — Agent 自己的模型就是分析引擎
- **每个判断都必须带证据** — commit hash、diff 片段、blame 行号
- **每次只分析一个作者** — 分批处理，避免上下文不可控

## 输出要求

最终报告至少包含：

1. 仓库概览（总提交数、贡献者数、主要语言、核心模块）
2. 文件重要性地图（路径模式 × 重要性 × 理由）
3. 每位作者的五维度分析表（实质度 / 核心模块 / 存活率 / 提交模式 / 异常标记）
4. 交叉对比总表（含排序与排序理由）
5. 异常标记汇总（含证据与建议）
6. 局限性声明

每个关键判断附带 `commit hash`、文件路径或 `git blame` 证据。

## 当前状态

| 完成 | 内容 |
|------|------|
| ✅ | Skill 主体 + 六步流程 |
| ✅ | 三份提示词（file-importance / author-deep-dive / cross-comparison） |
| ✅ | 完整示例报告 |
| ✅ | 项目计划书 |
| 🔜 | 多仓库试跑与人工校验 |
| 🔜 | 跨 Agent 兼容性验证（Hermes / Codex / others） |

## 许可

MIT License — 详见 [LICENSE](LICENSE)。
