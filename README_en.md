# creditwhere-skill

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Skill Type](https://img.shields.io/badge/Skill-AI%20Agent-green)](SKILL.md)
[中文](README.md)

> Give credit where credit is due.  
> An AI Agent Skill for Git contribution quality analysis. No rules engine, no scoring scripts — the Agent runs git commands, collects evidence, and uses its own model reasoning for every judgment.

---

## What is this

`creditwhere-skill` is a `SKILL.md`-compliant AI Agent Skill. Install it into Hermes, Codex, or any SKILL.md-compatible agent, and the agent gains the ability to perform a complete contribution quality analysis on any Git repository.

How it compares to traditional tools:

| | Traditional Git Stats | creditwhere-skill |
|---|---|---|
| Approach | Hardcoded rules / regex / scoring formulas | Model-driven reasoning |
| Judgment | Mechanical rule-based classification | Semantic understanding of code & commit intent |
| Evidence | Numbers without traceable evidence chains | Every judgment cited with commit hash + diff + blame |
| Maintenance | Edit rules, tweak thresholds, adapt to languages | Edit prompts |
| Dependencies | Python packages, CLI tools | Only needs `git` |

## Quick Start

### Install for Hermes

```bash
git clone https://github.com/cxyxharry/creditwhere-skill.git ~/.hermes/skills/creditwhere-skill
```

### Install for Codex

```bash
git clone https://github.com/cxyxharry/creditwhere-skill.git ~/.codex/skills/creditwhere-skill
```

### Trigger

From any Git repository, tell your agent:

```
analyze contributions
who contributed most
assess contribution quality
分析仓库贡献
谁贡献最多
```

The agent will use its shell tool to run `git log` / `git diff` / `git blame`, follow the 6-step workflow defined in this skill, and produce a structured report.

## Analysis Workflow

```
Confirm Scope → Collect Git Data → Assess File Importance → Per-Author Deep Dive → Cross-Comparison → Generate Report
```

Every step is detailed in [SKILL.md](SKILL.md), including:
- Batched data collection strategy (prevent context overflow)
- Per-author five-dimension analysis (substance, core modules, survival rate, commit patterns, anomaly flags)
- Mandatory evidence formats
- Full report template

## Project Structure

```text
creditwhere-skill/
├── SKILL.md                       # Main file: triggers + 6-step workflow + report template
├── prompts/
│   ├── file-importance.md          # File importance assessment prompt
│   ├── author-deep-dive.md         # Per-author deep-dive prompt
│   └── cross-comparison.md         # Cross-comparison + anomaly flagging prompt
├── examples/
│   └── sample-report.md            # Full example report (format & evidence style demo)
├── README.md                       # 中文 README
├── README_en.md                    # This file
└── PLAN.md                         # Project plan
```

## Design Principles

- **No scoring formula code** — all judgments are model-reasoned
- **No rules engine or regex classifier** — classification relies on semantic understanding
- **No external LLM API dependencies** — the agent's own model is the analysis engine
- **Every judgment must carry evidence** — commit hash, diff snippet, blame line numbers
- **One author at a time** — batched processing prevents context blow-up

## Output Requirements

The final report must include at minimum:

1. Repository overview (total commits, contributors, language, core modules)
2. File importance map (path pattern × importance × rationale)
3. Per-author five-dimension analysis table (substance / core modules / survival rate / commit patterns / anomaly flags)
4. Cross-comparison table (with ranking and ranking justification)
5. Anomaly flag summary (with evidence and recommendations)
6. Limitations statement

Every key judgment must be accompanied by a `commit hash`, file path, or `git blame` evidence.

## Current Status

| Done | Item |
|------|------|
| ✅ | Skill body + 6-step workflow |
| ✅ | Three prompt files (file-importance / author-deep-dive / cross-comparison) |
| ✅ | Full example report |
| ✅ | Project plan |
| 🔜 | Multi-repo trial runs & human review |
| 🔜 | Cross-agent compatibility testing (Hermes / Codex / others) |

## License

MIT License — see [LICENSE](LICENSE).
