# Analysis Result Management

---

# 1. Overview

Analysis Result Management（分析结果管理）负责统一管理 Analysis Engine Layer 产生的分析结果，并向 Detection Service Layer 提供标准化的分析数据接口。

由于移动应用安全检测需要同时结合静态分析和动态分析能力，不同分析引擎产生的数据结构、粒度和表达方式存在较大差异，因此需要通过统一的结果管理机制，对分析结果进行组织、关联和标准化。

Analysis Result Management 的核心目标是：

> 将不同分析引擎产生的原始分析结果，转换为统一、可消费的 Analysis Result。

Analysis Result Management 不负责安全判断，不负责风险评级，仅负责分析结果管理和能力输出。

---

# 2. Design Objectives

Analysis Result Management 的设计目标包括：

- 统一静态和动态分析结果；
- 建立标准化分析结果模型；
- 管理分析证据；
- 支持多检测服务共享分析结果；
- 提供稳定的数据接口；
- 解耦分析引擎和业务检测逻辑。

---

# 3. Position in Architecture

Analysis Result Management 位于 Analysis Engine Layer 内部。

整体关系如下：

```text
                Analysis Engine Layer

        ┌───────────────────────────┐
        │                           │
        ▼                           ▼

 Static Analysis           Dynamic Analysis

        │                           │

        └──────────────┬────────────┘

                       ▼

        Analysis Result Management

                       │

                       ▼

          Standardized Analysis Result

                       │

                       ▼

          Detection Service Layer
```

---

# 4. Core Responsibilities

## 4.1 Result Standardization

将不同分析引擎的原始结果转换为统一数据模型。

包括：
- 数据模型映射
- 字段标准化
- 类型统一
- 编码规范

## 4.2 Evidence Correlation

关联静态证据和动态证据。

包括：
- 静态证据 → 动态行为关联
- API 调用 → 网络请求关联
- 权限声明 → 实际使用关联
- SDK 声明 → SDK 行为关联

## 4.3 Result Organization

对分析结果进行结构化组织。

包括：
- 按应用组织
- 按模块组织
- 按证据类型组织
- 按时间线组织

## 4.4 Result Storage

分析结果持久化存储。

包括：
- 结构化数据存储（Elasticsearch/MongoDB）
- 非结构化数据存储（对象存储）
- 索引构建
- 数据生命周期管理

## 4.5 Result Query

提供统一的查询接口。

包括：
- 按应用查询
- 按证据类型查询
- 按行为模式查询
- 跨应用关联查询

---

# 5. Standardized Analysis Result Model

统一分析结果数据模型：

```text
Standardized Analysis Result

├── Application Info
│   ├── Package Name
│   ├── Version Name
│   ├── Version Code
│   ├── SHA256
│   ├── Size
│   ├── Min SDK
│   ├── Target SDK
│   └── Certificate Info
│
├── Static Analysis Result
│   ├── Application Structure
│   │   ├── Activities
│   │   ├── Services
│   │   ├── Receivers
│   │   ├── Providers
│   │   └── Permissions
│   ├── Program Structure
│   │   ├── Classes
│   │   ├── Methods
│   │   ├── Fields
│   │   └── Call Graph
│   ├── Dependencies
│   │   ├── SDK List
│   │   ├── Library List
│   │   └── Native Libraries
│   ├── Code Logic
│   │   ├── Data Flow
│   │   ├── Control Flow
│   │   └── ICC Paths
│   └── Static Evidence
│       ├── Permission Usage
│       ├── API Calls
│       ├── URLs
│       ├── IPs
│       ├── Domains
│       ├── Strings
│       ├── SDK Signatures
│       ├── Obfuscation Info
│       └── Packing Info
│
├── Dynamic Analysis Result
│   ├── Runtime Events
│   │   ├── System Calls
│   │   ├── API Calls
│   │   ├── File Operations
│   │   ├── Network Requests
│   │   ├── IPC Calls
│   │   └── Permission Usage
│   ├── Behavior Information
│   │   ├── Data Access Behavior
│   │   ├── Network Behavior
│   │   ├── File Behavior
│   │   ├── SDK Runtime Behavior
│   │   └── UI Interaction Behavior
│   └── Dynamic Evidence
│       ├── Network Targets
│       ├── Data Exfiltration
│       ├── Dynamic Loading
│       ├── Privilege Escalation
│       └── Anti-Analysis Behavior
│
└── Correlation
    ├── Static-Dynamic Correlation
    ├── Permission-Usage Correlation
    ├── SDK Declaration-Behavior Correlation
    └── Data Flow Correlation
```

---

# 6. Evidence Correlation Model

静态证据与动态证据的关联模型：

```text
Static Evidence                Dynamic Evidence

Permission Declaration  ←→  Permission Usage

SDK Declaration         ←→  SDK Runtime Behavior

API Declaration         ←→  API Call

URL in Code             ←→  Network Request

Data Source in Code     ←→  Data Access Behavior

Component Declaration  ←→  Component Lifecycle
```

关联规则：

| Static | Dynamic | Correlation Type |
|--------|---------|-----------------|
| 声明 android.permission.READ_CONTACTS | 调用 ContactsContract API | 权限-使用关联 |
| 引用 com.google.ads SDK | 运行时请求广告网络 | SDK 声明-行为关联 |
| 代码中包含 https://evil.com | 运行时连接 evil.com:443 | URL-网络关联 |
| 声明 android.permission.ACCESS_FINE_LOCATION | 调用 LocationManager.getLastKnownLocation() | 权限-行为关联 |

---

# 7. Key Technologies

| 技术领域 | 关键技术 | 说明 |
|----------|---------|------|
| 数据标准化 | JSON Schema / Protocol Buffers | 统一分析结果数据模型与序列化 |
| 证据关联 | Graph Database / Entity Resolution | 静态与动态证据的关联分析 |
| 结构化存储 | Elasticsearch / MongoDB | 分析结果全文检索与文档存储 |
| 对象存储 | MinIO / S3 | 应用包、PCAP 文件等非结构化数据存储 |
| 索引引擎 | Elasticsearch / Lucene | 多字段索引、全文检索、聚合分析 |
| 查询接口 | GraphQL / REST API | 统一分析结果查询接口 |
| 数据血缘 | Apache Atlas / DataHub | 分析结果血缘追踪与影响分析 |

---

# 8. Technical Metrics

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 结果标准化率 | 100% | 所有分析结果符合统一数据模型 |
| 证据关联完整率 | ≥ 90% | 静态证据与动态证据可关联的比例 |
| 结果存储可靠性 | 100% | 分析结果不丢失 |
| 结果查询延迟 | ≤ 500 ms | 典型查询响应时间 |
| 跨应用关联查询延迟 | ≤ 3 sec | 跨应用证据关联查询响应 |
| 并发查询能力 | ≥ 100 QPS | 同时支持的查询请求数 |
| 数据模型版本兼容 | 100% | 新旧版本数据模型向前兼容 |

---

# 9. Design Principles

## Standardized Output

所有分析结果必须符合统一数据模型。

## Evidence Correlation

静态证据与动态证据必须可关联。

## Immutable Result

分析结果一旦写入不可修改，保证可追溯。

## Decoupling

分析结果模型与具体检测业务解耦。

## Extensibility

支持新增分析结果类型，无需修改整体模型。

---

# 10. Summary

Analysis Result Management 是 Analysis Engine Layer 的数据统一层。

它通过 Result Standardization、Evidence Correlation、Result Organization、Result Storage 和 Result Query 五个核心能力，将静态分析和动态分析的原始结果转换为统一、可消费的标准化分析结果。

关键技术涵盖数据标准化（JSON Schema/Protobuf）、证据关联（Graph Database）、结构化存储（Elasticsearch/MongoDB）和查询接口（GraphQL/REST）等。

技术指标从标准化率、关联完整率、存储可靠性和查询性能等维度定义了能力基线，确保分析结果管理具备统一、完整、高效的数据服务能力。
