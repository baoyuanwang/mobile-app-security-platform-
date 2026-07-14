# Chapter 05 Detection Service Layer

---

# 1. Overview

Detection Service Layer（检测服务层）是移动应用安全检测平台的业务判断层，负责基于 Analysis Engine Layer 输出的标准化分析结果，结合安全规则、知识库、特征库以及 AI 能力，对应用安全风险进行识别和判断。

Analysis Engine Layer 解决的问题是：

> 应用程序做了什么。

Detection Service Layer 解决的问题是：

> 这些行为是否构成安全风险。

Detection Service Layer 面向具体安全场景构建检测能力，包括：

- 恶意软件检测；
- 隐私合规检测；
- 涉诈风险检测；
- 恶意广告检测；
- 仿冒侵权检测；
- 内容安全检测。

Detection Service Layer 不负责应用程序解析、不负责底层行为采集，而是消费分析结果，并结合安全知识和检测策略形成业务检测结论。

---

# 2. Design Objectives

Detection Service Layer 的设计目标包括：

- 面向业务安全场景提供检测能力；
- 基于分析结果完成风险判断；
- 支持规则、特征和 AI 多种检测方式；
- 输出可解释的检测结果；
- 支撑应用审核、风险治理和安全运营；
- 支持检测能力持续扩展。

---

# 3. Position in Overall Architecture

Detection Service Layer 位于 Analysis Engine Layer 和 Application Access Layer 之间。

整体架构如下：
