# Chapter 04 Analysis Engine Layer

---

# 1. Overview

Analysis Engine Layer（分析引擎层）是移动应用安全检测平台的核心分析层，负责对移动应用程序进行程序理解（Program Understanding），输出标准化分析结果，为 Detection Service Layer 提供统一、可信的分析数据。

Analysis Engine Layer 通过静态分析（Static Analysis）和动态分析（Dynamic Analysis）两种分析方式，从应用程序中提取程序结构、运行行为及安全证据，并形成统一的分析结果。

Analysis Engine Layer 不负责安全风险判定，不直接输出恶意软件、隐私违规、广告违规等业务结论，而是专注于程序分析本身，为上层检测服务提供分析能力支撑。

---

# 2. Design Objectives

Analysis Engine Layer 的设计目标包括：

- 提供统一的程序分析能力；
- 支持静态分析与动态分析协同工作；
- 提取应用程序结构信息和运行行为；
- 输出标准化分析结果；
- 支持多种检测服务共享分析结果；
- 与执行环境解耦；
- 与业务检测逻辑解耦。

Analysis Engine Layer 关注的是**程序分析（Analysis）**，而不是**安全决策（Decision）**。

---

# 3. Position in Overall Architecture

Analysis Engine Layer 位于 Execution Environment Layer 与 Detection Service Layer 之间。

```text
┌──────────────────────────────────────────────┐
│        Application Access Layer              │
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

Execution Environment Layer 提供应用运行环境。

Analysis Engine Layer 对应用进行分析。

Detection Service Layer 消费分析结果，并结合规则、知识库、AI 等能力完成业务判定。

---

# 4. Responsibilities

Analysis Engine Layer 承担整个检测平台的程序分析职责。

主要包括以下几个方面。

## 4.1 Static Analysis

通过分析应用安装包，在不运行应用程序的情况下理解程序结构。

主要分析内容包括：

- 应用基本信息；
- 应用组件；
- 权限声明；
- 代码结构；
- 程序依赖；
- SDK 使用情况；
- 程序控制关系；
- 程序数据关系；
- 静态安全证据。

---

## 4.2 Dynamic Analysis

通过运行应用程序，分析应用实际运行行为。

主要分析内容包括：

- 生命周期行为；
- 系统调用；
- 文件访问；
- 网络通信；
- IPC 通信；
- 权限使用；
- SDK 行为；
- 用户交互行为；
- 动态安全证据。

---

## 4.3 Analysis Result Management

统一管理静态分析和动态分析输出。

主要负责：

- 分析结果标准化；
- 证据关联；
- 数据统一组织；
- 分析结果输出；
- 为 Detection Service Layer 提供统一分析接口。

Analysis Result Management 不负责风险判定，仅负责分析结果管理。

---

# 5. Architecture

Analysis Engine Layer 由三个核心模块组成。

```text
Analysis Engine Layer

├── Static Analysis
├── Dynamic Analysis
└── Analysis Result Management
```

三个模块之间的关系如下：

```text
                  Application Package
                           │
          ┌────────────────┴────────────────┐
          ▼                                 ▼
  Static Analysis                  Dynamic Analysis
          │                                 │
          └──────────────┬──────────────────┘
                         ▼
           Analysis Result Management
                         │
                         ▼
        Standardized Analysis Result
                         │
                         ▼
             Detection Service Layer
```

静态分析与动态分析相互独立，可以单独执行，也可以协同完成程序分析。

Analysis Result Management 对两类分析结果进行统一组织和输出。

---

# 6. Inputs and Outputs

## Input

Analysis Engine Layer 的输入包括：

- Application Package（APK、AAB、HAP 等）；
- Execution Environment（由 Execution Environment Layer 提供）；
- Analysis Configuration（分析配置）；
- Analysis Task（分析任务）。

---

## Output

Analysis Engine Layer 输出：

- Static Analysis Result；
- Dynamic Analysis Result；
- Program Representation；
- Analysis Evidence；
- Standardized Analysis Result。

Analysis Engine Layer 不输出：

- 风险等级；
- 恶意软件结论；
- 隐私违规结论；
- 广告违规结论；
- 涉诈结论。

所有业务判定均由 Detection Service Layer 完成。

---

# 7. Interaction with Other Modules

Analysis Engine Layer 与上下层之间保持清晰的职责分离。

## 与 Execution Environment Layer

Execution Environment Layer 提供：

- 真机资源；
- 沙箱环境；
- 执行环境管理。

Analysis Engine Layer 使用这些资源完成程序分析，但不负责执行环境管理。

---

## 与 Detection Service Layer

Detection Service Layer 消费 Analysis Engine 输出的标准化分析结果。

Detection Service Layer 可以结合：

- 检测规则；
- AI 模型；
- 安全知识库；
- 特征库；
- 风险画像；

形成最终安全检测结论。

Analysis Engine Layer 不参与业务决策。

---

# 8. Design Principles

Analysis Engine Layer 的设计遵循以下原则。

## Analysis-Oriented

专注程序分析，不承担业务风险判定职责。

---

## Static and Dynamic Collaboration

静态分析与动态分析相互补充。

静态分析提供程序结构理解。

动态分析提供运行行为理解。

两者共同构成完整的程序分析能力。

---

## Standardized Output

所有分析结果均采用统一的数据模型输出。

屏蔽不同分析方式之间的数据差异，为上层检测服务提供统一接口。

---

## Decoupling

Analysis Engine 与 Execution Environment、Detection Service 解耦。

能够独立演进分析能力，而不影响资源管理或业务检测逻辑。

---

## Scalability

支持新增分析能力。

未来可以扩展：

- Native Analysis；
- Memory Analysis；
- Firmware Analysis；
- Hybrid Analysis；

而无需修改整体架构。

---

# 9. Chapter Organization

本章节包括以下内容：

```text
04-analysis-engine-layer/

├── README.md
├── static-analysis.md
├── dynamic-analysis.md
└── analysis-result-management.md
```

各模块职责如下：

| Module | Responsibility |
|----------|----------------|
| Static Analysis | 对应用安装包进行静态程序分析，提取程序结构和静态证据。 |
| Dynamic Analysis | 在执行环境中运行应用，分析运行时行为并提取动态证据。 |
| Analysis Result Management | 统一管理静态分析和动态分析结果，输出标准化分析结果。 |

---

# 10. Key Technologies

Analysis Engine Layer 涉及的关键技术如下。

## 10.1 静态分析关键技术

| 技术领域 | 关键技术 | 说明 |
|----------|---------|------|
| APK 解包 | Apktool / AAPT2 | 资源解包、Manifest 解析、DEX 提取 |
| DEX 反编译 | JADX / JEB / Procyon | Dalvik 字节码反编译为 Java 源码 |
| Native 反汇编 | Ghidra / IDA Pro / Capstone | SO 文件反汇编、ARM/x86 指令分析 |
| 中间表示构建 | Soot / Jimple / Smali IR | 统一中间表示，支撑后续数据流与控制流分析 |
| 控制流分析 | CFG Construction / Call Graph | 控制流图构建、方法调用图构建 |
| 数据流分析 | Taint Analysis / Data Flow Analysis | 污点传播分析、数据依赖分析 |
| 组件间通信分析 | ICC Analyzer / Intent Analysis | Activity/Service/Receiver/Provider 间通信路径分析 |
| 代码相似性分析 | SSDeep / TLSH / SimHash / BinDiff | 恶意代码同源分析、重打包检测、家族聚类 |
| 字符串提取 | String Extraction / Deobfuscation | 加密字符串解密、混淆字符串还原 |
| 加固对抗 | DEX Dump / Memory Dump / Static Unpacking | 加壳应用脱壳、DEX 内存dump、静态拆包 |
| SDK 识别 | SDK Fingerprinting / Library Identification | 第三方 SDK 指纹提取与版本识别 |

## 10.2 动态分析关键技术

| 技术领域 | 关键技术 | 说明 |
|----------|---------|------|
| 运行时插桩 | Frida / Xposed / LSPosed | 运行时方法 Hook、参数捕获、返回值追踪 |
| 系统调用监控 | Strace / Seccomp-BPF | 系统调用级行为监控，覆盖文件/网络/进程操作 |
| 网络流量采集 | Mitmproxy / tcpdump / Pcap | HTTPS 中间人代理、流量采集、协议解析 |
| 文件系统监控 | Inotify / Auditd | 文件创建/修改/删除实时监控 |
| 进程行为监控 | /proc 文件系统 / Process Monitor | 进程创建、信号、内存映射监控 |
| UI 自动化 | UIAutomator / Appium / Accessibility | 自动化 UI 操作、界面遍历、场景触发 |
| 行为序列建模 | LSTM / Transformer / Markov Chain | 运行行为序列化、行为模式学习 |
| 内存分析 | Memory Dump / Memory Forensics | 运行时内存快照、敏感数据内存泄露检测 |

## 10.3 分析结果管理关键技术

| 技术领域 | 关键技术 | 说明 |
|----------|---------|------|
| 数据标准化 | JSON Schema / Protocol Buffers | 统一分析结果数据模型与序列化 |
| 证据关联 | Graph Database / Entity Resolution | 静态证据与动态证据的关联分析 |
| 结果存储 | Elasticsearch / MongoDB / Object Storage | 分析结果持久化、全文检索、对象存储 |
| 结果查询 | GraphQL / REST API | 统一分析结果查询接口 |

---

# 11. Technical Metrics

Analysis Engine Layer 的关键技术指标如下。

## 11.1 静态分析指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 单应用静态分析时间 | ≤ 3 min | 标准 APK（≤ 100MB）全量静态分析耗时 |
| 批量吞吐量 | ≥ 200 apps/hour | 并发静态分析处理能力 |
| APK 解包成功率 | ≥ 99.9% | 标准及加固 APK 的解包成功率 |
| 反编译覆盖率 | ≥ 95% | DEX 代码反编译为可读源码的比例 |
| 控制流图构建率 | ≥ 90% | 可构建有效 CFG 的方法比例 |
| 数据流追踪覆盖率 | ≥ 85% | 敏感 API 数据流可追踪到的比例 |
| SDK 识别覆盖率 | ≥ 95% | 主流 SDK 的指纹识别覆盖度 |
| 加固对抗成功率 | ≥ 80% | 主流加固方案（360、腾讯、梆梆等）的脱壳成功率 |

## 11.2 动态分析指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 单应用动态分析时间 | ≤ 15 min | 含安装、运行、采集的完整动态分析耗时 |
| 并发执行能力 | ≥ 50 instances | 同时运行的应用实例数 |
| 行为采集覆盖率 | ≥ 90% | 关键行为（网络、文件、API）的采集覆盖度 |
| 网络流量捕获率 | ≥ 95% | HTTPS/TLS 流量的解密捕获比例 |
| UI 自动化覆盖率 | ≥ 70% | 主要界面和功能路径的自动遍历覆盖 |
| 场景触发成功率 | ≥ 85% | 预定义分析场景的成功触发比例 |

## 11.3 分析结果指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 结果标准化率 | 100% | 所有分析结果符合统一数据模型 |
| 证据关联完整率 | ≥ 90% | 静态证据与动态证据可关联的比例 |
| 结果查询延迟 | ≤ 500 ms | 分析结果查询响应时间 |
| 结果持久化可靠性 | 100% | 分析结果不丢失 |

---

# 12. Summary

Analysis Engine Layer 是移动应用安全检测平台的程序分析核心。

它通过静态分析和动态分析两种方式，对移动应用进行全面的程序理解，并输出统一的分析结果，为 Detection Service Layer 提供可信、完整的分析数据。

关键技术涵盖：静态分析侧的 APK 解包、DEX 反编译、中间表示构建、控制流/数据流分析、代码相似性分析、加固对抗；动态分析侧的运行时插桩（Frida）、系统调用监控、网络流量采集、UI 自动化、行为序列建模；以及分析结果管理侧的数据标准化、证据关联和结果持久化。

技术指标从分析性能、覆盖率和可靠性三个维度定义了能力基线，确保平台具备高效、全面、可信的程序分析能力。

Analysis Engine Layer 不负责安全风险判定，而是作为平台统一的分析能力中心，实现程序分析与业务检测解耦，为平台持续扩展新的分析能力和检测场景奠定基础。
