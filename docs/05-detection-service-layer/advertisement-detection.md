# Advertisement Detection

---

# 1. Overview

Advertisement Detection（广告检测）负责识别移动应用中的异常广告行为，包括恶意广告投放、广告欺诈、违规广告展示、诱导点击以及影响用户体验的广告行为。

移动应用广告生态中，部分应用通过不透明广告行为获取非法收益，例如：

- 后台弹广告；
- 强制跳转；
- 诱导点击；
- 隐藏广告；
- 广告欺诈；
- 恶意推广。

这些行为可能不会直接表现为传统恶意代码，但会影响用户安全、应用生态质量以及广告业务公平性。

Advertisement Detection 基于：

- Static Analysis Result；
- Dynamic Analysis Result；
- Application Behavior；
- Advertisement Knowledge；

结合：

- 广告规则；
- SDK 信息；
- 用户反馈；
- AI 分析能力；

完成广告风险识别。

---

# 2. Design Objectives

Advertisement Detection 的设计目标包括：

- 识别恶意广告行为；
- 发现广告作弊行为；
- 检测违规广告展示；
- 分析广告 SDK 风险；
- 提升用户体验安全；
- 支撑应用生态治理。

---

# 3. Position in Architecture

Advertisement Detection 属于 Detection Service Layer。

整体关系：
