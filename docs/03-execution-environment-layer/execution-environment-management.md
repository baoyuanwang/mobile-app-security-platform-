# Execution Environment Management

---

# 1. Overview

Execution Environment Management（执行环境管理）是 Execution Environment Layer 的控制中心，负责统一管理平台中的所有执行环境资源。

它向上为 Analysis Engine Layer 提供统一的执行环境服务，向下协调 Device Farm 和 Sandbox，实现执行资源的注册、调度、分配、监控和回收。

Execution Environment Management 不负责程序分析，也不负责应用执行控制，而是负责整个执行环境资源的生命周期管理和资源编排。

---

# 2. Design Objectives

Execution Environment Management 的设计目标包括：

- 统一管理平台所有执行环境资源；
- 屏蔽真机与沙箱环境差异；
- 提供统一的资源调度能力；
- 提供标准化环境管理接口；
- 支持环境生命周期管理；
- 支持资源健康监控；
- 支持高并发任务调度；
- 为 Analysis Engine 提供统一的执行环境入口。

---

# 3. Position in Architecture

Execution Environment Management 位于 Execution Environment Layer 的中心位置。

```text
                    Analysis Engine Layer
                              │
                              ▼
                 Execution Environment API
                              │
                              ▼
          ┌──────────────────────────────────┐
          │ Execution Environment Management │
          └───────────────┬──────────────────┘
                          │
             ┌────────────┴────────────┐
             ▼                         ▼
       Device Farm                 Sandbox
```

Analysis Engine 不直接访问 Device Farm 或 Sandbox，而是通过 Execution Environment Management 获取所需的执行环境。

---

# 4. Core Responsibilities

Execution Environment Management 负责执行环境资源的统一管理，其核心职责如下。

## 4.1 Environment Registry

维护平台所有执行环境资源信息。

包括：

- 真机设备；
- 沙箱实例；
- 环境模板；
- 环境标签；
- 能力信息；
- 当前状态。

统一形成环境资源目录。

---

## 4.2 Resource Scheduling

根据检测任务需求选择合适的执行环境。

调度因素包括：

- Android 版本；
- 设备类型；
- Root 能力；
- CPU 架构；
- 内存容量；
- 当前负载；
- 环境可用性；
- 资源优先级。

Execution Environment Management 决定**使用哪个环境**，但不决定**执行什么任务**。

---

## 4.3 Environment Allocation

负责环境资源分配与释放。

包括：

- 环境申请；
- 环境锁定；
- 资源占用；
- 环境释放；
- 回收复用。

确保资源不会被多个任务同时占用。

---

## 4.4 Environment Lifecycle Management

统一管理执行环境生命周期。

典型生命周期如下：

```text
Register

↓

Available

↓

Allocated

↓

Running

↓

Released

↓

Recover

↓

Available

↓

Offline
```

这里管理的是**执行环境生命周期**，不是应用生命周期。

---

## 4.5 Health Monitoring

持续监控执行环境运行状态。

包括：

- 在线状态；
- 健康状态；
- CPU；
- 内存；
- 存储；
- 网络；
- 故障状态；
- 可调度状态。

异常环境将自动退出资源池。

---

# 5. Functional Architecture

```text
Execution Environment Management

├── Environment Registry
├── Resource Scheduler
├── Environment Allocator
├── Lifecycle Manager
├── Health Monitor
├── Metadata Manager
└── Execution Environment API
```

各组件共同完成执行环境资源管理。

---

# 6. Scheduling Workflow

执行环境调度流程如下：

```text
Analysis Engine
        │
        ▼
Execution Environment API
        │
        ▼
Resource Scheduler
        │
        ├────────► Device Farm
        │
        └────────► Sandbox
        │
        ▼
Allocate Environment
        │
        ▼
Return Execution Handle
```

Analysis Engine 获取 Execution Handle 后，即可在对应环境中开展分析任务。

---

# 7. Environment Metadata

每个执行环境均维护统一元数据。

示例字段包括：

| Field | Description |
|--------|-------------|
| Environment ID | 环境唯一标识 |
| Environment Type | Device / Sandbox |
| Android Version | Android 系统版本 |
| API Level | API 等级 |
| Root Capability | 是否支持 Root |
| Architecture | CPU 架构 |
| Status | 当前状态 |
| Labels | 环境标签 |
| Health Score | 健康评分 |

统一元数据有助于实现资源发现、调度和监控。

---

# 8. Public Interfaces

Execution Environment Management 对上层提供统一接口。

典型接口如下：

| API | Description |
|------|-------------|
| RegisterEnvironment() | 注册环境 |
| QueryEnvironment() | 查询环境 |
| AllocateEnvironment() | 分配环境 |
| ReleaseEnvironment() | 释放环境 |
| GetEnvironmentStatus() | 获取环境状态 |
| RecoverEnvironment() | 恢复环境 |
| RemoveEnvironment() | 移除环境 |

上层无需关心环境来自真机还是沙箱。

---

# 9. Design Principles

Execution Environment Management 遵循以下原则。

## Unified Resource Management

统一管理所有执行环境资源。

---

## Resource Abstraction

屏蔽不同执行环境的实现差异，为上层提供统一资源模型。

---

## Decoupling

Analysis Engine 与具体执行资源解耦。

Analysis Engine 仅依赖统一接口。

---

## Scalability

支持执行环境横向扩展，新增设备或沙箱无需修改上层分析逻辑。

---

## High Availability

支持资源健康检查、自动摘除、自动恢复和动态调度，保证平台持续运行。

---

# 10. Relationship with Other Modules

Execution Environment Management 与平台其它模块关系如下：

```text
                  Analysis Engine Layer
                           │
                           ▼
        Execution Environment Management
                │                     │
                ▼                     ▼
          Device Farm            Sandbox
```

Execution Environment Management 是 Execution Environment Layer 的统一管理入口，也是 Analysis Engine 获取执行环境的唯一入口。

---

# 11. Key Technologies

| 技术领域 | 关键技术 | 说明 |
|----------|---------|------|
| 资源注册 | Service Registry (Consul/Eureka) | 执行环境自动注册与发现 |
| 资源调度 | 自研调度器 / Kubernetes Scheduler | 基于约束的执行环境智能调度 |
| 资源分配 | 分布式锁 (Redis/ZooKeeper) | 资源独占分配与并发控制 |
| 健康监控 | Health Check / Heartbeat | 执行环境状态持续监控与异常检测 |
| 元数据管理 | etcd / MongoDB | 环境元数据存储与查询 |
| 负载均衡 | Round Robin / Weighted / Least Loaded | 多种调度策略支持 |

---

# 12. Technical Metrics

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 环境分配延迟 | ≤ 30 sec | 从请求到环境就绪的等待时间 |
| 资源调度成功率 | ≥ 99% | 环境分配请求的成功率 |
| 资源健康检测周期 | ≤ 30 sec | 健康检查间隔 |
| 故障检测延迟 | ≤ 60 sec | 从环境异常到检测发现的时间 |
| 故障恢复时间 | ≤ 5 min | 环境异常后恢复时间 |
| 调度公平性 | ≥ 95% | 资源分配的公平性指数 |
| 系统可用率 | ≥ 99.9% | 管理服务可用时间占比 |

---

# 13. Summary

Execution Environment Management 是 Execution Environment Layer 的资源管理中心。

关键技术涵盖服务注册（Consul/Eureka）、智能调度、分布式锁（Redis/ZooKeeper）和健康监控等。

技术指标从分配效率、调度可靠性、故障恢复和系统可用性等维度定义了能力基线，确保 Execution Environment Management 通过统一的资源注册、调度、分配、监控和生命周期管理，实现真实设备与虚拟执行环境的统一管理，为 Analysis Engine 提供稳定、高效、可扩展的执行环境服务。

Execution Environment Management 不承担程序分析职责，也不负责应用执行控制，而是作为执行环境资源的统一控制平面，支撑整个移动应用安全检测平台的运行。
