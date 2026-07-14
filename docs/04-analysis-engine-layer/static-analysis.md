# Static Analysis

---

# 1. Overview

Static Analysis（静态分析）负责在**不运行应用程序**的情况下，对应用安装包进行程序分析，理解应用的组成结构、代码逻辑、资源配置及依赖关系，并提取安全相关的静态证据。

静态分析是 Analysis Engine Layer 的基础能力，也是整个检测平台获取程序结构信息的重要来源。

相比动态分析，静态分析具有分析速度快、覆盖范围广、结果稳定、可批量处理等特点，能够全面描述应用的静态特征，为 Detection Service Layer 提供丰富的程序分析结果。

Static Analysis 不负责安全风险判定，仅负责程序理解和静态证据提取。

---

# 2. Design Objectives

Static Analysis 的设计目标包括：

- 理解应用程序结构；
- 建立程序静态表示；
- 提取安全相关静态信息；
- 输出标准化静态分析结果；
- 为动态分析提供程序上下文；
- 为 Detection Service Layer 提供静态分析能力。

---

# 3. Position in Architecture

Static Analysis 是 Analysis Engine Layer 的组成部分。

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

Static Analysis 可以独立完成，也可以与 Dynamic Analysis 协同工作。

静态分析无需运行应用程序。

---

# 4. Responsibilities

Static Analysis 负责理解应用程序的静态组成。

主要职责包括：

## 4.1 Application Structure Analysis

分析应用整体结构。

包括：

- 安装包结构；
- 应用组件；
- Manifest 信息；
- 权限声明；
- 资源文件；
- 签名信息；
- 安装配置。

---

## 4.2 Program Structure Analysis

理解程序代码结构。

包括：

- 类；
- 方法；
- 字段；
- 模块；
- 包结构；
- 代码组织关系。

---

## 4.3 Dependency Analysis

分析程序依赖关系。

包括：

- SDK；
- Library；
- Framework；
- Native Library；
- 第三方组件。

---

## 4.4 Code Logic Analysis

分析程序逻辑关系。

例如：

- 方法调用；
- 数据流；
- 控制关系；
- 生命周期关系；
- 组件通信。

这些分析结果用于建立程序静态表示（Program Representation）。

---

## 4.5 Static Evidence Extraction

提取安全检测所需静态证据。

例如：

- 权限使用；
- API 调用；
- SDK 特征；
- 字符串常量；
- URL；
- IP；
- 域名；
- 配置项；
- 加固信息；
- 混淆信息。

Static Analysis 不判断风险，仅负责提取证据。

---

# 5. Analysis Workflow

Static Analysis 的典型分析流程如下：

```text
Application Package

        │

        ▼

Package Parsing

        │

        ▼

Application Structure Analysis

        │

        ▼

Program Structure Analysis

        │

        ▼

Dependency Analysis

        │

        ▼

Code Logic Analysis

        │

        ▼

Static Evidence Extraction

        │

        ▼

Static Analysis Result
```

整个流程无需运行应用程序。

---

# 6. Inputs and Outputs

## Input

Static Analysis 的输入包括：

- APK；
- AAB；
- HAP（未来扩展）；
- Analysis Configuration。

---

## Output

输出包括：

- Application Metadata；
- Program Representation；
- Dependency Information；
- Static Evidence；
- Static Analysis Result。

这些结果统一交由 Analysis Result Management 管理。

---

# 7. Interaction with Other Modules

## 与 Dynamic Analysis

Static Analysis 为 Dynamic Analysis 提供程序上下文。

例如：

- 应用组件；
- 方法信息；
- SDK 信息；
- 程序结构；
- 关键入口；
- 配置数据。

Dynamic Analysis 可结合这些信息，提高运行时分析效率。

---

## 与 Analysis Result Management

Static Analysis 将所有分析结果输出至 Analysis Result Management。

Analysis Result Management 负责统一组织、标准化和输出分析结果。

---

## 与 Detection Service Layer

Detection Service Layer 消费静态分析结果，用于：

- 恶意软件检测；
- 隐私合规检测；
- 广告检测；
- 涉诈检测；
- SDK 检测；
- 内容检测。

Static Analysis 本身不参与检测决策。

---

# 8. Design Principles

Static Analysis 遵循以下设计原则。

## Comprehensive

尽可能完整地描述应用程序的静态信息。

---

## Non-Intrusive

整个分析过程无需运行应用程序，不影响应用行为。

---

## Structured Representation

将复杂程序统一表示为标准化程序模型，便于后续分析与检测。

---

## Evidence-Oriented

所有分析均围绕证据提取展开。

分析结果能够直接支撑 Detection Service Layer 的检测能力。

---

## Extensible

支持新增分析能力，例如：

- Native Code Analysis；
- Resource Analysis；
- Bytecode Analysis；
- Configuration Analysis。

无需影响整体架构。

---

# 9. Key Technologies

| 技术领域 | 关键技术 | 说明 |
|----------|---------|------|
| APK 解包 | Apktool / AAPT2 | 资源解包、AndroidManifest.xml 解析、DEX 提取 |
| DEX 反编译 | JADX / JEB / Procyon | Dalvik 字节码反编译为可读 Java 源码 |
| Native 反汇编 | Ghidra / IDA Pro / Capstone | SO 文件反汇编、ARM/x86 指令集分析 |
| 中间表示 | Soot / Jimple / Smali IR | 统一中间表示构建，支撑后续分析 |
| 控制流分析 | CFG / Call Graph / ICC Analysis | 控制流图、调用图、组件间通信分析 |
| 数据流分析 | Taint Analysis / Data Flow Analysis | 污点传播、数据依赖、变量定义-使用链 |
| 代码相似性 | SSDeep / TLSH / SimHash / BinDiff | 代码/二进制相似性比较、同源分析 |
| 字符串分析 | String Extraction / Deobfuscation | 加密字符串解密、混淆还原 |
| 加固对抗 | DEX Dump / Static Unpacking / Memory Dump | 加壳应用脱壳、DEX 提取 |
| SDK 识别 | SDK Fingerprinting / Maven Index | 第三方 SDK 指纹提取与版本识别 |
| 证书分析 | Certificate Parsing / Signature Verification | 签名证书解析、开发者身份关联 |

---

# 10. Technical Metrics

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 单应用分析时间 | ≤ 3 min | 标准 APK（≤ 100MB）全量静态分析耗时 |
| 批量吞吐量 | ≥ 200 apps/hour | 并发静态分析处理能力 |
| APK 解包成功率 | ≥ 99.9% | 标准及加固 APK 的解包成功率 |
| 反编译覆盖率 | ≥ 95% | DEX 代码反编译为可读源码的比例 |
| 控制流图构建率 | ≥ 90% | 可构建有效 CFG 的方法比例 |
| 数据流追踪覆盖率 | ≥ 85% | 敏感 API 数据流可追踪比例 |
| SDK 识别覆盖率 | ≥ 95% | 主流 SDK 的指纹识别覆盖度 |
| 加固对抗成功率 | ≥ 80% | 主流加固方案的脱壳成功率 |
| 代码相似性计算延迟 | ≤ 30 sec | 单应用代码相似性比对耗时 |

---

# 11. Summary

Static Analysis 是移动应用安全检测平台的重要分析能力。

它通过对应用安装包进行程序结构分析、依赖分析、代码逻辑分析和静态证据提取，全面理解应用的静态特征，并输出标准化静态分析结果。

关键技术涵盖 APK 解包（Apktool）、反编译（JADX/JEB）、中间表示（Soot/Jimple）、控制流/数据流分析、代码相似性（SSDeep/BinDiff）、加固对抗和 SDK 识别等。

技术指标从分析性能、覆盖率和对抗能力等维度定义了能力基线，确保静态分析具备高效、全面、可靠的程序理解能力。

这些分析结果既可独立支撑检测服务，也可与 Dynamic Analysis 协同工作，共同构建完整的程序分析能力，为 Detection Service Layer 提供可信的分析基础。
