---
title: Design Principles
chapter: 2
part: Platform Overview
layer: Platform Overview
status: Released
version: 1.0
author: MASAP Architecture Team
last_updated: 2026-07
---

# 第2章 设计原则（Design Principles）

> **Chapter 2**
>
> Design Principles

---

# 1. 本章目标

平台架构设计决定了系统未来数年的可扩展性、可维护性以及持续演进能力。

本章从总体架构层面对 MASAP 的设计理念进行说明，明确平台建设过程中遵循的核心原则，并给出非功能需求（Non-functional Requirements，NFR）及关键技术指标（KPI），为后续所有章节提供统一的设计约束。

阅读本章后，读者应理解：

- 平台为什么采用六层架构；
- 每一层职责如何划分；
- 平台遵循哪些核心架构原则；
- 平台需要满足哪些非功能需求；
- 后续章节的设计依据是什么。

---

# 2. Architecture Decision（架构决策）

MASAP 在设计初期对多种架构进行了评估，包括：

- 单体检测平台（Monolithic）
- 按检测能力拆分（Static/Dynamic/AI）
- 微服务检测平台（Microservice）
- 分层平台架构（Layered Architecture）

最终选择**分层架构（Layered Architecture）**作为平台总体架构。

原因如下：

1. **职责边界清晰**

分析、AI、检测、知识平台、开放接口各自独立演进。

2. **能力复用**

静态分析结果可以同时服务于隐私检测、恶意软件检测、广告检测等多个业务。

3. **扩展性**

新增检测能力无需修改分析引擎，只需新增 Detection Service。

4. **可替换性**

AI 模型、规则引擎、知识平台均可独立升级。

5. **工程实践成熟**

Layered Architecture 已广泛应用于大型安全平台及云原生平台。

---

# 3. 六层架构设计原则

MASAP 采用六层架构。

```text
┌────────────────────────────────────┐
│      Application Access Layer      │
├────────────────────────────────────┤
│      Detection Service Layer       │
├────────────────────────────────────┤
│      AI Intelligence Layer         │
├────────────────────────────────────┤
│      Analysis Engine Layer         │
├────────────────────────────────────┤
│      Infrastructure Layer          │
├────────────────────────────────────┤
│ Security Knowledge Platform        │
└────────────────────────────────────┘
```

各层之间采用**单向依赖**：

- 上层依赖下层；
- 下层不依赖上层；
- 相邻层通过标准接口通信；
- 禁止跨层直接调用。

这样可以降低模块耦合，提高系统可维护性。

---

# 4. 核心设计原则

## 4.1 Layered Architecture

所有能力必须归属于某一层。

禁止跨层实现业务逻辑。

例如：

Detection Service 不允许直接解析 APK。

Program Analysis 不允许直接调用 Portal。

---

## 4.2 Analyze Once, Use Everywhere

程序分析只执行一次。

生成统一 Security Facts。

所有检测服务共享分析结果。

避免重复计算。

---

## 4.3 AI Native

AI 是平台基础能力。

不是外挂。

AI 可参与：

- Program Understanding
- Page Understanding
- Behavior Understanding
- Risk Reasoning
- Evidence Generation

---

## 4.4 Knowledge Driven

所有检测结果均沉淀至 Security Knowledge Platform。

知识可持续积累。

支持：

- 持续学习；
- 样本关联；
- AI训练；
- 风险画像。

---

## 4.5 Detection as a Service

所有检测能力均服务化。

统一通过：

- REST API
- Workflow
- SDK
- Webhook

向外提供。

---

## 4.6 Cloud Native

平台支持：

- Kubernetes
- Docker
- Service Mesh
- Auto Scaling
- Distributed Scheduling

---

## 4.7 Plugin Architecture

新增检测能力无需修改平台核心。

检测器通过插件注册。

例如：

```
Malware Plugin

Privacy Plugin

Fraud Plugin

Ad Plugin

...
```

---

# 5. 非功能需求（NFR）

平台应满足以下非功能需求：

| 项目 | 指标 |
|------|------|
| 可扩展性 | 支持新增检测插件，无需修改核心框架 |
| 可维护性 | 微服务独立部署 |
| 可观测性 | 全链路日志、Metrics、Tracing |
| 可用性 | ≥99.9% |
| 安全性 | RBAC、多租户、审计日志 |
| 性能 | 百万级应用检测能力 |
| 自动恢复 | 支持失败重试与节点迁移 |

---

# 6. 平台 KPI

建议平台总体能力达到以下目标：

| 指标 | 目标值 |
|------|--------|
| APK 解析成功率 | ≥99.9% |
| 静态分析成功率 | ≥99% |
| 动态运行成功率 | ≥98% |
| AI 页面识别准确率 | ≥95% |
| 恶意软件检测召回率 | ≥95% |
| 隐私违规误报率 | ≤3% |
| 单应用平均检测时间 | ≤15分钟 |
| 每日检测能力 | ≥100万应用 |
| 平均报告生成时间 | ≤30秒 |

---

# 7. 本章总结

本章明确了 MASAP 的总体设计原则、架构决策及非功能需求。

后续所有模块均需遵循本章提出的设计约束，确保平台在功能扩展、性能优化及长期演进过程中保持统一的架构风格。

---

## 下一章

**第3章：平台总体架构（Overall Architecture）**

下一章将详细介绍平台六层总体架构，包括各层职责、数据流、核心组件以及跨层协作关系。
