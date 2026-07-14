# Chapter 06 Application Access Layer

---

# 1. Overview

Application Access Layer（应用接入层）是移动应用安全检测平台的能力开放层，负责向应用商店、安全运营平台和第三方系统提供统一的检测服务入口。

Application Access Layer 不负责安全分析，也不负责风险判断，而是作为平台与外部业务系统的桥梁，将检测能力以标准化、可集成的方式开放给业务方。

Application Access Layer 主要承担以下职责：

- 检测任务管理（接收、编排、调度、监控）；
- 检测报告生成（聚合、格式化、输出）；
- 能力开放（Portal、OpenAPI、SDK）；
- 业务集成（与审核系统、运营平台对接）；
- 工作流编排（检测流程自动化）。

---

# 2. Design Objectives

Application Access Layer 的设计目标包括：

- 提供统一的检测服务入口；
- 支持多种接入方式（Portal、OpenAPI、SDK）；
- 提供灵活的检测工作流编排；
- 支持标准化检测报告输出；
- 支持与业务系统深度集成；
- 提供检测任务全生命周期管理；
- 支持高并发检测请求接入。

---

# 3. Position in Overall Architecture

Application Access Layer 位于检测能力链的顶层。

```text
┌──────────────────────────────────────────────┐
│       Application Access Layer               │
│ Portal | OpenAPI | Workflow | Report | Integ  │
└──────────────────────────────────────────────┘
                     ▲
                     │
┌──────────────────────────────────────────────┐
│         Detection Service Layer              │
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

Application Access Layer 直接面向业务系统，是外部系统使用检测能力的唯一入口。

---

# 4. Core Modules

Application Access Layer 包括五个核心模块。

```text
Application Access Layer

├── Portal
├── OpenAPI
├── Workflow Engine
├── Report Engine
└── Integration Framework
```

## 4.1 Portal

Portal 提供可视化操作界面。

主要功能包括：

- 检测任务提交；
- 检测进度查看；
- 检测报告浏览；
- 风险详情查看；
- 历史记录查询；
- 统计数据展示。

面向用户：
- 安全审核人员；
- 安全运营人员；
- 应用开发者；
- 管理人员。

---

## 4.2 OpenAPI

OpenAPI 提供标准化检测服务接口。

主要接口包括：

| API | Description |
|-----|-------------|
| SubmitDetection() | 提交检测任务 |
| QueryDetection() | 查询检测状态 |
| GetReport() | 获取检测报告 |
| GetRiskDetail() | 获取风险详情 |
| ListDetections() | 列出检测记录 |
| CancelDetection() | 取消检测任务 |
| GetStatistics() | 获取统计数据 |

接口规范：
- RESTful API
- JSON 数据格式
- OAuth 2.0 认证
- 速率限制
- 异步回调

---

## 4.3 Workflow Engine

Workflow Engine 负责检测任务编排和流程自动化。

主要能力包括：

- 检测流程定义；
- 检测步骤编排；
- 条件分支；
- 并行执行；
- 超时控制；
- 失败重试；
- 回调通知。

典型检测工作流：

```text
Application Submit

↓

Static Analysis

↓

┌─────────────┬─────────────┐

│             │             │

Malware      Privacy      Ad

Detection    Detection    Detection

│             │             │

└─────────────┴─────────────┘

↓

Risk Aggregation

↓

Report Generation

↓

Callback Notification
```

Workflow Engine 支持自定义检测流程，满足不同业务场景需求。

---

## 4.4 Report Engine

Report Engine 负责检测报告生成和管理。

主要能力包括：

- 检测结果聚合；
- 风险等级汇总；
- 证据关联输出；
- 报告模板管理；
- 多格式报告输出（PDF、HTML、JSON）；
- 报告版本管理；
- 报告归档。

报告模型：

```text
Detection Report

├── Application Info
│   ├── Package Name
│   ├── Version
│   ├── SHA256
│   ├── Size
│   └── Developer
├── Detection Summary
│   ├── Overall Risk Level
│   ├── Detection Time
│   └── Detection Duration
├── Detection Details
│   ├── Malware Detection Result
│   ├── Privacy Detection Result
│   ├── Fraud Detection Result
│   ├── Ad Detection Result
│   ├── Content Compliance Result
│   ├── Impersonation Result
│   └── SDK Risk Result
├── Risk Evidence
│   ├── Static Evidence
│   ├── Dynamic Evidence
│   └── Correlation Evidence
└── Recommendation
    ├── Action
    └── Reason
```

---

## 4.5 Integration Framework

Integration Framework 负责与外部系统对接。

主要集成场景包括：

- App Store Review System（应用审核系统）
- Security Operation Platform（安全运营平台）
- Developer Portal（开发者平台）
- CI/CD Pipeline（持续集成/部署流水线）
- Third-party Security Systems（第三方安全系统）

集成方式：
- RESTful API
- Webhook 回调
- Message Queue（Kafka/RabbitMQ）
- SDK 集成

---

# 5. Detection Task Lifecycle

检测任务从提交到完成的全生命周期如下：

```text
Submit

↓

Validate

↓

Schedule

↓

Execute (Static → Dynamic → Detection)

↓

Aggregate

↓

Report

↓

Notify

↓

Archive
```

| State | Description |
|-------|-------------|
| Submit | 业务系统提交检测请求 |
| Validate | 验证应用包和参数有效性 |
| Schedule | 调度检测任务到执行队列 |
| Execute | 执行检测流程 |
| Aggregate | 聚合检测结果 |
| Report | 生成检测报告 |
| Notify | 通知业务系统检测完成 |
| Archive | 归档检测记录和报告 |

---

# 6. Key Technologies

| 技术领域 | 关键技术 | 说明 |
|----------|---------|------|
| API Gateway | Kong / APISIX / Spring Cloud Gateway | 统一入口、认证鉴权、限流、路由 |
| 工作流引擎 | Camunda / Temporal / Apache Airflow | 检测流程编排、状态管理、回调 |
| 消息队列 | Kafka / RabbitMQ / RocketMQ | 异步任务调度、事件通知 |
| 报告生成 | JasperReports / Thymeleaf / Puppeteer | 多格式报告输出、模板渲染 |
| 认证鉴权 | OAuth 2.0 / JWT / RBAC | 统一身份认证与权限控制 |
| 任务调度 | Quartz / Celery / Distributed Scheduler | 检测任务分布式调度 |
| 缓存 | Redis / Memcached | 检测状态缓存、会话管理 |
| 对象存储 | MinIO / S3 | 应用包存储、报告文件存储 |

---

# 7. Technical Metrics

## 7.1 性能指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| API 响应延迟 | ≤ 200 ms | 非检测类 API 平均响应时间 |
| 并发请求处理 | ≥ 1,000 QPS | API Gateway 并发请求处理能力 |
| 任务提交延迟 | ≤ 500 ms | 检测任务提交到入队的延迟 |
| 报告生成时间 | ≤ 30 sec | 检测完成后报告生成耗时 |
| Webhook 通知延迟 | ≤ 5 sec | 检测完成后回调通知延迟 |

## 7.2 可靠性指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| API 可用率 | ≥ 99.95% | OpenAPI 服务可用时间占比 |
| 任务处理成功率 | ≥ 99.9% | 检测任务正常处理完成率 |
| 回调通知成功率 | ≥ 99.5% | Webhook 回调通知成功送达率 |
| 报告生成成功率 | ≥ 99.9% | 报告生成完成率 |

## 7.3 安全指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| API 认证覆盖率 | 100% | 所有接口均需认证 |
| 速率限制生效 | 100% | 所有限流策略均生效 |
| 数据传输加密 | 100% | 所有 API 通信使用 TLS |
| 审计日志覆盖 | 100% | 所有操作均记录审计日志 |

---

# 8. Inputs and Outputs

## Input

来自业务系统：
- Application Package（APK/AAB/HAP）
- Detection Configuration（检测配置）
- Detection Policy（检测策略）
- Callback Configuration（回调配置）

## Output

- Detection Report
- Detection Status
- Risk Decision
- Statistics Data

---

# 9. Design Principles

## Unified Access

所有检测能力通过统一入口访问。

## Standardized Interface

所有接口遵循统一规范。

## Asynchronous Processing

检测任务采用异步处理模式，避免阻塞业务。

## Loose Coupling

与业务系统解耦，支持独立演进。

## Extensibility

支持新增接入方式和集成场景。

---

# 10. Chapter Organization

本章节包括以下内容：

```text
06-application-access-layer/

├── README.md
├── portal.md
├── openapi.md
├── workflow.md
├── report.md
└── integration.md
```

---

# 11. Summary

Application Access Layer 是移动应用安全检测平台的能力开放与业务接入层。

它通过 Portal、OpenAPI、Workflow Engine、Report Engine 和 Integration Framework 五个核心模块，为应用商店、安全运营平台和第三方系统提供统一的检测服务入口。

关键技术涵盖 API Gateway、工作流引擎、消息队列、报告生成和认证鉴权等，确保平台具备高性能、高可靠、安全可控的能力开放能力。
