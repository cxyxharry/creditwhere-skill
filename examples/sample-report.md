# creditwhere 贡献分析报告（示例）

> 说明：本文件是演示格式与证据写法的样例，不对应真实仓库数据。

**仓库**：`/workspace/acme-payments-service`
**分析时间**：`2026-05-11 22:00 UTC+8`
**分析范围**：`all history`

---

## 1. 仓库概览

- 总提交数：182
- 贡献者数：3
- 主要语言：TypeScript、SQL
- 核心模块：`src/core/payment/`、`src/core/risk/`、`src/api/`

## 2. 文件重要性地图

| 路径模式 | 重要性 | 理由 |
|----------|--------|------|
| `src/core/payment/*` | 核心 | 支付主流程与状态流转逻辑集中在这里 |
| `src/core/risk/*` | 核心 | 风控规则与决策路径属于关键业务逻辑 |
| `src/api/*` | 高 | 对外接口层，直接影响功能行为 |
| `tests/integration/*` | 高 | 覆盖支付与回调的关键业务场景 |
| `migrations/*` | 中 | 重要但更多是配套演进 |
| `docs/*` | 中 | 文档对理解有帮助，但不直接构成功能实现 |
| `package-lock.json` | 忽略 | 自动生成依赖锁文件 |

## 3. 贡献者分析

### Alice <alice@example.com>

| 维度 | 评估 | 证据 |
|------|------|------|
| 实质度 | 约 75% 为实质变更 | `4ae91fb`（高，新增支付重试状态机）、`f09d122`（高，修复回调幂等）、`91bc0de`（中，重构 service 边界） |
| 核心模块 | 是，集中在支付主流程和风控入口 | `src/core/payment/retry.ts`、`src/core/risk/evaluator.ts`、`src/api/webhook.ts` |
| 存活率 | 约 84% | `Alice 引入的 340 行中，287 行存活到 HEAD（84%）`；`src/core/payment/retry.ts:18-126 — Alice` |
| 提交模式 | 整体正常，message 有信息量，颗粒度较稳定 | `4ae91fb ("add retry state machine for failed captures")`、`f09d122 ("fix webhook idempotency for duplicate callbacks")` |
| 异常标记 | 无明显异常 | 低实质提交样本较少，且核心代码存活率较高 |

**代表性提交**

- `4ae91fb` — "add retry state machine for failed captures" — 实质度：高 — 新增支付失败后的状态机与重试分支
- `f09d122` — "fix webhook idempotency for duplicate callbacks" — 实质度：高 — 修复回调重复入账问题，并补了集成测试
- `91bc0de` — "extract payment service orchestration" — 实质度：中 — 主要是结构重整，但保留了主行为

**整体评价**

Alice 是该仓库里最像核心功能 owner 的作者之一。她的代表性提交主要落在 `src/core/payment/*` 和 `src/core/risk/*`，且 `4ae91fb`、`f09d122` 这类行为级变更在 `HEAD` 中仍有较高留存，说明贡献不只是短期试验。

---

### Bob <bob@example.com>

| 维度 | 评估 | 证据 |
|------|------|------|
| 实质度 | 约 35% 为实质变更，低实质提交偏多 | `6c1fa20`（低，批量改 lint）、`8bd2249`（低，统一 import 顺序）、`de1287e`（中，补支付失败回归测试） |
| 核心模块 | 部分涉及，但以测试和格式调整为主 | `tests/integration/payment-retry.spec.ts`、`src/api/webhook.ts`、`eslint.config.js` |
| 存活率 | 约 58% | `Bob 引入的 190 行中，111 行存活到 HEAD（58%）`；格式性改动较多，难以稳定归属 |
| 提交模式 | message 偏短，颗粒度偏小 | `6c1fa20 ("fix")`、`8bd2249 ("update")`、`9f0cc12 ("lint")` |
| 异常标记 | 低实质提交比例偏高 | 在抽样的 10 个代表性提交里，4 个主要是格式或小修小补 |

**代表性提交**

- `de1287e` — "add retry regression cases" — 实质度：中 — 增加了支付失败重试的回归测试
- `6c1fa20` — "fix" — 实质度：低 — 批量调整 lint 与空行
- `8bd2249` — "update" — 实质度：低 — 统一 import 顺序，行为无明显变化

**整体评价**

Bob 有一定有效贡献，尤其在测试补强上提供了价值，但提交画像偏碎，且低实质提交比例明显高于 Alice 和 Carol。现有样本更支持把他视为支撑型贡献者，而不是主要功能驱动者。

---

### Carol <carol@example.com>

| 维度 | 评估 | 证据 |
|------|------|------|
| 实质度 | 约 60% 为实质变更 | `ab281cf`（高，接手重写风控阈值评估）、`d910ce4`（中，数据库迁移与回填脚本）、`7af0132`（低，更新 README） |
| 核心模块 | 是，主要在风控与数据迁移 | `src/core/risk/thresholds.ts`、`migrations/20260114_backfill_risk_scores.sql` |
| 存活率 | 约 69% | `Carol 引入的 260 行中，180 行存活到 HEAD（69%）`；`src/core/risk/thresholds.ts:40-112 — Carol` |
| 提交模式 | 整体正常，提交颗粒度中等 | `ab281cf ("rewrite adaptive risk thresholds")`、`d910ce4 ("backfill historical risk scores")` |
| 异常标记 | 无明显异常 | 有文档提交，但不构成主要画像 |

**代表性提交**

- `ab281cf` — "rewrite adaptive risk thresholds" — 实质度：高 — 重写风控阈值逻辑，覆盖了 Alice 早期的一部分判断分支
- `d910ce4` — "backfill historical risk scores" — 实质度：中 — 补了迁移与数据回填脚本
- `7af0132` — "update setup docs" — 实质度：低 — 更新接入说明文档

**整体评价**

Carol 的贡献更像第二核心作者，重点在风控模块和数据迁移。`ab281cf` 对 Alice 早期逻辑存在局部替换，但从提交内容看更像后续方案升级，而不是单纯覆盖他人工作。

---

## 4. 交叉对比

| 作者 | 实质变更 | 核心模块 | 存活率 | 异常 | 排序 | 排序理由 |
|------|----------|----------|--------|------|------|----------|
| Alice | 高 | 支付主流程、风控入口 | 84% | 无明显异常 | 1 | 核心逻辑占比最高，且代表性功能提交在 `HEAD` 中留存最多 |
| Carol | 中高 | 风控、迁移 | 69% | 无明显异常 | 2 | 在核心风控模块有明显影响力，但总体留存和覆盖范围略低于 Alice |
| Bob | 中低 | 测试、接口零碎调整 | 58% | 低实质提交比例偏高 | 3 | 有支撑性贡献，但低实质提交较多，核心功能主导性不足 |

### 关键重叠或改写案例

- `src/core/risk/thresholds.ts` 被 Alice 和 Carol 都频繁修改。`ab281cf` 覆盖了 Alice 在 `c83d1b4` 引入的一部分阈值判断，但从最终结构看更像后续迭代重构，而不是低质量返工。
- `src/api/webhook.ts` 由 Alice 完成功能修复，Bob 随后做了若干格式与测试补充。这个重叠更像正常协作，不像冲突。

## 5. 异常标记汇总

| 标记 | 涉及作者 | 证据 | 建议 |
|------|----------|------|------|
| 低实质提交比例偏高 | Bob | `6c1fa20`、`8bd2249`、`9f0cc12` 主要是 lint、import 或小修补 | 人工复核是否存在被拆碎的格式性提交 |

## 6. 局限性声明

- 本报告由 AI Agent 生成，所有判断基于 Git 历史记录的静态分析。
- 无法评估设计讨论、需求澄清、code review、值班支持等非 Git 活动。
- Carol 对 Alice 代码的覆盖可能是方案升级，不宜仅凭存活率断言质量高低。
- Bob 的低实质提交比例偏高，不代表其协作价值低；测试补强和收尾工作在 Git 历史中往往不如主功能提交显眼。

---

*报告由 creditwhere-skill 驱动生成 · Example Agent · GPT-5*
