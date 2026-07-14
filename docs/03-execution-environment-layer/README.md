# Chapter 03 Execution Environment Layer

---

# 1. Overview

Execution Environment Layer（执行环境层）是移动应用安全检测平台的基础资源层。

本层负责构建、管理和调度应用检测所需的执行环境，为 Analysis Engine Layer 提供统一、稳定、可扩展的运行资源。

Execution Environment Layer 不负责程序分析，也不负责应用执行控制，而是作为整个检测平台的资源提供者（Resource Provider），统一管理真实设备、虚拟执行环境以及执行资源生命周期。

所有程序分析能力均运行在 Execution Environment Layer 提供的执行环境之上。

---

# 2. Design Objectives

Execution Environment Layer 的设计目标包括：

- 提供统一的应用执行资源；
- 支持真实设备和虚拟环境统一管理；
- 支持执行环境统一注册和调度；
- 提供标准化环境管理接口；
- 支持大规模并发检测任务；
- 支持环境快速创建、恢复和回收；
- 为 Analysis Engine 提供稳定、可重复的运行基础。

Execution Environment Layer 关注的是**执行资源（Execution Resources）**，而不是**程序分析（Program Analysis）**。

---

# 3. Position in Overall Architecture

Execution Environment Layer 位于整个检测能力链的底层。

```text
┌──────────────────────────────────────────────┐
│         Application Access Layer             │
└──────────────────────────────────────────────┘
                     ▲
                     │
┌──────────────────────────────────────────────┐
│         Detection Service Layer              │
└──────────────────────────────────────────────┘
                     ▲
                     │
┌──────────────────────────────────────────────┐
│         Analysis Engine Layer                │
└──────────────────────────────────────────────┘
                     ▲
                     │
┌──────────────────────────────────────────────┐
│       Execution Environment Layer            │
└──────────────────────────────────────────────┘
```

Analysis Engine 负责分析应用程序。

Execution Environment Layer 负责提供应用运行所需的执行资源。

两层通过标准接口协同工作，实现资源管理与程序分析解耦。

---

# 4. Core Responsibilities

Execution Environment Layer 主要承担以下职责。

## 4.1 Execution Resource Management

统一管理平台所有执行资源，包括：

- 真机设备；
- 虚拟执行环境；
- 环境资源池；
- 执行实例。

---

## 4.2 Environment Scheduling

根据检测任务需求自动选择合适的执行环境。

例如：

- Android 版本；
- Root 环境；
- 真机；
- 虚拟环境；
- 指定设备型号。

Execution Environment Layer 不决定**执行什么**，仅决定**在哪里执行**。

---

## 4.3 Environment Lifecycle Management

统一管理执行环境生命周期。

包括：

- 创建；
- 注册；
- 分配；
- 回收；
- 恢复；
- 销毁。

管理对象为**执行环境**，而非应用生命周期。

---

## 4.4 Resource Monitoring

持续监控执行资源运行状态。

包括：

- 在线状态；
- 健康状态；
- CPU；
- 内存；
- 存储；
- 网络；
- 环境可用性。

为资源调度提供依据。

---

# 5. Architecture

Execution Environment Layer 包括三个核心模块。

```text
Execution Environment Layer

├── Device Farm
├── Sandbox
└── Execution Environment Management
```

整体关系如下：

```text
                    Analysis Engine Layer
                              ▲
                              │
                 Execution Environment APIs
                              │
          ┌───────────────────┴───────────────────┐
          │                                       │
┌────────────────────┐             ┌────────────────────────────┐
│    Device Farm     │             │         Sandbox            │
└────────────────────┘             └────────────────────────────┘
          ▲                                       ▲
          └───────────────────┬───────────────────┘
                              │
          ┌────────────────────────────────────────┐
          │ Execution Environment Management       │
          └────────────────────────────────────────┘
```

Execution Environment Management 统一管理所有执行资源，并向 Analysis Engine 提供标准化访问接口。

---

# 6. Core Modules

## 6.1 Device Farm

Device Farm 提供真实设备资源。

负责：

- 真机资源管理；
- 设备注册；
- 设备分配；
- 设备回收；
- 健康检查；
- 在线监控。

支持多种 Android 设备统一管理。

---

## 6.2 Sandbox

Sandbox 提供虚拟执行环境。

负责：

- Android Emulator；
- 虚拟设备；
- 隔离运行环境；
- 环境快照；
- 环境恢复；
- 环境克隆。

Sandbox 主要用于批量检测、自动化分析以及快速环境恢复。

---

## 6.3 Execution Environment Management

Execution Environment Management 是 Execution Environment Layer 的管理中心。

负责统一管理所有执行资源。

主要能力包括：

- 环境注册；
- 资源调度；
- 资源分配；
- 环境状态管理；
- 健康监控；
- 环境恢复；
- 环境元数据管理。

Execution Environment Management 不直接运行应用，而是负责管理执行环境。

---

# 7. Interaction with Analysis Engine

Execution Environment Layer 与 Analysis Engine Layer 采用资源提供模式协作。

Analysis Engine 根据检测任务申请执行环境。

Execution Environment Layer 根据资源状态分配执行资源，并返回运行实例。

典型流程如下：

```text
Analysis Engine

        │

        │ Request Execution Environment

        ▼

Execution Environment Management

        │

        ├────────► Device Farm

        │

        └────────► Sandbox

        │

        ▼

Execution Handle

        │

        ▼

Analysis Engine
```

Execution Environment Layer 不控制应用启动，也不控制应用执行，仅负责提供执行资源。

---

# 8. Design Principles

Execution Environment Layer 遵循以下设计原则。

## Resource-Oriented

所有能力围绕执行资源构建。

不承担程序分析职责。

---

## Unified Management

统一管理所有执行环境。

屏蔽不同设备和虚拟环境之间的差异。

---

## Loose Coupling

与 Analysis Engine 解耦。

Analysis Engine 不关心底层资源实现方式。

---

## High Availability

支持执行环境健康检查、故障恢复和资源自动调度。

保证平台持续稳定运行。

---

## Scalability

支持执行资源横向扩展。

能够根据检测规模动态增加设备和虚拟环境。

---

# 9. Inputs and Outputs

## Input

来自 Analysis Engine Layer：

- Execution Request；
- Environment Requirement；
- Runtime Profile。

---

## Output

返回：

- Execution Handle；
- Device Instance；
- Sandbox Instance；
- Environment Metadata。

Execution Environment Layer 不输出程序分析结果，也不输出安全风险结论。

---

# 10. Chapter Organization

本章节包括以下内容：

```text
03-execution-environment-layer/

├── README.md
├── device-farm.md
├── sandbox.md
└── execution-environment-management.md
```

三个模块共同构成统一的执行环境资源平台，为 Analysis Engine Layer 提供稳定、高效、可扩展的运行基础。

---

# 11. Key Technologies

Execution Environment Layer 涉及的关键技术如下。

## 11.1 设备虚拟化技术

| 技术 | 用途 | 说明 |
|------|------|------|
| QEMU/KVM | 硬件辅助虚拟化 | 基于 Linux KVM 的 Android 虚拟机，支持 ARM/x86 硬件加速 |
| Android Emulator | Google 官方模拟器 | 基于 QEMU 2.12+，支持 GPU 加速、快速启动 |
| Redroid | 容器化 Android | 基于 Docker 的 Android 运行环境，资源开销低，适合批量部署 |
| Anbox | 容器化 Android | 基于 LXC 的 Android 容器，支持 GPU 直通 |

**选型策略**：
- 需要高兼容性和完整系统模拟：Android Emulator + KVM
- 需要高密度并发和低资源开销：Redroid 容器方案
- 需要真实硬件行为（传感器、基带等）：真机设备

## 11.2 设备管控技术

| 技术 | 用途 | 说明 |
|------|------|------|
| ADB (Android Debug Bridge) | 设备通信 | 设备连接、应用安装、文件传输、日志采集 |
| STF (Smartphone Test Farm) | 设备集群管理 | 设备注册、远程控制、屏幕投射、批量操作 |
| Scrcpy / Minicap | 屏幕流采集 | 低延迟屏幕投射，用于 UI 自动化分析 |
| USB Hub + ADB over TCP | 连接管理 | 多设备 USB 集中管理，TCP 模式远程访问 |

## 11.3 网络模拟与隔离技术

| 技术 | 用途 | 说明 |
|------|------|------|
| Linux Network Namespace | 网络隔离 | 每个执行实例独立网络栈 |
| TC (Traffic Control) | 流量控制 | 延迟注入、丢包模拟、带宽限制 |
| iptables / nftables | 防火墙策略 | 网络访问控制、流量重定向 |
| Mitmproxy / Charles | 中间人代理 | HTTPS 流量截获与分析 |
| DNS 劫持模拟 | 域名解析控制 | 自定义 DNS 响应，模拟钓鱼域名场景 |

## 11.4 环境快照与恢复技术

| 技术 | 用途 | 说明 |
|------|------|------|
| QEMU Snapshot | 虚拟机快照 | 完整 VM 状态保存与秒级恢复 |
| Docker Checkpoint/Restore | 容器快照 | CRIU-based 容器状态保存与恢复 |
| Android Backup/Restore | 系统状态恢复 | 应用数据与系统设置的备份还原 |
| Factory Reset | 设备恢复 | 真机设备恢复出厂设置，确保环境干净 |

## 11.5 运行时监控注入技术

| 技术 | 用途 | 说明 |
|------|------|------|
| Frida | 动态插桩 | 运行时方法 Hook、参数追踪、返回值修改 |
| Xposed / LSPosed | Framework Hook | 系统 API 级别拦截，无需修改 APK |
| Magisk | Root 管理 | 无系统修改的 Root 方案，支持 Zygisk 注入 |
| Riru | Zygote 注入 | 基于 Zygote 进程的模块加载框架 |

---

# 12. Technical Metrics

Execution Environment Layer 的关键技术指标如下。

## 12.1 资源容量指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 真机设备数量 | ≥ 200 台 | 覆盖主流品牌和 Android 版本 |
| 并发 Sandbox 实例 | ≥ 100 个 | 同时运行的虚拟执行环境数量 |
| 设备品牌覆盖 | ≥ 10 个品牌 | Google、Samsung、Xiaomi、OPPO、vivo 等 |
| Android 版本覆盖 | Android 10-15 | 覆盖市场主流 Android 版本 |
| Root/Non-Root 设备比例 | 1:1 | 两种环境均需充足覆盖 |

## 12.2 性能指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 环境分配延迟 | ≤ 30 sec | 从请求到环境就绪的等待时间 |
| Sandbox 实例启动时间 | ≤ 60 sec | 从创建到 Ready 的耗时 |
| 快照恢复时间 | ≤ 30 sec | 从快照恢复到环境可用的耗时 |
| 真机设备恢复时间 | ≤ 5 min | 清理应用数据并恢复到可用状态 |
| 环境回收时间 | ≤ 10 sec | 释放执行环境资源的耗时 |

## 12.3 可靠性指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 设备在线率 | ≥ 98% | 真机设备在线可用时间占比 |
| Sandbox 可用率 | ≥ 99.5% | 虚拟环境可用时间占比 |
| 环境故障恢复时间 | ≤ 5 min | 异常环境自动检测和恢复时间 |
| 资源调度成功率 | ≥ 99% | 环境分配请求的成功率 |
| 设备利用率 | ≥ 80% | 真机设备资源利用效率 |

## 12.4 隔离安全指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 实例间数据泄露 | 0 | 不同执行实例间的数据完全隔离 |
| 网络隔离有效性 | 100% | 实例间网络流量不可互访 |
| 环境恢复干净度 | 100% | 恢复后无残留应用数据和文件 |

---

# 13. Summary

Execution Environment Layer 是整个移动应用安全检测平台的执行资源基础。

它通过 Device Farm、Sandbox 和 Execution Environment Management 三个核心模块，实现真实设备与虚拟执行环境的统一管理，为 Analysis Engine 提供标准化的执行资源。

关键技术涵盖设备虚拟化（QEMU/KVM、Redroid）、设备集群管控（STF、ADB）、网络隔离（Network Namespace、TC）、环境快照（QEMU Snapshot、Docker Checkpoint）以及运行时注入（Frida、Xposed）等。

技术指标从资源容量、性能、可靠性和隔离安全四个维度定义了能力基线，确保平台具备大规模、高并发、高可用的执行环境支撑能力。

这种设计将资源管理与程序分析彻底解耦，使平台能够灵活扩展新的设备类型、执行环境和资源调度策略，为平台长期演进和大规模检测提供可靠支撑。
