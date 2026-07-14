# Chapter 07 Security Big Data Platform

---

# 1. Overview

Security Big Data Platform（安全大数据平台）是移动应用安全检测平台的横向数据能力平台，负责安全相关数据的采集、治理、分析和安全态势感知。

Security Big Data Platform 不负责安全检测，也不负责安全运营，而是作为平台的数据基座，汇聚多源异构安全数据，为 Security Operation Platform 提供风险信号，为 Security Intelligence & Knowledge Platform 提供数据基础。

平台核心职责：

- 安全数据采集（多源、实时、批量）；
- 数据治理（质量、标准化、血缘）；
- 数据分析（多维、实时、离线）；
- 安全态势感知（宏观、微观、趋势）。

---

# 2. Design Objectives

Security Big Data Platform 的设计目标包括：

- 建立统一的安全数据采集体系；
- 实现多源异构数据标准化治理；
- 构建安全数据分析能力；
- 提供安全态势感知能力；
- 为安全运营提供风险信号；
- 为知识平台提供数据基础；
- 支持实时和离线双模分析。

---

# 3. Position in Overall Architecture

Security Big Data Platform 是三大横向能力平台之一。

```text
+--------------------------------------------------------------+
|             Security Big Data Platform                        |
| Data Collection | Governance | Analytics | Situation Awareness|
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
+--------------------------------------------------------------+
                                      │
                                      ▼
                             Detection Capability Evolution
```

Security Big Data Platform 位于数据链的起点，为安全运营和知识平台提供数据支撑。

---

# 4. Core Modules

```text
Security Big Data Platform

├── Data Collection
├── Data Governance
├── Data Analytics
└── Situation Awareness
```

## 4.1 Data Collection

负责多源异构安全数据的采集。

数据来源包括：

| 数据源 | 数据类型 | 采集方式 |
|--------|---------|---------|
| 应用元数据 | 包名、版本、签名、开发者 | 实时同步 |
| 检测结果 | 恶意/隐私/涉诈/广告检测结果 | 事件推送 |
| 运行态数据 | 应用运行行为、网络流量 | 批量导入 |
| 用户反馈 | 举报、投诉、评价 | 实时采集 |
| 互联网舆情 | 新闻、论坛、社交媒体 | 爬虫采集 |
| 威胁情报 | IOC、恶意域名、C2 地址 | STIX/TAXII |
| 监管通报 | 合规要求、处罚公告 | 定期同步 |
| SDK 信息 | SDK 版本、行为、风险 | 批量导入 |

采集技术：
- 实时流式采集（Kafka / Flume）
- 批量导入（Spark / Sqoop）
- API 数据拉取（REST / GraphQL）
- 日志采集（Filebeat / Logstash）
- Web 爬虫（Scrapy / 自研）

---

## 4.2 Data Governance

负责数据质量管控和标准化。

主要能力包括：

- 数据质量检测（完整性、准确性、一致性、时效性）
- 数据标准化（统一数据模型、编码规范、分类体系）
- 元数据管理（数据资产目录、字段定义、血缘关系）
- 数据生命周期管理（采集、存储、归档、销毁）
- 数据安全管理（脱敏、加密、访问控制）

数据治理模型：

```text
Raw Data

↓

Data Quality Check

↓

Data Standardization

↓

Data Classification

↓

Governed Data
```

---

## 4.3 Data Analytics

负责安全数据的分析计算。

分析能力包括：

### 4.3.1 实时分析

- 实时风险指标计算
- 异常行为实时检测
- 实时告警生成
- 流式聚合统计

技术栈：Flink / Spark Streaming / Kafka Streams

### 4.3.2 离线分析

- 历史数据趋势分析
- 风险模型训练数据准备
- 大规模关联分析
- 统计报表生成

技术栈：Spark / Hive / Presto / ClickHouse

### 4.3.3 交互式分析

- 安全分析师即席查询
- 多维钻取分析
- 可视化探索

技术栈：ClickHouse / Doris / Elasticsearch

### 4.3.4 图分析

- 实体关联分析
- 社区发现
- 传播路径分析
- 异常子图检测

技术栈：Neo4j / JanusGraph / GraphX

---

## 4.4 Situation Awareness

负责安全态势感知和可视化。

主要能力包括：

- 安全态势大盘（宏观风险概览）
- 风险趋势分析（时间维度趋势）
- 应用风险画像（单应用风险视图）
- 生态健康度评估（整体生态安全评分）
- 监管合规态势（合规风险地图）
- 威胁情报态势（威胁活动地图）

输出：

```text
Risk Signal
```

Risk Signal 是安全运营的输入，而非最终风险结论。

---

# 5. Data Model

### 5.1 Application Profile

```text
Application Profile

├── Package Name
├── Version
├── SHA256
├── Developer
├── Certificate
├── Category
├── Upload Time
├── Download Count
├── Risk Score
└── Detection History
```

### 5.2 Risk Signal

```text
Risk Signal

├── Signal ID
├── Signal Type
├── Source
├── Application
├── Risk Category
├── Confidence
├── Timestamp
└── Raw Data Reference
```

### 5.3 Security Event

```text
Security Event

├── Event ID
├── Event Type
├── Application
├── Timestamp
├── Severity
├── Description
└── Related Entities
```

---

# 6. Key Technologies

| 技术领域 | 关键技术 | 说明 |
|----------|---------|------|
| 实时采集 | Kafka / Flume / Logstash | 多源数据实时流式采集 |
| 批量采集 | Spark / Sqoop / DataX | 历史数据和批量数据导入 |
| 流式计算 | Flink / Spark Streaming | 实时风险指标计算和异常检测 |
| 离线计算 | Spark / Hive | 大规模离线分析和报表 |
| 交互式查询 | ClickHouse / Doris / Presto | 安全分析师即席查询 |
| 图数据库 | Neo4j / JanusGraph | 实体关联、社区发现、传播分析 |
| 搜索引擎 | Elasticsearch | 全文检索、日志分析 |
| 时序数据库 | InfluxDB / TimescaleDB | 指标存储、趋势分析 |
| 对象存储 | MinIO / HDFS | 原始数据和应用包存储 |
| 可视化 | Grafana / Kibana / 自研 | 态势大盘、分析看板 |
| 数据质量 | Great Expectations / Deequ | 数据质量检测和监控 |
| 元数据管理 | Apache Atlas / DataHub | 数据资产目录和血缘管理 |
| 爬虫 | Scrapy / Selenium | 互联网舆情和公开数据采集 |
| 威胁情报对接 | MISP / STIX/TAXII | 威胁情报标准化接入 |

---

# 7. Technical Metrics

## 7.1 数据采集指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 实时数据采集延迟 | ≤ 5 sec | 从数据产生到入库的延迟 |
| 批量数据导入吞吐 | ≥ 1M records/min | 批量数据导入处理速度 |
| 数据源覆盖 | ≥ 10 类 | 支持的数据源类型数量 |
| 采集可用率 | ≥ 99.9% | 采集服务可用时间占比 |

## 7.2 数据治理指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 数据标准化率 | ≥ 99% | 入库数据符合标准模型的比例 |
| 数据质量达标率 | ≥ 98% | 数据质量检测通过率 |
| 元数据覆盖率 | 100% | 所有数据资产均有元数据 |
| 数据血缘追踪率 | 100% | 所有数据可追踪来源 |

## 7.3 数据分析指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 实时分析延迟 | ≤ 10 sec | 实时流式计算延迟 |
| 离线分析吞吐 | ≥ 10 TB/day | 离线分析日处理数据量 |
| 交互式查询延迟 | ≤ 5 sec | 典型分析查询响应时间 |
| 图查询延迟 | ≤ 3 sec | 三度关联查询响应时间 |

## 7.4 态势感知指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 态势更新频率 | ≤ 5 min | 态势大盘数据刷新周期 |
| 风险信号产出延迟 | ≤ 15 min | 从数据入库到风险信号产出 |
| 告警准确率 | ≥ 90% | 态势告警的有效性 |

---

# 8. Data Flow

Security Big Data Platform 的主要数据流如下：

```text
External Data Sources

↓

Data Collection

↓

Data Governance

↓ ┌──────────────┐
  │ Governed Data │
↓ └──────────────┘

┌─────────────┬─────────────┐

│             │             │

Real-time    Offline      Interactive

Analytics    Analytics    Analytics

│             │             │

└─────────────┴─────────────┘

↓

Situation Awareness

↓

Risk Signal → Security Operation Platform

Data Asset → Security Intelligence & Knowledge Platform
```

---

# 9. Inputs and Outputs

## Input

- Application Metadata
- Detection Results
- Runtime Behavior Data
- User Feedback
- Internet Public Opinion
- Threat Intelligence
- Regulatory Information
- SDK Information

## Output

- Risk Signal（→ Security Operation Platform）
- Governed Data Asset（→ Security Intelligence & Knowledge Platform）
- Security Statistics（→ Portal / Report）
- Situation Awareness（→ Management Dashboard）

---

# 10. Design Principles

## Data-Driven

所有分析和判断基于数据。

## Real-time First

优先保障实时数据采集和分析能力。

## Data Quality

数据质量是分析有效性的前提。

## Unified Model

所有数据遵循统一数据模型。

## Scalability

支持数据量和数据源的持续扩展。

---

# 11. Summary

Security Big Data Platform 是移动应用安全检测平台的数据基座。

它通过 Data Collection、Data Governance、Data Analytics 和 Situation Awareness 四个核心模块，实现安全数据的采集、治理、分析和态势感知，为安全运营提供风险信号，为知识平台提供数据基础。

关键技术涵盖流式计算（Flink）、离线分析（Spark）、交互式查询（ClickHouse）、图数据库（Neo4j）、搜索引擎（Elasticsearch）和可视化（Grafana）等。

技术指标从数据采集、治理、分析和态势感知四个维度定义了能力基线，确保平台具备实时、高效、可信的安全大数据处理能力。
