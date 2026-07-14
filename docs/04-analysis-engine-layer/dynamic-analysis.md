# Dynamic Analysis

---

# 1. Overview

Dynamic Analysis（动态分析）负责在受控执行环境中运行移动应用，通过采集应用运行过程中的行为数据，理解应用实际运行逻辑，并提取动态安全证据。

与 Static Analysis 关注应用程序静态结构不同，Dynamic Analysis 关注应用在真实执行过程中的行为表现，包括运行时调用、系统交互、网络通信、文件访问以及组件行为等。

Dynamic Analysis 依赖 Execution Environment Layer 提供的执行资源，包括：

- Device Farm；
- Sandbox。

通过运行时观测、行为采集和行为分析，Dynamic Analysis 能够发现仅通过静态分析无法识别的运行行为。

Dynamic Analysis 不负责安全风险判定，仅负责运行行为分析和动态证据提取。

---

# 2. Design Objectives

Dynamic Analysis 的设计目标包括：

- 在受控环境中安全运行应用；
- 获取应用真实运行行为；
- 采集运行时事件；
- 还原应用行为链路；
- 输出标准化动态分析结果；
- 与静态分析结果结合形成完整程序理解。

---

# 3. Position in Architecture

Dynamic Analysis 是 Analysis Engine Layer 的核心组成部分。

```text
                Analysis Engine Layer

                        │

        ┌───────────────┼────────────────┐

        ▼                                ▼

 Static Analysis               Dynamic Analysis

        │                                │

        └──────────────┬─────────────────┘

                       ▼

        Analysis Result Management
```

Dynamic Analysis 使用 Execution Environment Layer 提供的执行环境完成应用运行。

---

# 4. Responsibilities

Dynamic Analysis 负责理解应用运行过程中的行为。

主要职责包括：

---

## 4.1 Application Execution

负责控制应用运行过程。

包括：

- 应用安装；
- 应用启动；
- 生命周期控制；
- 场景执行；
- 运行状态管理。

Dynamic Analysis 负责分析任务执行，不负责底层执行资源管理。

---

## 4.2 Runtime Behavior Collection

采集应用运行过程中的行为数据。

包括：

- 系统调用；
- API 调用；
- 文件访问；
- 网络通信；
- 进程行为；
- 服务调用；
- IPC 通信；
- 权限使用。

---

## 4.3 Runtime Instrumentation

通过运行时观测能力获取应用内部和外部行为。

包括：

- 方法调用观测；
- 关键接口监控；
- 系统交互采集；
- 运行状态跟踪。

Runtime Instrumentation 是动态分析获取运行信息的重要手段。

---

## 4.4 Behavior Reconstruction

将离散的运行事件转换为具有语义的行为描述。

例如：

原始事件：

```
File Open

↓

Read Data

↓

Encrypt

↓

Network Send
```

经过行为重建：

```
读取本地数据

↓

加密处理

↓

上传远程服务器
```

Behavior Reconstruction 的目标是：

> 将底层运行事件转换为可理解的程序行为。

它属于程序理解能力，而不是风险判断能力。

---

## 4.5 Dynamic Evidence Extraction

从运行行为中提取动态安全证据。

例如：

- 网络通信目标；
- 敏感数据访问；
- 系统接口调用；
- 动态加载行为；
- 权限使用行为；
- 外部交互行为。

Dynamic Analysis 输出证据，不输出安全结论。

---

# 5. Analysis Workflow

Dynamic Analysis 的典型流程如下：

```text
Application Package

        │

        ▼

Execution Environment Allocation

        │

        ▼

Application Execution

        │

        ▼

Runtime Data Collection

        │

        ▼

Behavior Reconstruction

        │

        ▼

Dynamic Evidence Extraction

        │

        ▼

Dynamic Analysis Result
```

其中：

Execution Environment Allocation

由 Execution Environment Layer 完成。

Dynamic Analysis 负责分析过程。

---

# 6. Inputs and Outputs

## Input

Dynamic Analysis 输入包括：

- Application Package；
- Execution Environment；
- Analysis Configuration；
- Execution Scenario。

---

## Output

Dynamic Analysis 输出包括：

- Runtime Events；
- Behavior Information；
- Dynamic Evidence；
- Dynamic Analysis Result。

输出结果统一进入：

Analysis Result Management。

---

# 7. Relationship with Other Modules

## 与 Execution Environment Layer

Execution Environment Layer 提供：

- 真机设备；
- Sandbox 环境；
- 环境管理能力。

Dynamic Analysis 使用这些资源运行应用。

两者职责分离：

```
Execution Environment

负责：

在哪里运行


Dynamic Analysis

负责：

运行过程中分析什么
```

---

## 与 Static Analysis

Static Analysis 与 Dynamic Analysis 相互补充。

Static Analysis：

提供：

- 程序结构；
- 代码信息；
- 依赖关系。

Dynamic Analysis：

提供：

- 实际运行行为；
- 运行路径；
- 行为证据。

两者共同提升程序理解能力。

---

## 与 Detection Service Layer

Detection Service Layer 消费动态分析结果，并结合：

- 检测规则；
- AI 模型；
- 安全知识库；
- 风险策略；

完成业务风险判断。

Dynamic Analysis 不直接输出：

- 恶意软件；
- 涉诈应用；
- 隐私违规；
- 广告违规。

---

# 8. Design Principles

Dynamic Analysis 遵循以下原则。

---

## Behavior-Oriented

关注应用真实运行行为，而非代码结构。

---

## Controlled Execution

所有分析均在受控环境中完成。

保证安全性和可重复性。

---

## Evidence-Based

以运行行为证据作为核心输出。

---

## Static-Dynamic Collaboration

结合静态分析结果，提高动态分析效率和覆盖能力。

---

## Extensible

支持未来扩展：

- Native Runtime Analysis；
- Memory Analysis；
- Network Behavior Analysis；
- Advanced Instrumentation。

---

# 9. Key Technologies

| 技术领域 | 关键技术 | 说明 |
|----------|---------|------|
| 运行时插桩 | Frida / Xposed / LSPosed | 方法 Hook、参数捕获、返回值追踪 |
| 系统调用监控 | Strace / Seccomp-BPF | 系统调用级行为监控 |
| 网络流量采集 | Mitmproxy / tcpdump / PCAP | HTTPS 中间人代理、流量采集、协议解析 |
| 文件系统监控 | Inotify / Auditd | 文件创建/修改/删除实时监控 |
| 进程监控 | /proc / Process Monitor | 进程创建、信号、内存映射 |
| UI 自动化 | UIAutomator / Appium / Accessibility | 自动化 UI 操作与界面遍历 |
| 行为序列建模 | LSTM / Transformer / Markov Chain | 运行行为序列化与模式学习 |
| 内存分析 | Memory Dump / Forensics | 运行时内存快照与敏感数据检测 |
| 证书绕过 | SSL Unpinning / Certificate Bypass | HTTPS 证书校验绕过以捕获加密流量 |
| Root 检测绕过 | Root Hide / Magisk Hide | 绕过应用的 Root 检测逻辑 |

---

# 10. Technical Metrics

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 单应用动态分析时间 | ≤ 15 min | 含安装、运行、采集的完整动态分析耗时 |
| 并发执行能力 | ≥ 50 instances | 同时运行的应用实例数 |
| 行为采集覆盖率 | ≥ 90% | 关键行为采集覆盖度 |
| 网络流量捕获率 | ≥ 95% | HTTPS/TLS 流量解密捕获比例 |
| UI 自动化覆盖率 | ≥ 70% | 主要界面和功能路径的自动遍历覆盖 |
| 场景触发成功率 | ≥ 85% | 预定义分析场景的成功触发比例 |
| 行为重建准确率 | ≥ 90% | 运行事件转换为行为描述的准确度 |
| 动态证据完整率 | ≥ 85% | 可提取的动态证据完整度 |

---

# 11. Summary

Dynamic Analysis 是移动应用安全检测平台理解应用运行行为的重要能力。

它通过在受控执行环境中运行应用，采集运行时数据，并将底层运行事件转换为可理解的行为信息，最终输出标准化动态分析结果。

关键技术涵盖运行时插桩（Frida/Xposed）、系统调用监控（Strace）、网络流量采集（Mitmproxy）、UI 自动化（UIAutomator/Appium）和行为序列建模（LSTM/Transformer）等。

技术指标从分析性能、行为采集覆盖和证据质量等维度定义了能力基线，确保动态分析具备高效、全面、可信的运行行为理解能力。

Dynamic Analysis 与 Static Analysis 共同构成 Analysis Engine Layer 的程序理解能力，为 Detection Service Layer 提供全面的分析基础。

Dynamic Analysis 关注：

> 应用运行时做了什么。

而 Detection Service Layer 负责判断：

> 这些行为是否构成安全风险。
