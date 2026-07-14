# Sandbox

---

# 1. Overview

Sandbox（沙箱执行环境）负责为移动应用提供隔离、安全、可重复的虚拟执行环境，是 Execution Environment Layer 的核心执行资源之一。

与 Device Farm 提供真实设备资源不同，Sandbox 提供基于虚拟化技术构建的执行环境，能够快速创建、复制、恢复和销毁，用于支撑大规模自动化检测、动态分析以及安全研究等场景。

Sandbox 的核心目标是通过环境隔离和资源虚拟化，为 Analysis Engine 提供稳定、高效、可控的应用运行环境。

Sandbox 本身不负责程序分析，也不负责风险判断，仅负责提供虚拟执行资源。

---

# 2. Design Objectives

Sandbox 的设计目标包括：

- 提供统一的虚拟执行环境；
- 支持快速创建和销毁执行实例；
- 支持环境隔离与资源隔离；
- 支持环境快照与快速恢复；
- 支持多实例并发运行；
- 提供标准化的环境管理接口；
- 满足大规模自动化检测需求。

---

# 3. Position in Architecture

Sandbox 是 Execution Environment Layer 中的重要执行资源。

```text
                    Analysis Engine Layer
                              │
                              ▼
              Execution Environment Management
                     │                   │
                     ▼                   ▼
               Device Farm           Sandbox
```

Device Farm 提供真实设备。

Sandbox 提供虚拟执行环境。

两者统一由 Execution Environment Management 管理和调度。

---

# 4. Core Responsibilities

Sandbox 主要承担以下职责。

## 4.1 Virtual Execution Environment

提供可运行移动应用的虚拟环境。

支持：

- Android Emulator；
- 基于虚拟机的 Android Runtime；
- 基于容器的 Android Runtime（未来）；
- 其它虚拟执行环境实现。

---

## 4.2 Environment Isolation

每个 Sandbox 实例均具有独立运行空间。

包括：

- 文件系统；
- 应用数据；
- 网络；
- 系统配置；
- 用户数据；
- 临时文件。

不同实例之间互不影响。

---

## 4.3 Rapid Provisioning

支持执行环境快速创建。

包括：

- 创建实例；
- 克隆实例；
- 模板部署；
- 自动初始化。

满足批量检测任务对环境快速启动的要求。

---

## 4.4 Snapshot & Recovery

支持环境状态保存与恢复。

包括：

- Snapshot；
- Restore；
- Rollback；
- Clone。

保证检测任务具有良好的可重复性。

---

## 4.5 Resource Reuse

支持执行实例重复利用。

通过环境恢复和资源回收，减少环境初始化时间，提高资源利用率。

---

# 5. Sandbox Architecture

```text
Sandbox

├── Runtime Instance
├── Filesystem Isolation
├── Network Isolation
├── Process Isolation
├── Snapshot Management
├── Resource Control
└── Instance Metadata
```

各 Sandbox 实例均拥有独立运行上下文。

---

# 6. Sandbox Lifecycle

Sandbox 实例生命周期如下：

```text
Create

↓

Initialize

↓

Ready

↓

Allocated

↓

Running

↓

Released

↓

Restore

↓

Ready

↓

Destroy
```

状态说明：

| State | Description |
|--------|-------------|
| Create | 创建实例 |
| Initialize | 初始化运行环境 |
| Ready | 可供调度 |
| Allocated | 分配给检测任务 |
| Running | 正在执行任务 |
| Released | 检测结束 |
| Restore | 恢复初始状态 |
| Destroy | 销毁实例 |

---

# 7. Isolation Model

Sandbox 提供多维度隔离能力。

## 7.1 Filesystem Isolation

每个实例拥有独立文件系统。

避免应用数据交叉污染。

---

## 7.2 Process Isolation

不同实例运行于独立进程空间。

保证检测任务互不干扰。

---

## 7.3 Network Isolation

支持独立网络配置。

包括：

- 独立 IP；
- 独立 DNS；
- 独立代理配置；
- 独立网络策略。

---

## 7.4 Resource Isolation

限制各实例资源使用。

包括：

- CPU；
- Memory；
- Disk；
- Network。

保证多实例稳定运行。

---

# 8. Snapshot Management

Snapshot 是 Sandbox 的重要能力。

支持：

- 创建快照；
- 加载快照；
- 删除快照；
- 回滚快照；
- 快照克隆。

典型流程如下：

```text
Base Image

↓

Initialize

↓

Snapshot

↓

Allocate

↓

Run Analysis

↓

Restore Snapshot

↓

Ready
```

通过快照机制，避免重复安装和初始化环境，提高检测效率。

---

# 9. Interfaces

Sandbox 对 Execution Environment Management 提供统一接口。

典型接口包括：

| API | Description |
|------|-------------|
| CreateInstance() | 创建实例 |
| DestroyInstance() | 销毁实例 |
| AllocateInstance() | 分配实例 |
| ReleaseInstance() | 释放实例 |
| CreateSnapshot() | 创建快照 |
| RestoreSnapshot() | 恢复快照 |
| QueryStatus() | 查询实例状态 |

Analysis Engine 不直接管理 Sandbox 实例，而是通过 Execution Environment Management 获取执行环境。

---

# 10. Design Principles

Sandbox 的设计遵循以下原则。

## Isolation First

每个执行实例必须完全隔离。

---

## Fast Recovery

优先采用快照恢复，而非重新创建环境。

---

## Resource Efficiency

支持资源复用，提高虚拟执行环境利用率。

---

## Elastic Scaling

支持根据检测任务动态扩容和缩容。

---

## Unified Management

所有 Sandbox 实例均由 Execution Environment Management 统一管理。

---

# 11. Relationship with Other Modules

Sandbox 与其它模块关系如下：

```text
                 Analysis Engine
                        │
                        ▼
        Execution Environment Management
                │                 │
                ▼                 ▼
          Device Farm         Sandbox
```

Sandbox 与 Device Farm 共同构成平台执行资源池。

Execution Environment Management 根据检测任务统一调度真实设备或虚拟执行环境。

---

# 12. Key Technologies

| 技术领域 | 关键技术 | 说明 |
|----------|---------|------|
| 虚拟化 | QEMU / KVM | 硬件辅助虚拟化，支持 ARM/x86 加速 |
| Android 模拟器 | Google Android Emulator | 官方模拟器，GPU 加速，快速启动 |
| 容器化 Android | Redroid / Anbox | Docker/LXC 容器化 Android，低资源开销 |
| 文件系统隔离 | OverlayFS / Union Mount | 容器级文件系统隔离 |
| 网络隔离 | Network Namespace / veth pair | 独立网络栈与 IP 配置 |
| 快照管理 | QEMU Snapshot / Docker Checkpoint (CRIU) | 虚拟机/容器状态保存与秒级恢复 |
| 资源控制 | Cgroups / CPU quotas | CPU/内存/IO 资源限制 |
| GPU 直通 | VirGL / GPU Passthrough | 虚拟环境 GPU 加速 |

---

# 13. Technical Metrics

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 实例启动时间 | ≤ 60 sec | 从创建到 Ready 的耗时 |
| 快照恢复时间 | ≤ 30 sec | 从快照恢复到环境可用的耗时 |
| 并发实例数 | ≥ 100 | 同时运行的 Sandbox 实例数量 |
| 实例可用率 | ≥ 99.5% | 虚拟环境可用时间占比 |
| 实例间隔离有效性 | 100% | 实例间数据/网络完全隔离 |
| 环境回收时间 | ≤ 10 sec | 释放实例资源的耗时 |
| 资源利用率 | ≥ 70% | Sandbox 集群资源利用效率 |
| 单实例资源开销 | ≤ 2 GB RAM / 2 CPU | 单个 Sandbox 实例的资源消耗 |

---

# 14. Summary

Sandbox 为移动应用安全检测平台提供统一的虚拟执行环境。

关键技术涵盖虚拟化（QEMU/KVM）、Android 模拟器、容器化 Android（Redroid/Anbox）、网络隔离（Network Namespace）、快照管理（QEMU Snapshot/Docker Checkpoint）和资源控制（Cgroups）等。

技术指标从实例启动效率、快照恢复、并发能力、隔离安全和资源效率等维度定义了能力基线，确保 Sandbox 能够支持大规模自动化检测和动态分析任务，与 Device Farm 共同构建平台完整的执行资源体系。
