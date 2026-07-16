# Chapter 02 Overall Architecture Design

---

# 1. Overview

移动应用安全检测与生态风险治理平台采用**四层检测能力链（Detection Capability Layers）**与**三大横向能力平台（Horizontal Capability Platforms）**相结合的总体架构。

其中：

- **检测能力链**负责完成应用从接入、分析、检测到结果输出的实时检测流程。
- **横向能力平台**负责安全数据汇聚、安全运营闭环以及知识沉淀，持续增强平台检测能力。

整个架构遵循：

> **Detection → Operation → Intelligence → Continuous Evolution**

的设计理念，实现平台能力持续演进。

---

# 2. Architecture Overview

整体架构如下：

```text
                                +------------------------------------------------------+
                                |          Layer 4  Application Access Layer           |
                                |------------------------------------------------------|
                                | Portal | OpenAPI | Workflow | Report | Integration   |
                                +------------------------------------------------------+
                                                   ▲
                                                   │
                                +------------------------------------------------------+
                                |          Layer 3  Detection Service Layer            |
                                |------------------------------------------------------|
                                | Malware | Privacy | Fraud | Ad | Content | SDK ...  |
                                +------------------------------------------------------+
                                                   ▲
                                                   │
                                +------------------------------------------------------+
                                |          Layer 2  Analysis Engine Layer              |
                                |------------------------------------------------------|
                                | Static Analysis | Dynamic Analysis | Result Manager  |
                                +------------------------------------------------------+
                                                   ▲
                                                   │
                                +------------------------------------------------------+
                                |          Layer 1  Execution Environment Layer               |
                                |------------------------------------------------------|
                                | Device Farm | Sandbox | Execution Environment Management|
                                +------------------------------------------------------+



========================================================================================



                +--------------------------------------------------------------+
                |             Security Big Data Platform                        |
                |--------------------------------------------------------------|
                | Data Collection | Governance | Analytics | Situation Awareness|
                +--------------------------------------------------------------+
                                      │
                                      ▼
                +--------------------------------------------------------------+
                |             Security Operation Platform                       |
                |--------------------------------------------------------------|
                | Risk Discovery | Case | Auto Analysis | Human Review | Action|
                +--------------------------------------------------------------+
                                      │
                                      ▼
                +--------------------------------------------------------------+
                |     Security Intelligence & Knowledge Platform               |
                |--------------------------------------------------------------|
                | Sample | Feature | IOC | Rule | Graph | Model | Knowledge    |
                +--------------------------------------------------------------+
                                      │
                                      ▼
                             Detection Capability Evolution
```

---

# 3. Design Philosophy

整体架构遵循以下设计理念。

## 3.1 Layered Architecture

检测流程按照职责进行分层。

每一层仅负责自身能力，通过标准接口向上一层提供服务。

各层之间保持：

- 高内聚（High Cohesion）
- 低耦合（Loose Coupling）

任何一层的实现变化，不应影响其它层的能力边界。

---

## 3.2 Separation of Concerns

平台严格区分：

- 应用运行环境；
- 程序分析；
- 风险检测；
- 业务接入；
- 安全运营；
- 安全知识。

避免不同能力之间职责交叉。

---

## 3.3 Capability Reuse

分析能力不属于任何检测业务。

Analysis Engine 只负责分析应用行为。

Malware、Privacy、Fraud 等检测服务共享同一套分析能力。

一次分析，多业务复用。

---

## 3.4 Continuous Evolution

平台能力不是静态的。

所有检测能力均来源于：

- 安全运营；
- 样本积累；
- 风险分析；
- AI 学习；
- 专家经验。

实现持续演进。

---

# 4. Detection Capability Layers

检测能力链负责完成实时安全检测。

整体流程如下：

```text
Application

↓

Execution Environment Layer

↓

Analysis Engine

↓

Detection Service

↓

Application Access

↓

Security Report
```

---

## 4.1 Layer 1 Execution Environment Layer

### Responsibilities

负责提供应用运行所需的执行资源。

包括：

- Device Farm
- Sandbox
- Execution Environment Management

输出：

```text
Execution Environment
```

自身不负责安全分析。

---

## 4.2 Layer 2 Analysis Engine Layer

### Responsibilities

负责理解应用程序。

回答：

> 应用是什么？

以及：

> 应用做了什么？

输出统一分析结果。

包括：

- Static Analysis
- Dynamic Analysis
- Analysis Result Management

Analysis Engine 不负责风险判定。

---

## 4.3 Layer 3 Detection Service Layer

### Responsibilities

负责业务安全检测。

输入：

- Analysis Result
- Security Knowledge
- Detection Rules
- AI Models

输出：

```text
Risk Decision
```

包括：

- Malware Detection
- Privacy Detection
- Fraud Detection
- Malicious Advertisement Detection
- Content Compliance Detection
- Impersonation Detection

---

## 4.4 Layer 4 Application Access Layer

负责向业务开放检测能力。

包括：

- Portal
- Workflow
- OpenAPI
- Report
- Integration

服务对象包括：

- App Store
- Internal Review System
- Security Platform
- Third-party Systems

---

# 5. Horizontal Capability Platforms

横向平台不属于检测链。

它们共同支撑检测能力持续增强。

---

## 5.1 Security Big Data Platform

### Responsibilities

负责安全数据采集、治理、分析和安全态势感知。

数据来源包括：

- 应用元数据
- 审核记录
- 运行态数据
- 用户反馈
- 举报投诉
- 舆情信息
- 威胁情报
- 监管通报

输出：

```text
Risk Signal
```

Risk Signal 是安全运营的输入，而不是最终风险结论。

---

## 5.2 Security Operation Platform

### Responsibilities

负责风险分析、人工研判和风险处置。

平台建立完整安全运营闭环：

```text
Risk Signal

↓

Case Creation

↓

Automated Analysis

↓

Human Review

↓

Risk Decision

↓

Response

↓

Knowledge Feedback
```

典型处置包括：

- 下架
- 整改
- 限流
- 持续观察
- 人工复审

Security Operation Platform 是平台持续治理能力的核心。

---

## 5.3 Security Intelligence & Knowledge Platform

### Responsibilities

负责沉淀安全知识资产。

包括：

- Malware Samples
- Feature Library
- IOC Library
- Rule Library
- Knowledge Graph
- AI Assets
- Model Repository

输出：

```text
Detection Capability
```

供：

- Detection Service
- Analysis Engine
- Security Operation

共同使用。

---

# 6. Data Flow

平台主要存在两条数据流。

## 6.1 Detection Flow

实时检测流程。

```text
Application

↓

Infrastructure

↓

Analysis Engine

↓

Detection Service

↓

Detection Report
```

---

## 6.2 Governance Flow

持续治理流程。

```text
Runtime Data

+
User Feedback

+
Threat Intelligence

↓

Security Big Data

↓

Security Operation

↓

Knowledge Platform

↓

Detection Enhancement
```

---

# 7. Capability Boundary

平台各模块职责边界如下。

| Module | Responsibility | Output |
|---------|----------------|--------|
| Execution Environment Layer | 提供检测运行环境 | Execution Environment |
| Analysis Engine | 程序分析与行为分析 | Analysis Result |
| Detection Service | 安全风险判定 | Risk Decision |
| Application Access | 能力开放与任务管理 | Detection Service |
| Security Big Data | 风险数据发现 | Risk Signal |
| Security Operation | 风险分析与处置 | Confirmed Case |
| Security Intelligence & Knowledge | 知识沉淀与能力增强 | Rules / Features / Models |

---

# 8. Continuous Evolution

平台能力持续来源于安全运营。

完整能力演进闭环如下：

```text
Application Detection

↓

Risk Discovery

↓

Security Operation

↓

Expert Analysis

↓

Knowledge Extraction

↓

Rule / Feature / Model

↓

Detection Capability Enhancement

↓

Application Detection
```

每一次风险分析和运营结果都会沉淀为新的安全知识，持续提升平台检测能力。

---

# 9. Key Technology Map

平台各层关键技术体系总览。

## 9.1 检测能力链关键技术

| Layer | 关键技术 | 核心工具 |
|-------|---------|---------|
| Infrastructure | 设备虚拟化 | QEMU/KVM, Android Emulator, Redroid |
| Infrastructure | 设备管控 | STF, ADB, Scrcpy |
| Infrastructure | 网络隔离 | Network Namespace, TC, Mitmproxy |
| Infrastructure | 环境快照 | QEMU Snapshot, Docker Checkpoint |
| Infrastructure | 运行时注入 | Frida, Xposed, Magisk |
| Analysis Engine | APK 解包 | Apktool, AAPT2 |
| Analysis Engine | 反编译 | JADX, JEB, Ghidra |
| Analysis Engine | 程序分析 | Soot, WALA, Jimple IR |
| Analysis Engine | 数据流分析 | Taint Analysis, Data Flow Analysis |
| Analysis Engine | 代码相似性 | SSDeep, TLSH, SimHash, BinDiff |
| Analysis Engine | 运行时监控 | Frida, Strace, System Call Tracing |
| Analysis Engine | 网络分析 | Mitmproxy, tcpdump, PCAP |
| Analysis Engine | AI 分析 | CNN, LSTM, GNN, Transformer |
| Detection Service | 特征匹配 | YARA, ClamAV |
| Detection Service | 恶意分类 | CNN, LSTM, XGBoost, GNN |
| Detection Service | 污点追踪 | Static/Dynamic Taint Tracking |
| Detection Service | 隐私策略 | Rule Engine, Policy Evaluation |
| Detection Service | NLP | BERT, Text Classification, NER |
| Detection Service | 图像识别 | ResNet, EfficientNet, YOLO, OCR |
| Detection Service | 感知哈希 | pHash, dHash, SIFT, Siamese Network |
| Application Access | API 网关 | Kong, APISIX |
| Application Access | 工作流引擎 | Camunda, Temporal |
| Application Access | 消息队列 | Kafka, RabbitMQ |

## 9.2 横向平台关键技术

| Platform | 关键技术 | 核心工具 |
|----------|---------|---------|
| Big Data | 流式计算 | Flink, Spark Streaming |
| Big Data | 离线计算 | Spark, Hive |
| Big Data | 交互式查询 | ClickHouse, Doris |
| Big Data | 图数据库 | Neo4j, JanusGraph |
| Big Data | 搜索引擎 | Elasticsearch |
| Big Data | 可视化 | Grafana, Kibana |
| Operation | 规则引擎 | Drools, Easy Rules |
| Operation | AI 辅助 | LLM, RAG |
| Operation | 风险评分 | XGBoost, LightGBM |
| Intelligence | 样本管理 | MinIO, HDFS |
| Intelligence | IOC 管理 | MISP, STIX 2.1 |
| Intelligence | 知识图谱 | Neo4j, JanusGraph, NebulaGraph |
| Intelligence | 模型管理 | MLflow, PyTorch, TensorFlow |
| Intelligence | 向量检索 | Milvus, FAISS |

---

# 10. Technical Metrics Overview

平台核心能力指标总览。

| 指标类别 | 核心指标 | 目标值 |
|----------|---------|--------|
| 恶意软件检测 | 已知检出率 | ≥ 99.5% |
| 恶意软件检测 | 未知检出率 | ≥ 85% |
| 恶意软件检测 | 误报率 | ≤ 0.1% |
| 隐私合规检测 | 违规检出率 | ≥ 90% |
| 涉诈风险检测 | 识别率 | ≥ 85% |
| 广告行为检测 | 违规检出率 | ≥ 90% |
| 内容安全检测 | 违规识别率 | ≥ 90% |
| 仿冒侵权检测 | 识别率 | ≥ 90% |
| SDK 风险检测 | 检出率 | ≥ 85% |
| 静态分析 | 单应用分析时间 | ≤ 3 min |
| 动态分析 | 单应用分析时间 | ≤ 15 min |
| 端到端检测 | 延迟 | ≤ 30 min |
| 日检测吞吐量 | | ≥ 5,000 apps/day |
| 平台可用性 | | ≥ 99.9% |
| 样本库规模 | | ≥ 10M |
| 检测特征数 | | ≥ 500K |
| IOC 指标数 | | ≥ 1M |

---

# 11. Chapter Navigation

后续章节将在本章架构基础上，分别展开各能力模块的详细设计。

- Chapter 03 Execution Environment Layer — 执行环境资源管理
- Chapter 04 Analysis Engine Layer — 静态分析与动态分析
- Chapter 05 Detection Service Layer — 七大检测服务
- Chapter 06 Application Access Layer — 能力开放与业务集成
- Chapter 07 Security Big Data Platform — 安全数据汇聚与分析
- Chapter 08 Security Operation Platform — 安全运营闭环
- Chapter 09 Security Intelligence & Knowledge Platform — 知识沉淀与能力演进

本章定义的架构边界、模块职责和数据流，为整份技术地图的统一技术基线。
