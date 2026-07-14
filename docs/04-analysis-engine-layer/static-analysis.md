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

# 9. Summary

Static Analysis 是移动应用安全检测平台的重要分析能力。

它通过对应用安装包进行程序结构分析、依赖分析、代码逻辑分析和静态证据提取，全面理解应用的静态特征，并输出标准化静态分析结果。

这些分析结果既可独立支撑检测服务，也可与 Dynamic Analysis 协同工作，共同构建完整的程序分析能力，为 Detection Service Layer 提供可信的分析基础。
