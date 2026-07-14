# Chapter 03 Execution Environment Layer

---

# 1. Overview

Execution Environment Layer（执行环境层）是移动应用安全检测平台的基础能力层。

本层负责构建统一、可控、可观测、可重复的应用运行环境，为 Analysis Engine Layer 提供稳定、一致的程序执行基础。

Execution Environment Layer 本身**不负责安全检测**，也**不负责风险判断**，而是负责让应用能够在不同环境中真实运行，并向上层提供统一的运行时数据采集能力。

---

# 2. Design Objectives

Execution Environment Layer 的建设目标包括：

- 提供统一的应用执行环境；
- 屏蔽不同设备和系统环境差异；
- 支持静态分析前后的运行准备；
- 支持动态分析、自动化测试和行为采集；
- 提供统一的系统事件采集能力；
- 提供统一的网络环境模拟能力；
- 支持大规模并发检测。

---

# 3. Architecture

整体架构如下：

```text
                     Analysis Engine Layer
                               ▲
                               │
               Runtime APIs / Events / Logs
                               │
──────────────────────────────────────────────────────────

              Execution Environment Layer

    ┌───────────────────────────────────────────────┐
    │ Application Lifecycle Management              │
    ├───────────────────────────────────────────────┤
    │ Device Management                             │
    ├───────────────────────────────────────────────┤
    │ Runtime Environment Management                │
    ├───────────────────────────────────────────────┤
    │ Environment Simulation                        │
    ├───────────────────────────────────────────────┤
    │ Automation Runtime                            │
    ├───────────────────────────────────────────────┤
    │ Runtime Data Collection                       │
    └───────────────────────────────────────────────┘
```

---

# 4. Core Responsibilities

Execution Environment Layer 主要承担以下职责：

## 4.1 Environment Provisioning

负责创建应用运行环境，包括：

- Android 真机；
- Android Emulator；
- Sandbox；
- Root Environment（可选）；
- Multi-Version Android Runtime。

平台能够根据检测任务自动选择适合的运行环境。

---

## 4.2 Environment Isolation

不同检测任务之间相互隔离。

每个任务拥有独立：

- 文件系统；
- 应用安装空间；
- 网络状态；
- 系统配置；
- 数据目录。

保证检测结果不会互相影响。

---

## 4.3 Environment Reproducibility

检测环境必须支持重复构建。

同一个应用在相同配置下应能够获得一致的运行结果。

支持：

- Snapshot；
- Environment Reset；
- Template Image。

---

## 4.4 Runtime Observability

Execution Environment Layer 应持续采集应用运行过程中产生的系统事件。

包括：

- Process Lifecycle；
- Activity Lifecycle；
- Service Lifecycle；
- Broadcast；
- File Events；
- Network Events；
- Binder Events；
- Permission Usage。

这些数据统一提供给 Analysis Engine Layer。

---

# 5. Functional Architecture

Execution Environment Layer 由六个核心子模块组成。

```text
Execution Environment Layer

├── Application Lifecycle Management
├── Device Management
├── Runtime Environment Management
├── Environment Simulation
├── Automation Runtime
└── Runtime Data Collection
```

---

# 6. Application Lifecycle Management

负责应用生命周期管理。

包括：

- Install
- Upgrade
- Launch
- Background
- Resume
- Force Stop
- Uninstall

统一管理应用执行流程。

提供标准生命周期接口。

---

# 7. Device Management

负责管理各种检测设备。

支持：

## Real Device

真实 Android 设备。

适用于：

- Root 检测；
- 系统兼容性；
- ROM 差异分析。

---

## Emulator

Android Emulator。

适用于：

- 批量检测；
- 自动化执行；
- 快速环境恢复。

---

## Device Farm

统一设备资源池。

负责：

- 调度；
- 分配；
- 回收；
- 健康检查。

---

# 8. Runtime Environment Management

负责维护应用运行所需的系统环境。

包括：

## Android Version

支持多个 Android API Level。

例如：

- Android 10
- Android 11
- Android 12
- Android 13
- Android 14

---

## System Configuration

包括：

- Locale
- Time Zone
- Screen Resolution
- Language
- Accessibility
- Developer Options

---

## Runtime Configuration

包括：

- Root 状态
- SELinux
- Debuggable
- USB
- Developer Mode

---

# 9. Environment Simulation

为了覆盖不同业务场景，需要支持丰富的环境模拟能力。

包括：

## Network Simulation

支持：

- Wi-Fi
- Cellular
- Offline
- Proxy
- VPN

可模拟：

- 网络延迟；
- 丢包；
- 限速；
- DNS 劫持（测试环境）。

---

## Device Information Simulation

模拟：

- IMEI（测试）
- Android ID
- MAC Address（测试）
- Manufacturer
- Model
- Screen Size

用于观察应用环境感知行为。

---

## User State Simulation

模拟：

- 首次启动；
- 已登录；
- 新用户；
- 老用户；
- 多账号；
- 权限已授权；
- 权限未授权。

---

## Location Simulation

支持：

- GPS；
- Mock Location；
- 城市切换；
- 国家切换。

---

## Time Simulation

支持：

- 系统时间调整；
- 时区调整；
- 日期跳变。

用于检测时间敏感行为。

---

# 10. Automation Runtime

负责自动驱动应用运行。

支持：

- UI 自动化；
- 页面遍历；
- 点击；
- 输入；
- 滑动；
- 深链启动；
- Intent 调用。

Automation Runtime 不负责分析，只负责执行。

Analysis Engine 基于执行结果进行分析。

---

# 11. Runtime Data Collection

负责统一采集运行时数据。

包括：

## System Events

- Process
- Activity
- Service
- Broadcast

---

## File System

- Create
- Read
- Write
- Delete

---

## Network

- Socket
- HTTP
- HTTPS
- DNS

---

## IPC

- Binder
- ContentProvider
- Broadcast

---

## Permission Usage

记录：

- 权限申请；
- 权限使用；
- 敏感权限调用。

---

## Application Logs

包括：

- Logcat；
- ANR；
- Crash；
- Native Crash。

所有运行数据统一进入 Analysis Engine。

---

# 12. Design Principles

Execution Environment Layer 遵循以下原则：

## Environment First

所有分析能力建立在统一运行环境之上。

---

## Deterministic Execution

同样环境、同样输入，应获得一致输出。

---

## Isolation

任务之间严格隔离。

---

## High Scalability

支持海量应用并发检测。

---

## Extensibility

支持新增：

- Android 新版本；
- 新设备；
- 新环境；
- 新模拟能力。

无需影响上层 Analysis Engine。

---

# 13. Inputs and Outputs

## Input

来自 Application Access Layer：

- Detection Task；
- Runtime Profile；
- Environment Configuration。

---

## Output

向 Analysis Engine Layer 输出：

- Runtime Events；
- Runtime Logs；
- Application State；
- Execution Context；
- System Telemetry。

Execution Environment Layer 不产生任何安全结论，仅负责提供真实、完整、可观测的应用运行环境。

---

# 14. Chapter Navigation

Execution Environment Layer 为整个检测平台提供统一的运行基础。

下一章节将介绍 **Analysis Engine Layer**，说明平台如何基于执行环境对应用进行静态分析、动态分析和统一分析结果管理。
