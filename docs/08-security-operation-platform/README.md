# Chapter 08 Security Operation Platform

---

# 1. Overview

Security Operation Platform（安全运营平台）是移动应用安全检测平台的运营闭环平台，负责风险分析、人工研判、风险处置和运营反馈，建立从风险发现到能力增强的完整安全运营体系。

Security Operation Platform 不负责程序分析，也不负责安全检测本身，而是作为平台的运营中枢，将检测能力、安全数据和专家经验有机结合，形成持续运转的安全治理闭环。

平台核心职责：

- 风险发现与信号接入；
- 自动分析与风险研判；
- 人工审核与专家研判；
- 风险处置与响应执行；
- 运营反馈与知识沉淀。

---

# 2. Design Objectives

Security Operation Platform 的设计目标包括：

- 建立完整安全运营闭环；
- 实现风险快速发现；
- 支持自动分析与人工研判协同；
- 提供标准化风险处置能力；
- 建立运营反馈与知识沉淀机制；
- 支撑平台检测能力持续演进。

---

# 3. Position in Overall Architecture

Security Operation Platform 是三大横向能力平台之一，位于数据平台和知识平台之间。

```text
+--------------------------------------------------------------+
|             Security Big Data Platform                        |
+--------------------------------------------------------------+
                                      │
                                      ▼
+--------------------------------------------------------------+
|             Security Operation Platform                       |
| Risk Discovery | Case | Auto Analysis | Human Review | Action|
+--------------------------------------------------------------+
                                      │
                                      ▼
+--------------------------------------------------------------+
|     Security Intelligence & Knowledge Platform                |
+--------------------------------------------------------------+
                                      │
                                      ▼
                             Detection Capability Evolution
```

Security Operation Platform 消费来自 Security Big Data Platform 的风险信号，输出经过确认的安全事件，并将运营成果沉淀到 Security Intelligence & Knowledge Platform。

---

# 4. Core Modules

```text
Security Operation Platform

├── Risk Discovery
├── Case Management
├── Automated Analysis
├── Human Review
└── Response & Action
```

## 4.1 Risk Discovery

负责风险信号接入和初步筛选。

信号来源：

| 信号源 | 信号类型 | 优先级 |
|--------|---------|--------|
| Detection Service | 检测告警（恶意/隐私/涉诈/广告） | 高 |
| Security Big Data | 风险信号（异常指标/关联告警） | 中 |
| User Feedback | 用户举报/投诉 | 高 |
| Regulatory Notice | 监管通报/合规要求 | 高 |
| Threat Intelligence | IOC 命中/威胁情报关联 | 中 |
| Public Opinion | 舆情风险/媒体曝光 | 中 |

信号处理：

```text
Risk Signal

↓

Signal Deduplication

↓

Signal Enrichment

↓

Priority Scoring

↓

Case Creation / Signal Dismiss
```

- 信号去重：避免重复告警
- 信号丰富：补充上下文信息
- 优先级评分：基于风险等级、影响范围、紧急程度
- 案件创建：达到阈值的信号自动创建案件

---

## 4.2 Case Management

负责安全案件全生命周期管理。

案件模型：

```text
Security Case

├── Case ID
├── Case Type (Malware / Privacy / Fraud / Ad / Content / SDK)
├── Priority (Critical / High / Medium / Low)
├── Status (New / Analyzing / Reviewing / Resolved / Closed)
├── Application Info
├── Risk Evidence
├── Analysis Notes
├── Review Decision
├── Action Record
└── Timeline
```

案件生命周期：

```text
New

↓

Auto Analysis

↓

Human Review (if needed)

↓

Decision

↓

Response

↓

Closed

↓

Knowledge Feedback
```

---

## 4.3 Automated Analysis

负责对案件进行自动化风险分析。

分析能力包括：

### 4.3.1 应用画像分析

自动构建应用风险画像：

- 应用基本信息
- 开发者画像
- 历史检测记录
- 关联应用分析
- 证书/签名分析

### 4.3.2 行为关联分析

自动关联分析应用行为：

- 网络通信关联
- 数据流关联
- SDK 行为关联
- 家族/同源关联

### 4.3.3 风险评分

自动计算应用综合风险评分：

```text
Risk Score = w1 * Malware Score
           + w2 * Privacy Score
           + w3 * Fraud Score
           + w4 * Ad Score
           + w5 * Content Score
           + w6 * Reputation Score
```

### 4.3.4 AI 辅助研判

利用 AI 能力辅助运营分析：

- LLM 辅助分析报告生成
- AI 风险摘要
- 相似案例推荐
- 自动分类建议

---

## 4.4 Human Review

负责人工研判和专家审核。

审核流程：

```text
Auto Analysis Result

↓

Risk Level Decision

┌─────────────┐

│             │

Low Risk     High Risk / Uncertain

│             │

Auto Pass    Human Review

              │

              ├── Review Evidence

              ├── Verify Detection

              ├── Assess Impact

              └── Make Decision

              │

              ├── Confirm Risk

              ├── Dismiss (False Positive)

              └── Escalate
```

审核工具：

- 应用行为回放
- 证据可视化
- 关联分析视图
- 历史对比
- 专家协作

---

## 4.5 Response & Action

负责风险处置和响应执行。

处置动作：

| Action | Description | 适用场景 |
|--------|-------------|---------|
| Block | 阻止应用上架 | 恶意软件、严重涉诈 |
| Remove | 下架已上线应用 | 确认恶意/严重违规 |
| Restrict | 限制分发（限流/降级） | 中等风险、待确认 |
| Remediate | 要求开发者整改 | 可修复的违规行为 |
| Monitor | 持续监控 | 低风险、需观察 |
| Whitelist | 加入白名单 | 确认误报 |
| Notify | 通知开发者/监管 | 需要外部配合 |

处置策略：

```text
Risk Level → Action Mapping

Critical → Block / Remove
High     → Block / Remove / Restrict
Medium   → Restrict / Remediate / Monitor
Low      → Monitor / Notify
Clean    → Pass
```

---

# 5. Operation Workflow

完整安全运营闭环：

```text
Risk Signal (from Big Data Platform / Detection Service)

↓

Signal Processing (Dedup / Enrich / Score)

↓

Case Creation

↓

Automated Analysis

↓

Risk Level Decision

┌─────────────────────┐

│                     │

Low Risk            High Risk / Uncertain

│                     │

Auto Close          Human Review

│                     │

│               ┌─────┼─────┐

│           Confirm  Dismiss  Escalate

│               │     │

│               ↓     ↓

│          Response  Whitelist

│               │

└───────────────┼───────┘

                ↓

        Knowledge Feedback

                ↓

    Security Intelligence & Knowledge Platform

                ↓

    Detection Capability Enhancement
```

---

# 6. Key Technologies

| 技术领域 | 关键技术 | 说明 |
|----------|---------|------|
| 规则引擎 | Drools / Easy Rules / 自研 | 自动化风险判定规则执行 |
| 工作流引擎 | Camunda / Temporal | 运营流程编排和状态管理 |
| AI 辅助分析 | LLM / RAG / Few-shot | 大语言模型辅助研判、报告生成 |
| 风险评分 | XGBoost / LightGBM / Scoring Model | 综合风险评分模型 |
| 关联分析 | Graph Database / Link Analysis | 实体关联、传播分析 |
| 协同平台 | 自研 Review Platform | 人工审核工作台、证据可视化 |
| 通知系统 | Message Queue / Webhook / Email | 处置通知和回调 |
| 审计追踪 | Event Sourcing / Audit Log | 全链路操作审计 |

---

# 7. Technical Metrics

## 7.1 运营效率指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 信号处理延迟 | ≤ 5 min | 从信号产生到案件创建的延迟 |
| 自动分析完成时间 | ≤ 10 min | 案件自动分析完成时间 |
| 人工审核平均时间 | ≤ 30 min | 单案件人工审核平均耗时 |
| 高优案件响应时间 | ≤ 2 hours | Critical/High 案件从创建到处置的时间 |
| 案件闭环率 | ≥ 95% | 案件最终完成闭环的比例 |

## 7.2 分析质量指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 自动判定准确率 | ≥ 90% | 自动分析判定的准确度 |
| 误报处理率 | ≥ 99% | 误报被正确识别和关闭的比例 |
| 漏报率 | ≤ 1% | 实际风险未被发现的概率 |
| 知识反馈率 | ≥ 80% | 案件结论沉淀为知识的比例 |

## 7.3 处置指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 处置执行成功率 | ≥ 99.9% | 风险处置动作成功执行率 |
| 处置通知送达率 | ≥ 99.5% | 处置通知成功送达率 |
| 开发者整改响应率 | ≥ 70% | 收到整改通知后开发者响应率 |

## 7.4 运营规模指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 日均处理案件量 | ≥ 500 个 | 日均创建和处理的案件数量 |
| 并发审核容量 | ≥ 50 人 | 同时在线审核人数 |
| 运营知识产出 | ≥ 100 条/天 | 每日沉淀的安全知识数量 |

---

# 8. Inputs and Outputs

## Input

来自 Security Big Data Platform：
- Risk Signal
- Security Event
- Anomaly Alert

来自 Detection Service Layer：
- Detection Result
- Detection Alert

来自外部：
- User Feedback
- Regulatory Notice
- Threat Intelligence

## Output

- Confirmed Case（→ Security Intelligence & Knowledge Platform）
- Response Action（→ Application Access Layer / App Store）
- Knowledge Feedback（→ Security Intelligence & Knowledge Platform）
- Operation Statistics（→ Portal / Dashboard）

---

# 9. Design Principles

## Closed-Loop Operation

所有风险必须闭环处理。

## Human-Machine Collaboration

自动分析与人工研判协同，取长补短。

## Evidence-Based Decision

所有处置决定基于证据。

## Rapid Response

高风险快速响应，低风险批量处理。

## Knowledge Feedback

所有运营成果沉淀为知识资产。

---

# 10. Summary

Security Operation Platform 是移动应用安全检测平台的运营闭环核心。

它通过 Risk Discovery、Case Management、Automated Analysis、Human Review 和 Response & Action 五个核心模块，建立从风险信号到处置反馈的完整安全运营体系。

关键技术涵盖规则引擎（Drools）、工作流引擎（Camunda）、AI 辅助分析（LLM/RAG）、风险评分模型（XGBoost）和关联分析（Graph Database）等。

技术指标从运营效率、分析质量、处置效果和运营规模四个维度定义了能力基线，确保平台具备快速、准确、闭环的安全运营能力。

Security Operation Platform 是平台持续治理能力的核心，它将每一次运营成果沉淀为安全知识，持续增强检测能力，实现平台能力持续演进。
