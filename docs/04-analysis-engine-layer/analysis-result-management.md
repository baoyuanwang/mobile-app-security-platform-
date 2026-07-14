# Analysis Result Management

---

# 1. Overview

Analysis Result Management（分析结果管理）负责统一管理 Analysis Engine Layer 产生的分析结果，并向 Detection Service Layer 提供标准化的分析数据接口。

由于移动应用安全检测需要同时结合静态分析和动态分析能力，不同分析引擎产生的数据结构、粒度和表达方式存在较大差异，因此需要通过统一的结果管理机制，对分析结果进行组织、关联和标准化。

Analysis Result Management 的核心目标是：

> 将不同分析引擎产生的原始分析结果，转换为统一、可消费的 Analysis Result。

Analysis Result Management 不负责安全判断，不负责风险评级，仅负责分析结果管理和能力输出。

---

# 2. Design Objectives

Analysis Result Management 的设计目标包括：

- 统一静态和动态分析结果；
- 建立标准化分析结果模型；
- 管理分析证据；
- 支持多检测服务共享分析结果；
- 提供稳定的数据接口；
- 解耦分析引擎和业务检测逻辑。

---

# 3. Position in Architecture

Analysis Result Management 位于 Analysis Engine Layer 内部。

整体关系如下：

```text
                Analysis Engine Layer


        ┌───────────────────────────┐
        │                           │
        ▼                           ▼

 Static Analysis           Dynamic Analysis

        │                           │

        └──────────────┬────────────┘

                       ▼

        Analysis Result Management

                       │

                       ▼

          Standardized Analysis Result

                       │

                       ▼

          Detection Service Layer
