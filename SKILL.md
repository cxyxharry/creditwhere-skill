---
name: creditwhere
description: Evaluate contribution quality in a Git repository by running git commands, reasoning over evidence, and producing a structured contributor report. Use when the user asks who contributed most, wants to assess repository contributions, compare authors, or analyze contribution quality.
triggers:
  - creditwhere
  - 评估贡献
  - 谁贡献最多
  - 分析仓库贡献
  - analyze contributions
  - who contributed most
---

# creditwhere

## What this skill does

Use this skill when the user wants a qualitative contribution analysis for a Git repository, not just raw commit counts.

You must:

- use the agent's terminal or shell tool to gather Git evidence;
- use model reasoning for all judgments;
- produce a structured report with explicit evidence annotations.

You must not:

- invent a rules engine, scoring script, or regex classifier;
- call external APIs for author ranking or diff labeling;
- output unsupported judgments or value-laden labels such as "摸鱼" or "划水".

## Operating Principles

- Every conclusion must cite evidence.
- If evidence is incomplete, use cautious language such as "可能" or "大概率".
- Analyze one author at a time. Do not load every author's full history into context at once.
- Prefer representative commits over full-history diff dumps.
- Treat commit count as context, not as the final answer.
- Exclude obvious generated or vendored paths by default unless the user asks otherwise.
- Always end the report with a limitations section.

Default exclusions:

- `node_modules/`
- `vendor/`
- `dist/`
- `*.lock`

## Workflow

### Step 1: Confirm Scope

Establish the analysis scope before pulling data.

Defaults:

- repository: current working repository, if one is available;
- time range: all history;
- authors: all authors;
- exclusions: the default exclusions above.

If the repository path is unclear or the current directory is not a Git repository, ask for a repository path before continuing.

State the final scope in one short block before analysis:

```text
Repository: <path>
Time range: <all history or explicit range>
Authors: <all authors or explicit subset>
Excluded paths: <list>
```

### Step 2: Collect Git Data

Gather evidence in controlled batches. Never paste the full repository history into context.

Round 1: repository overview

- `git shortlog -sne --all`
- `git log --oneline --all | wc -l`
- `git log --numstat --format= --all`

Use Round 1 to determine:

- contributor list;
- total commit count;
- changed-file hotspots for later file-importance analysis;
- a rough sense of language and module layout from file extensions and paths.

Round 2: one author at a time

For each author, run:

- `git log --author="<NAME>" --format="%H%x09%ad%x09%s" --date=short --all`
- `git log --author="<NAME>" --numstat --format="commit %H %ad %s" --date=short --all`

Then inspect only a representative subset of commits. Choose commits that maximize coverage across:

- core-module changes;
- largest logical diffs;
- refactors vs behavior changes;
- low-substance examples when they exist;
- different time periods.

Useful follow-up commands for selected commits:

- `git show --stat --summary <commit>`
- `git show --unified=20 <commit> -- <path>`

Round 3: survival and ownership

Identify candidate core files from the changed-file hotspots, then inspect ownership at `HEAD`.

Use:

- `git blame HEAD -- <file>`
- `git blame -L <start>,<end> HEAD -- <file>` for large files

If needed, narrow to specific line ranges or functions rather than blaming an entire large file.

### Step 3: Assess File Importance

Open [prompts/file-importance.md](prompts/file-importance.md) and use it to classify the changed-file hotspots.

Rules:

- infer importance from paths, filenames, and module roles;
- prefer path patterns such as `src/core/*` or `tests/integration/*`;
- do not build numeric weights or a scoring formula;
- do not crawl the whole repository tree if `git log --numstat` already gives sufficient coverage.

Output a short table for later reuse in author analysis.

### Step 4: Deep-Dive Per Author

Open [prompts/author-deep-dive.md](prompts/author-deep-dive.md).

Process authors sequentially. For each author, answer these five questions:

1. How substantive are the author's changes?
2. Did the author work in core modules or mostly peripheral files?
3. How much of the author's introduced code survives to `HEAD`?
4. What does the commit pattern look like?
5. What is the overall qualitative assessment?

Mandatory evidence formats:

```text
- 提交示例：commit abc123 ("fix bug") — 实质度：低 — 只改了 2 行注释
- 文件归属：src/core/handler.py:120-145 — Alice
- 存活率：Alice 引入的 340 行中，287 行存活到 HEAD（84%）
```

Requirements:

- cite 3-5 representative commits when possible;
- distinguish logic change, refactor, test, config, docs, and formatting work;
- keep survival estimates conservative when the blame evidence is partial;
- if multiple identities likely refer to one person, note the ambiguity instead of silently merging it.

### Step 5: Cross-Comparison

Open [prompts/cross-comparison.md](prompts/cross-comparison.md).

After all authors are analyzed, compare them across:

- code overlap on the same files or modules;
- rewrite or replacement patterns;
- anomalous contribution signatures;
- contribution ranking with narrative justification.

Do not collapse the result into a single score. The ranking must be justified in words using the evidence you already collected.

### Step 6: Generate Report

Produce the final report using the template below.

If a section is evidence-poor, say so explicitly instead of filling it with generic text.

## Report Template

```markdown
# creditwhere 贡献分析报告

**仓库**：<路径>
**分析时间**：<时间戳>
**分析范围**：<分支 / 时间范围>

---

## 1. 仓库概览

- 总提交数：____
- 贡献者数：____
- 主要语言：____
- 核心模块：____

## 2. 文件重要性地图

| 路径模式 | 重要性 | 理由 |
|----------|--------|------|
| ... | ... | ... |

## 3. 贡献者分析

### <作者名>

| 维度 | 评估 | 证据 |
|------|------|------|
| 实质度 | 约 X% 实质变更 | `abc123`（高）、`def456`（低） |
| 核心模块 | <是/否/部分> | 核心文件路径 |
| 存活率 | XX% | 引入 N 行，存活 M 行 |
| 提交模式 | <正常/异常/混合> | message、颗粒度、时间分布 |
| 异常标记 | <无/有> | 触发原因与证据 |

**代表性提交**

- `abc123` — "重构引擎模块" — 实质度：高 — 新增 200 行核心逻辑
- `def456` — "update" — 实质度：低 — 只改了 3 行注释
- `ghi789` — "fix typo" — 实质度：低 — 修改 README 拼写

**整体评价**

2-3 句话，必须引用上面的具体证据。

---

## 4. 交叉对比

| 作者 | 实质变更 | 核心模块 | 存活率 | 异常 | 排序 | 排序理由 |
|------|----------|----------|--------|------|------|----------|
| ... | ... | ... | ... | ... | ... | ... |

## 5. 异常标记汇总

| 标记 | 涉及作者 | 证据 | 建议 |
|------|----------|------|------|
| ... | ... | ... | ... |

## 6. 局限性声明

- 本报告由 AI Agent 生成，所有判断基于 Git 历史记录的静态分析。
- 无法评估线下讨论、设计决策、code review 等非 Git 活动。
- 存活率低不一定代表质量问题，也可能来自需求变化或后续重构。
- 提交时间集中不一定代表异常，也可能来自集中开发节奏。
- 本报告应作为讨论起点，而非唯一结论。

---

*报告由 creditwhere-skill 驱动生成 · <Agent 名称> · <模型名称>*
```

## Failure Modes And Recovery

- If the repository is too large, narrow by time range, top authors, or a module subset.
- If `git blame` is too noisy, focus on specific core files or specific representative line ranges.
- If merge commits dominate, prioritize author-specific commits and file ownership evidence.
- If the user only wants a quick answer, provide a shortened report but keep the evidence format.

## Resources

- File importance prompt: [prompts/file-importance.md](prompts/file-importance.md)
- Author deep dive prompt: [prompts/author-deep-dive.md](prompts/author-deep-dive.md)
- Cross-comparison prompt: [prompts/cross-comparison.md](prompts/cross-comparison.md)
- Example output style: [examples/sample-report.md](examples/sample-report.md)
