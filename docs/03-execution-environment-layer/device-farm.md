# Device Farm

---

# 1. Overview

Device Farm（真机资源池）负责统一管理平台中的真实移动设备资源，为 Analysis Engine Layer 提供稳定、可靠、可调度的真机执行环境。

相比虚拟执行环境，真实设备能够更准确地反映应用在实际用户终端上的运行行为，因此在兼容性验证、安全检测、运行时分析以及对抗检测等场景中具有不可替代的价值。

Device Farm 不负责程序分析，也不负责应用执行控制，其职责是统一管理设备资源，并向 Execution Environment Management 提供标准化的设备服务。

---

# 2. Design Objectives

Device Farm 的设计目标包括：

- 建立统一的真机资源池；
- 支持多品牌、多型号、多系统版本设备管理；
- 提供标准化设备注册与发现机制；
- 支持设备资源统一调度；
- 实现设备健康监控；
- 支持设备快速恢复与复用；
- 为 Analysis Engine 提供稳定、一致的真实设备环境。

---

# 3. Architecture

```text
                    Execution Environment Management
                                   │
                         Device Allocation API
                                   │
         ┌─────────────────────────┴─────────────────────────┐
         │                                                   │
┌────────────────────┐                           ┌────────────────────┐
│   Device Registry  │                           │   Device Monitor   │
└────────────────────┘                           └────────────────────┘
         │                                                   │
         └─────────────────────────┬─────────────────────────┘
                                   │
                          Device Resource Pool
                                   │
        ┌───────────────┬───────────────┬───────────────┐
        │               │               │
   Pixel Series     Samsung Series   Xiaomi Series   ...
```

Device Farm 负责维护设备资源池，并持续向 Execution Environment Management 上报资源状态。

---

# 4. Responsibilities

Device Farm 的职责包括：

## 4.1 Device Resource Management

统一管理平台所有真实设备资源。

包括：

- 设备注册；
- 设备下线；
- 设备信息维护；
- 设备分组；
- 标签管理。

---

## 4.2 Device Allocation

根据环境要求分配符合条件的设备。

例如：

- Android Version；
- Device Model；
- CPU Architecture；
- Root Capability；
- ABI；
- RAM；
- Screen Resolution。

Device Farm 不负责决定检测策略，仅负责资源匹配。

---

## 4.3 Device Monitoring

持续监控设备运行状态。

包括：

- Online / Offline；
- CPU 使用率；
- 内存使用率；
- 存储空间；
- 电池状态；
- 温度；
- 网络状态；
- USB 连接状态。

---

## 4.4 Device Recovery

支持设备自动恢复。

包括：

- 清理应用；
- 清理数据；
- 重启设备；
- 恢复系统状态；
- 恢复默认配置。

确保设备能够快速投入下一次检测任务。

---

# 5. Device Model

平台中的每台设备均具有统一描述模型。

```text
Device

├── Device ID
├── Manufacturer
├── Model
├── Android Version
├── API Level
├── CPU ABI
├── RAM
├── Storage
├── Screen
├── Root Capability
├── Network Capability
├── Status
└── Tags
```

统一模型使调度层无需关心不同厂商设备的差异。

---

# 6. Device Lifecycle

真实设备在平台中的生命周期如下：

```text
Register

↓

Initialize

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
```

各状态说明：

| State | Description |
|--------|-------------|
| Register | 设备注册到平台 |
| Initialize | 初始化设备环境 |
| Available | 可供调度 |
| Allocated | 已分配给检测任务 |
| Running | 正在执行检测 |
| Released | 检测结束，释放资源 |
| Recover | 恢复设备初始状态 |

---

# 7. Device Classification

为了满足不同检测需求，Device Farm 支持多维度设备分类。

## 7.1 By Manufacturer

例如：

- Google Pixel；
- Samsung；
- Xiaomi；
- OPPO；
- vivo；
- HONOR；
- HUAWEI（兼容场景）。

---

## 7.2 By Android Version

例如：

- Android 10；
- Android 11；
- Android 12；
- Android 13；
- Android 14；
- Android 15（未来）。

---

## 7.3 By Capability

包括：

- Root Device；
- Non-Root Device；
- Debuggable Device；
- High Performance Device；
- Low-End Device。

---

## 7.4 By Usage

包括：

- Compatibility Testing；
- Dynamic Analysis；
- Malware Analysis；
- Performance Analysis；
- Regression Testing。

---

# 8. Health Monitoring

为了保证检测任务稳定运行，平台持续监控设备健康状态。

主要指标包括：

| Category | Metrics |
|----------|---------|
| Availability | Online、Offline、Busy |
| Performance | CPU、Memory、Storage |
| Stability | Crash、ANR、Reboot |
| Connectivity | USB、ADB、Network |
| Environment | Battery、Temperature |

当设备状态异常时，可自动将设备从资源池中摘除，待恢复后重新加入。

---

# 9. Interfaces

Device Farm 对 Execution Environment Management 提供统一接口。

典型接口包括：

| API | Description |
|------|-------------|
| RegisterDevice() | 注册设备 |
| RemoveDevice() | 移除设备 |
| QueryDevices() | 查询设备 |
| AllocateDevice() | 分配设备 |
| ReleaseDevice() | 释放设备 |
| GetDeviceStatus() | 获取设备状态 |
| RecoverDevice() | 恢复设备 |

上层无需关心设备连接方式或厂商差异。

---

# 10. Design Principles

Device Farm 遵循以下原则。

## Resource Abstraction

所有真实设备统一抽象为标准资源对象。

---

## Unified Scheduling

设备统一由 Execution Environment Management 调度。

Device Farm 不直接参与检测任务管理。

---

## High Availability

支持设备故障检测、自动摘除和恢复，保证资源池稳定运行。

---

## Scalability

支持新增设备型号、Android 版本和厂商类型，无需修改 Analysis Engine。

---

# 11. Relationship with Other Modules

Device Farm 与 Execution Environment Layer 其它模块关系如下：

```text
                   Analysis Engine
                          │
                          ▼
          Execution Environment Management
                   │                │
                   ▼                ▼
             Device Farm         Sandbox
```

Device Farm 与 Sandbox 共同作为执行资源提供者，由 Execution Environment Management 统一管理和调度。

---

# 12. Key Technologies

| 技术领域 | 关键技术 | 说明 |
|----------|---------|------|
| 设备连接 | ADB over TCP / USB | 设备通信与远程控制 |
| 设备集群 | STF (Smartphone Test Farm) | 设备注册、远程操控、屏幕投射 |
| 屏幕采集 | Scrcpy / Minicap | 低延迟屏幕投射与截图 |
| 设备监控 | ADB shell / 自研 Agent | CPU/内存/温度/电池状态采集 |
| 设备恢复 | Factory Reset / ADB clear | 应用数据清理与系统状态恢复 |
| 网络管理 | Wi-Fi AP / USB Tethering | 设备网络接入与流量控制 |
| 远程安装 | ADB install / pm install | 应用包远程安装 |

---

# 13. Technical Metrics

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 设备在线率 | ≥ 98% | 真机设备在线可用时间占比 |
| 设备分配延迟 | ≤ 30 sec | 从请求到设备就绪的等待时间 |
| 设备恢复时间 | ≤ 5 min | 清理数据并恢复到可用状态 |
| 设备利用率 | ≥ 80% | 真机设备资源利用效率 |
| 并发设备支持 | ≥ 200 台 | 同时在线管理的设备数量 |
| 设备故障恢复时间 | ≤ 10 min | 异常设备检测和恢复时间 |
| 设备品牌覆盖 | ≥ 10 个 | Google、Samsung、Xiaomi 等 |
| Android 版本覆盖 | Android 10-15 | 覆盖市场主流版本 |

---

# 14. Summary

Device Farm 是平台真实设备资源的统一管理中心。

关键技术涵盖设备集群管理（STF）、远程控制（ADB/Scrcpy）、设备监控和快速恢复等。

技术指标从设备可用性、分配效率、利用率和覆盖度等维度定义了能力基线，确保 Device Farm 为 Analysis Engine 提供稳定、可靠的真实设备执行环境，同时与 Sandbox 共同构成平台执行资源体系的重要组成部分。
