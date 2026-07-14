# Chapter 09 Security Intelligence & Knowledge Platform

---

# 1. Overview

Security Intelligence & Knowledge Platform（安全智能与知识平台）是移动应用安全检测平台的知识资产中心，负责安全知识的沉淀、管理和输出，驱动检测能力持续演进。

Security Intelligence & Knowledge Platform 不负责安全检测，也不负责安全运营，而是作为平台的知识引擎，将安全运营成果、样本积累、专家经验转化为可复用的安全知识资产，持续反哺检测能力。

平台核心职责：

- 安全样本与特征管理；
- IOC 指标管理；
- 检测规则管理；
- 安全知识图谱构建；
- AI 模型资产管理；
- 检测能力输出与增强。

---

# 2. Design Objectives

Security Intelligence & Knowledge Platform 的设计目标包括：

- 建立统一的安全知识资产体系；
- 实现知识从采集到输出的全生命周期管理；
- 构建安全知识图谱，支撑关联分析；
- 管理 AI 模型资产，支撑检测能力智能化；
- 驱动检测规则和特征持续演进；
- 为 Detection Service Layer 提供知识支撑。

---

# 3. Position in Overall Architecture

Security Intelligence & Knowledge Platform 是三大横向能力平台的末端，也是检测能力演进的关键环节。

```text
+--------------------------------------------------------------+
|             Security Big Data Platform                        |
+--------------------------------------------------------------+
                                      │
                                      ▼
+--------------------------------------------------------------+
|             Security Operation Platform                       |
+--------------------------------------------------------------+
                                      │
                                      ▼
+--------------------------------------------------------------+
|     Security Intelligence & Knowledge Platform                |
| Sample | Feature | IOC | Rule | Graph | Model | Knowledge    |
+--------------------------------------------------------------+
                                      │
                                      ▼
                             Detection Capability Evolution
```

Security Intelligence & Knowledge Platform 消费来自安全运营的确认案件，将运营成果转化为检测规则、特征、IOC、模型等知识资产，持续增强 Detection Service Layer 的检测能力。

---

# 4. Core Modules

```text
Security Intelligence & Knowledge Platform

├── Sample Management
├── Feature Library
├── IOC Library
├── Rule Library
├── Knowledge Graph
├── AI Model Repository
└── Knowledge Management
```

## 4.1 Sample Management

负责安全样本的全生命周期管理。

样本类型：

| 类型 | 说明 |
|------|------|
| 恶意应用样本 | 已确认的恶意应用 APK |
| 隐私违规样本 | 已确认的隐私违规应用 |
| 涉诈应用样本 | 已确认的涉诈应用 |
| 广告违规样本 | 已确认的广告违规应用 |
| 正常应用样本 | 用于基线对比的正常应用 |
| 家族代表样本 | 每个恶意家族的代表性样本 |
| 对抗样本 | 用于 AI 模型训练的对抗样本 |

样本管理能力：

```text
Sample Acquisition

↓

Sample Analysis

↓

Sample Classification

↓

Feature Extraction

↓

Sample Storage

↓

Sample Retrieval
```

- 样本采集：来自安全运营确认、外部情报、蜜罐捕获
- 样本分析：自动提取静态/动态特征
- 样本分类：按家族、类型、行为分类
- 特征提取：YARA 特征、行为特征、网络特征
- 样本存储：去重、压缩、加密存储
- 样本检索：多维检索、相似度查询

---

## 4.2 Feature Library

负责检测特征的统一管理。

特征类型：

| 特征类型 | 说明 | 示例 |
|----------|------|------|
| 文件特征 | 文件级哈希和结构特征 | MD5、SHA256、SSDeep、文件大小 |
| 代码特征 | 代码级特征 | YARA 规则、API 序列、字符串常量 |
| 行为特征 | 运行行为特征 | 系统调用序列、网络行为模式 |
| 网络特征 | 网络通信特征 | C2 域名、IP 地址、URL 模式 |
| SDK 特征 | 第三方 SDK 指纹 | 包名模式、API 签名、资源特征 |
| UI 特征 | 界面相关特征 | 图标哈希、布局特征、文本模式 |

特征生命周期：

```text
Feature Discovery (from analysis / operation)

↓

Feature Validation (accuracy / coverage testing)

↓

Feature Release (deploy to detection service)

↓

Feature Monitoring (effectiveness tracking)

↓

Feature Update / Deprecation
```

---

## 4.3 IOC Library

负责威胁指标（Indicator of Compromise）的统一管理。

IOC 类型：

| IOC 类型 | 说明 |
|----------|------|
| File Hash | 恶意文件哈希（MD5/SHA256） |
| Domain | 恶意域名、C2 域名 |
| IP Address | 恶意 IP、C2 服务器 |
| URL | 恶意 URL、钓鱼链接 |
| Certificate | 恶意签名证书 |
| Email | 诈骗邮箱地址 |
| Network Pattern | 网络通信模式 |

IOC 来源：

- 安全运营确认
- 威胁情报平台（MISP / STIX/TAXII）
- 外部情报源（VirusTotal / AlienVault OTX）
- 自动提取（从样本分析中提取）

IOC 管理能力：

- IOC 采集与导入
- IOC 标准化（STIX 2.1 格式）
- IOC 去重与合并
- IOC 关联分析
- IOC 时效性管理（过期标记）
- IOC 输出到检测服务

---

## 4.4 Rule Library

负责检测规则的统一管理。

规则类型：

| 规则类型 | 说明 | 引擎 |
|----------|------|------|
| YARA 规则 | 文件/代码特征匹配 | YARA Engine |
| 隐私合规规则 | 隐私违规行为判定 | Policy Engine |
| 涉诈风险规则 | 涉诈行为/画像判定 | Rule Engine |
| 广告行为规则 | 违规广告行为判定 | Rule Engine |
| 内容合规规则 | 违规内容判定 | Content Engine |
| SDK 风险规则 | SDK 行为/合规判定 | Rule Engine |
| 仿冒检测规则 | 仿冒行为判定 | Similarity Engine |

规则生命周期：

```text
Rule Creation (from operation / expert / AI)

↓

Rule Validation (accuracy / false positive testing)

↓

Rule Review (expert review and approval)

↓

Rule Release (gray / full deployment)

↓

Rule Monitoring (detection rate / false positive tracking)

↓

Rule Update / Deprecation
```

---

## 4.5 Knowledge Graph

负责安全知识图谱的构建和查询。

实体类型：

| 实体类型 | 说明 |
|----------|------|
| Application | 应用实体 |
| Developer | 开发者实体 |
| Certificate | 签名证书实体 |
| SDK | 第三方 SDK 实体 |
| Domain | 域名实体 |
| IP | IP 地址实体 |
| Malware Family | 恶意软件家族 |
| IOC | 威胁指标 |
| Vulnerability | 漏洞实体 |

关系类型：

| 关系类型 | 说明 |
|----------|------|
| signed_by | 应用由某证书签名 |
| developed_by | 应用由某开发者开发 |
| uses_sdk | 应用使用某 SDK |
| communicates_with | 应用与某域名/IP 通信 |
| belongs_to_family | 应用属于某恶意家族 |
| shares_code_with | 应用与某应用共享代码 |
| shares_infrastructure | 应用共享基础设施 |

知识图谱应用：

- 关联分析（发现同源/同基础设施应用）
- 传播分析（追踪恶意应用传播链）
- 家族演化（追踪恶意家族变异演化）
- 影响评估（评估风险影响范围）

---

## 4.6 AI Model Repository

负责 AI 模型资产管理。

模型类型：

| 模型类型 | 适用场景 |
|----------|---------|
| CNN 分类模型 | 恶意应用分类 |
| LSTM 序列模型 | 行为序列分析 |
| GNN 图模型 | 关联分析/家族识别 |
| BERT 文本模型 | 内容/语义分析 |
| ResNet 图像模型 | 图标/图像分析 |
| XGBoost 评分模型 | 风险评分 |
| LLM 辅助模型 | 辅助研判/报告生成 |

模型管理能力：

```text
Model Training

↓

Model Evaluation (accuracy / recall / F1)

↓

Model Review

↓

Model Registration

↓

Model Deployment (A/B Testing / Gray / Full)

↓

Model Monitoring (drift / performance)

↓

Model Retraining / Update
```

- 模型版本管理
- 模型效果评估
- 模型灰度发布
- 模型漂移监控
- 模型自动重训练

---

## 4.7 Knowledge Management

负责安全知识的统一管理和输出。

知识类型：

| 知识类型 | 说明 |
|----------|------|
| Malware Encyclopedia | 恶意软件百科（家族、行为、处置方案） |
| Privacy Knowledge | 隐私合规知识（法规、标准、最佳实践） |
| Fraud Knowledge | 涉诈知识（诈骗模式、特征、案例） |
| SDK Knowledge | SDK 知识（功能、行为、风险评估） |
| Threat Intelligence | 威胁情报（APT、漏洞、趋势） |
| Compliance Knowledge | 合规知识（法规、政策、要求） |

---

# 5. Knowledge Flow

知识从产生到应用的完整流程：

```text
Security Operation (Confirmed Cases)

↓

Knowledge Extraction

┌───────┼───────┬───────┬───────┐

│       │       │       │       │

Sample  Feature  IOC   Rule   Training

Update  Update  Update  Create  Data

│       │       │       │       │

└───────┴───────┴───────┴───────┘

↓

Knowledge Verification

↓

Knowledge Release

↓

Detection Capability Enhancement

↓

Detection Service Layer
```

---

# 6. Key Technologies

| 技术领域 | 关键技术 | 说明 |
|----------|---------|------|
| 样本存储 | MinIO / HDFS / 对象存储 | 样本去重、压缩、加密存储 |
| 特征提取 | YARA / SSDeep / 自研特征引擎 | 多类型特征自动化提取 |
| IOC 管理 | MISP / STIX 2.1 / OpenIOC | IOC 标准化、关联、共享 |
| 规则引擎 | YARA Engine / Drools / 自研 | 规则创建、测试、发布、监控 |
| 知识图谱 | Neo4j / JanusGraph / NebulaGraph | 实体关系建模、图查询、图算法 |
| 图算法 | PageRank / Community Detection / Shortest Path | 关联分析、社区发现、传播分析 |
| 模型训练 | PyTorch / TensorFlow / scikit-learn | 模型训练、评估、部署 |
| 模型管理 | MLflow / Model Registry / DVC | 模型版本、效果追踪、灰度发布 |
| 特征存储 | Feast / Redis / HBase | 在线/离线特征存储和服务 |
| 向量检索 | Milvus / FAISS / Elasticsearch | 向量相似度检索（代码/图标相似） |
| 情报对接 | MISP API / VirusTotal API / OTX | 外部威胁情报接入 |
| 知识管理 | Wiki / Confluence / 自研 | 安全知识文档管理和协作 |

---

# 7. Technical Metrics

## 7.1 知识资产指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 恶意样本库规模 | ≥ 10M | 积累的恶意应用样本数量 |
| 正常样本库规模 | ≥ 5M | 积累的正常应用样本数量 |
| 检测特征数量 | ≥ 500K | 可用检测特征/规则数量 |
| YARA 规则数量 | ≥ 10K | YARA 检测规则数量 |
| IOC 指标数量 | ≥ 1M | 威胁指标数量 |
| 知识图谱实体数 | ≥ 10M | 图谱中的实体数量 |
| 知识图谱关系数 | ≥ 50M | 图谱中的关系数量 |
| SDK 知识覆盖 | ≥ 5,000 | 可识别的第三方 SDK 数量 |
| AI 模型数量 | ≥ 20 | 各检测服务的 ML 模型数量 |

## 7.2 知识质量指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 特征准确率 | ≥ 99% | 检测特征的有效率 |
| 规则准确率 | ≥ 98% | 检测规则的有效率 |
| IOC 时效性 | ≥ 90% | IOC 在有效期内的比例 |
| 知识图谱准确率 | ≥ 95% | 图谱关系准确度 |
| 模型 AUC | ≥ 0.95 | AI 模型 ROC 曲线下面积 |

## 7.3 知识运营指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 知识更新延迟 | ≤ 24 hours | 从运营确认到知识生效的时间 |
| 规则发布周期 | ≤ 48 hours | 新规则从创建到发布的时间 |
| 模型迭代周期 | ≤ 2 weeks | 模型更新迭代周期 |
| 特征提取自动化率 | ≥ 90% | 自动化特征提取的比例 |
| 知识反馈率 | ≥ 80% | 运营案件结论沉淀为知识的比例 |

## 7.4 服务性能指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 特征查询延迟 | ≤ 100 ms | 在线特征查询响应时间 |
| 规则匹配延迟 | ≤ 10 sec | 单应用规则匹配耗时 |
| 图谱查询延迟 | ≤ 3 sec | 三度关联查询响应时间 |
| 模型推理延迟 | ≤ 60 sec | 单应用模型推理耗时 |
| 向量检索延迟 | ≤ 500 ms | 相似度检索响应时间 |

---

# 8. Inputs and Outputs

## Input

来自 Security Operation Platform：
- Confirmed Case（已确认的安全案件）
- Operation Feedback（运营反馈）
- Expert Knowledge（专家知识）

来自外部：
- Threat Intelligence（威胁情报）
- Open Source Intelligence（开源情报）
- Regulatory Requirements（合规要求）

## Output

供 Detection Service Layer：
- Detection Rules
- Feature Library
- IOC Library
- AI Models
- Knowledge Graph
- Risk Profiles

供 Analysis Engine Layer：
- SDK Knowledge
- Program Patterns
- Deobfuscation Rules

供 Security Operation Platform：
- Similar Cases
- Risk Assessment
- Expert Knowledge

---

# 9. Design Principles

## Knowledge-Driven

检测能力来源于知识积累。

## Automated Extraction

尽可能自动化知识提取，减少人工成本。

## Verification Before Release

所有知识发布前必须经过验证。

## Lifecycle Management

知识有生命周期，需要持续更新和淘汰。

## Shared Intelligence

知识在平台内共享，避免信息孤岛。

---

# 10. Summary

Security Intelligence & Knowledge Platform 是移动应用安全检测平台的知识资产中心和检测能力演进引擎。

它通过 Sample Management、Feature Library、IOC Library、Rule Library、Knowledge Graph、AI Model Repository 和 Knowledge Management 七个核心模块，实现安全知识的沉淀、管理和输出，持续驱动检测能力演进。

关键技术涵盖样本管理（MinIO/HDFS）、特征提取（YARA/SSDeep）、IOC 管理（MISP/STIX）、规则引擎（YARA/Drools）、知识图谱（Neo4j/JanusGraph）、模型管理（MLflow/PyTorch）和向量检索（Milvus/FAISS）等。

技术指标从知识资产规模、知识质量、知识运营和服务性能四个维度定义了能力基线，确保平台具备丰富、准确、及时的安全知识资产，持续驱动检测能力演进。

Security Intelligence & Knowledge Platform 是平台能力持续演进的核心引擎，它将每一次安全运营成果转化为可复用的知识资产，形成"检测 → 运营 → 知识 → 检测增强"的完整闭环。
