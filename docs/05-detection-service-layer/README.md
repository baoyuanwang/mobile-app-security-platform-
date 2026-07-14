# Chapter 05 Detection Service Layer

---

# 1. Overview

Detection Service Layer（检测服务层）是移动应用安全检测平台的业务判断层，负责基于 Analysis Engine Layer 输出的标准化分析结果，结合安全规则、知识库、特征库以及 AI 能力，对应用安全风险进行识别和判断。

Analysis Engine Layer 解决的问题是：

> 应用程序做了什么。

Detection Service Layer 解决的问题是：

> 这些行为是否构成安全风险。

Detection Service Layer 面向具体安全场景构建检测能力，包括：

- 恶意软件检测（Malware Detection）；
- 隐私合规检测（Privacy Detection）；
- 涉诈风险检测（Fraud Detection）；
- 恶意广告检测（Advertisement Detection）；
- 内容安全检测（Content Compliance Detection）；
- 仿冒侵权检测（Impersonation Detection）；
- SDK 风险检测（SDK Risk Detection）。

Detection Service Layer 不负责应用程序解析、不负责底层行为采集，而是消费分析结果，并结合安全知识和检测策略形成业务检测结论。

---

# 2. Design Objectives

Detection Service Layer 的设计目标包括：

- 面向业务安全场景提供检测能力；
- 基于分析结果完成风险判断；
- 支持规则、特征和 AI 多种检测方式；
- 输出可解释的检测结果；
- 支撑应用审核、风险治理和安全运营；
- 支持检测能力持续扩展。

---

# 3. Position in Overall Architecture

Detection Service Layer 位于 Analysis Engine Layer 和 Application Access Layer 之间。

整体架构如下：

```text
┌──────────────────────────────────────────────┐
│         Application Access Layer             │
└──────────────────────────────────────────────┘
                     ▲
                     │
┌──────────────────────────────────────────────┐
│         Detection Service Layer              │
│                                              │
│  Malware | Privacy | Fraud | Ad | Content   │
│  SDK Risk | Impersonation                    │
└──────────────────────────────────────────────┘
                     ▲
                     │
┌──────────────────────────────────────────────┐
│          Analysis Engine Layer               │
└──────────────────────────────────────────────┘
                     ▲
                     │
┌──────────────────────────────────────────────┐
│      Execution Environment Layer             │
└──────────────────────────────────────────────┘
```

---

# 4. Core Responsibilities

Detection Service Layer 主要承担以下职责。

## 4.1 Risk Identification

基于分析结果识别安全风险。

输入：
- Standardized Analysis Result
- Detection Rules
- Feature Library
- AI Models
- Security Knowledge

输出：
- Risk Decision

## 4.2 Detection Orchestration

协调多种检测方式完成综合判断。

包括：
- 规则检测
- 特征匹配
- AI 分类
- 行为分析
- 关联分析

## 4.3 Evidence Correlation

将分析证据与风险结论关联。

确保：
- 每个风险结论均有证据支撑
- 证据可追溯到分析源
- 检测结果可解释

## 4.4 Detection Result Output

输出标准化检测结果。

包括：
- 风险类型
- 风险等级
- 风险证据
- 判定依据
- 处置建议

---

# 5. Architecture

Detection Service Layer 由七个检测服务组成。

```text
Detection Service Layer

├── Malware Detection
├── Privacy Detection
├── Fraud Detection
├── Advertisement Detection
├── Content Compliance Detection
├── Impersonation Detection
└── SDK Risk Detection
```

各检测服务共享：
- Analysis Engine Layer 输出的分析结果
- Security Intelligence & Knowledge Platform 提供的知识资产
- 统一的检测框架和结果模型

---

# 6. Detection Flow

典型检测流程如下：

```text
Standardized Analysis Result
         │
         ▼
Detection Service Layer
         │
    ┌────┼────┬────┬────┬────┬────┐
    ▼    ▼    ▼    ▼    ▼    ▼    ▼
  Mal  Priv  Fraud  Ad  Content Imp  SDK
    │    │    │    │    │    │    │
    └────┴────┴────┴────┴────┴────┘
         │
         ▼
  Detection Result Aggregation
         │
         ▼
  Detection Report
```

---

# 7. Detection Methods

各检测服务采用多种检测方法组合。

## 7.1 Signature-Based Detection

基于特征匹配的检测。

- YARA 规则匹配
- Hash 比对（MD5/SHA256）
- 特征字符串匹配
- SDK 指纹匹配

## 7.2 Behavior-Based Detection

基于行为模式的检测。

- API 调用序列分析
- 行为链路还原与匹配
- 异常行为模式识别
- 运行时行为画像

## 7.3 Heuristic Detection

基于启发式规则的检测。

- 可疑代码模式识别
- 风险组合判定
- 阈值触发
- 加权评分

## 7.4 AI-Based Detection

基于机器学习/深度学习的检测。

- CNN/LSTM 恶意应用分类
- GNN 图神经网络分析
- NLP 文本语义分析
- LLM 辅助研判

## 7.5 Knowledge-Based Detection

基于安全知识库的检测。

- IOC 指标匹配
- 恶意家族关联
- 威胁情报关联
- 风险画像匹配

---

# 8. Key Technologies

Detection Service Layer 的关键技术体系如下。

| 技术领域 | 关键技术 | 适用检测服务 |
|----------|---------|-------------|
| 特征匹配 | YARA / ClamAV / Custom Signature Engine | Malware, SDK |
| 代码相似性 | SSDeep / TLSH / BinDiff / SimHash | Malware, Impersonation |
| 恶意分类 | CNN / LSTM / Random Forest / XGBoost | Malware, Fraud |
| 图神经网络 | GNN / GraphSAGE / GCN | Malware, Fraud |
| 污点分析 | Static Taint / Dynamic Taint | Privacy |
| 数据流追踪 | Data Flow Analysis / Taint Tracking | Privacy, SDK |
| 隐私策略引擎 | Rule Engine / Policy Evaluation | Privacy |
| 风险评分 | Scoring Model / Risk Quantification | Fraud, Ad |
| 关联图谱 | Knowledge Graph / Link Analysis | Fraud, Malware |
| NLP | Text Classification / NER / Sentiment | Content, Fraud |
| OCR / 图像识别 | Tesseract / PaddleOCR / CNN | Content, Impersonation |
| 感知哈希 | pHash / dHash / aHash / SIFT | Impersonation |
| SDK 指纹 | Bytecode Pattern / API Fingerprint / Metadata | SDK, Ad |
| 行为画像 | Behavior Profiling / Sequence Modeling | Ad, SDK |
| 大语言模型 | LLM / RAG / Few-shot | 全部（辅助） |

---

# 9. Technical Metrics

Detection Service Layer 的关键技术指标如下。

## 9.1 检测准确性指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 已知恶意软件检出率 | ≥ 99.5% | 基于样本库的已知家族检出 |
| 未知恶意软件检出率 | ≥ 85% | 零日/变异样本的启发式检出 |
| 恶意软件误报率 | ≤ 0.1% | 正常应用被误判为恶意的比例 |
| 隐私违规检出率 | ≥ 90% | 隐私违规行为的识别准确度 |
| 隐私检测误报率 | ≤ 1% | 正常行为被误判为违规的比例 |
| 涉诈应用识别率 | ≥ 85% | 诈骗类应用的识别准确度 |
| 涉诈检测误报率 | ≤ 1% | 正常应用被误判为涉诈的比例 |
| 违规广告检出率 | ≥ 90% | 违规广告行为的识别覆盖度 |
| 内容违规识别率 | ≥ 90% | 违规内容识别准确度 |
| 仿冒应用识别率 | ≥ 90% | 仿冒应用识别准确度 |
| SDK 风险检出率 | ≥ 85% | SDK 恶意/违规行为识别 |

## 9.2 检测效率指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 端到端检测延迟 | ≤ 30 min | 从应用提交到检测报告输出的总耗时 |
| 日检测吞吐量 | ≥ 5,000 apps/day | 平台日处理应用数量 |
| 规则匹配延迟 | ≤ 10 sec | 单应用特征匹配耗时 |
| AI 推理延迟 | ≤ 60 sec | 单应用 ML 模型推理耗时 |

## 9.3 检测覆盖指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 检测规则覆盖 | ≥ 50 个风险类型 | 支持的风险检测类型数量 |
| YARA 规则数量 | ≥ 10,000 条 | 恶意软件检测规则数量 |
| 隐私规则覆盖 | ≥ 200 条 | 隐私合规检测规则数量 |
| AI 模型数量 | ≥ 20 个 | 各检测服务的 ML 模型数量 |
| SDK 知识覆盖 | ≥ 5,000 个 | 可识别的第三方 SDK 数量 |

---

# 10. Detection Result Model

所有检测服务输出统一的检测结果模型。

```text
Detection Result

├── Application ID
├── Detection Time
├── Detection Services
│   ├── Malware Detection Result
│   │   ├── Risk Level (Clean / Suspicious / Malicious)
│   │   ├── Malware Family
│   │   ├── Detection Method
│   │   └── Evidence
│   ├── Privacy Detection Result
│   │   ├── Risk Level
│   │   ├── Violation Type
│   │   ├── Sensitive Data Category
│   │   └── Evidence
│   ├── Fraud Detection Result
│   │   ├── Risk Level
│   │   ├── Fraud Type
│   │   ├── Risk Score
│   │   └── Evidence
│   ├── Advertisement Detection Result
│   │   ├── Risk Level
│   │   ├── Violation Type
│   │   ├── Ad SDK Info
│   │   └── Evidence
│   ├── Content Compliance Result
│   │   ├── Risk Level
│   │   ├── Violation Category
│   │   └── Evidence
│   ├── Impersonation Result
│   │   ├── Risk Level
│   │   ├── Similarity Score
│   │   ├── Target App
│   │   └── Evidence
│   └── SDK Risk Result
│       ├── Risk Level
│       ├── SDK Info
│       ├── Risk Type
│       └── Evidence
├── Overall Risk Level
└── Recommendation
```

---

# 11. Inputs and Outputs

## Input

来自 Analysis Engine Layer：
- Static Analysis Result
- Dynamic Analysis Result
- Program Representation
- Analysis Evidence

来自 Security Intelligence & Knowledge Platform：
- Detection Rules
- Feature Library
- IOC Library
- AI Models
- Knowledge Graph
- Risk Profiles

## Output

- Detection Result
- Risk Decision
- Evidence Report
- Recommendation

---

# 12. Design Principles

Detection Service Layer 遵循以下设计原则。

## Evidence-Based Decision

所有风险判定必须基于证据。

## Multi-Method Fusion

单一检测方法不足以覆盖所有风险，采用多种方法融合。

## Explainable Detection

检测结果必须可解释，可追溯到具体证据。

## Decoupling

检测服务与分析引擎解耦，检测服务之间相互独立。

## Extensibility

支持新增检测服务，无需修改整体架构。

---

# 13. Chapter Organization

本章节包括以下内容：

```text
05-detection-service-layer/

├── README.md
├── malware-detection.md
├── privacy-detection.md
├── fraud-detection.md
├── advertisement-detection.md
├── content-compliance-detection.md
├── impersonation-detection.md
└── sdk-risk-detection.md
```

各模块职责如下：

| Module | Responsibility |
|--------|---------------|
| Malware Detection | 识别恶意软件和恶意行为 |
| Privacy Detection | 识别隐私违规和数据处理风险 |
| Fraud Detection | 识别涉诈应用和黑灰产活动 |
| Advertisement Detection | 识别恶意广告和违规广告行为 |
| Content Compliance Detection | 识别内容安全违规 |
| Impersonation Detection | 识别仿冒侵权应用 |
| SDK Risk Detection | 识别第三方 SDK 安全风险 |

---

# 14. Summary

Detection Service Layer 是移动应用安全检测平台的业务检测核心。

它通过七个专业检测服务，基于 Analysis Engine Layer 的分析结果，结合规则、特征、AI 和知识库，对应用安全风险进行全面识别和判断。

关键技术涵盖特征匹配（YARA）、代码相似性分析（SSDeep/BinDiff）、机器学习分类（CNN/LSTM/GNN）、污点分析、风险评分、知识图谱、NLP/OCR、感知哈希等。

技术指标从检测准确性、检测效率和检测覆盖三个维度定义了能力基线，确保平台具备高准确、高效率、广覆盖的安全检测能力。
