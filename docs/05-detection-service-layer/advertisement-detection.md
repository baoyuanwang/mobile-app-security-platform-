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

与 Malware Detection 关注恶意代码不同，Advertisement Detection 更关注：

> 应用的广告行为是否违反平台规则、侵害用户权益或破坏广告生态公平性。

恶意广告行为通常具有以下特征：

- 应用可能不包含恶意代码；
- 广告 SDK 本身为合法组件，但被滥用；
- 违规行为发生在运行时，难以通过静态分析单独判定；
- 部分行为依赖特定触发条件（时间、网络、用户操作）。

因此，Advertisement Detection 需要结合：

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

Advertisement Detection 不负责程序分析本身，而是消费 Analysis Engine Layer 输出的程序事实和行为证据，并结合广告知识完成风险判定。

---

# 2. Design Objectives

Advertisement Detection 的设计目标包括：

- 识别恶意广告行为；
- 发现广告作弊行为；
- 检测违规广告展示；
- 分析广告 SDK 风险；
- 提升用户体验安全；
- 支撑应用生态治理；
- 支持广告行为模式发现；
- 提供可解释检测结果；
- 支撑应用审核与广告治理决策。

---

# 3. Position in Architecture

Advertisement Detection 属于 Detection Service Layer。

整体关系：

```
              Detection Service Layer


                       │


              Advertisement Detection


                       ▲


                       │


           Standardized Analysis Result


                       ▲


                       │


             Analysis Engine Layer
```

Advertisement Detection 消费 Analysis Engine Layer 提供的标准化分析结果，包括：

- 静态分析结果（SDK 指纹、广告组件识别、权限声明）；
- 动态分析结果（UI 状态变化、网络流量、运行时行为）；
- 行为证据（广告展示上下文、点击事件、后台活动）。

同时依赖 Security Knowledge Platform 提供的广告规则和 SDK 知识。

---

# 4. Detection Scope

Advertisement Detection 主要覆盖以下风险类型。

---

## 4.1 Background Ad Display

检测应用在不在前台时展示广告的行为。

后台广告展示是指应用退到后台或屏幕关闭后，仍然弹出广告窗口或展示广告内容的行为。

包括：

- 应用退至后台后弹出广告；
- 锁屏状态下展示广告；
- 其他应用前台时叠加广告窗口；
- 利用系统弹窗机制展示广告。

该行为严重干扰用户正常使用，且用户难以定位广告来源。

---

## 4.2 Forced Ad Redirect

检测应用强制打开浏览器或其他应用展示广告的行为。

强制跳转是指用户在未主动操作的情况下，被应用强制打开浏览器、应用商店或其他应用页面展示广告内容。

包括：

- 未经用户操作自动打开浏览器；
- 强制跳转至应用商店下载页；
- 利用 Deep Link / Scheme 强制打开其他应用；
- 通过剪贴板注入 URL 触发跳转；
- 利用系统通知点击自动跳转。

该行为剥夺用户选择权，可能将用户引导至恶意页面。

---

## 4.3 Click Fraud

检测应用中的广告点击欺诈行为。

点击欺诈是指通过技术手段模拟用户点击广告，骗取广告主费用的行为。

包括：

- 自动点击广告（无需用户操作）；
- 透明覆盖层触发点击（Invisible Ad Overlay）；
- 不可见广告区域触发点击；
- 模拟触摸事件注入点击；
- 利用 Accessibility Service 自动点击；
- 广告展示后短时间内自动触发点击事件。

该行为直接损害广告主利益，破坏广告生态公平性。

---

## 4.4 Hidden / Invisible Ads

检测应用中隐藏或不可见的广告行为。

隐藏广告是指广告被加载和展示但用户无法看到的行为，通常用于刷广告展示量骗取收益。

包括：

- 广告视图设置为 0x0 像素或 1x1 像素；
- 广告视图透明度设为 0；
- 广告视图被放置在屏幕可视区域之外；
- 广告视图被其他视图完全遮挡；
- 广告视图设置为 INVISIBLE / GONE 状态但仍然加载；
- 多层广告叠加但仅最底层可见。

该行为骗取广告展示费用，同时消耗用户网络和设备资源。

---

## 4.5 Ad Overlay / Deceptive Ads

检测应用中使用欺骗性广告覆盖或误导用户的广告行为。

包括：

- 广告覆盖应用内容区域，难以关闭；
- 关闭按钮过小或不可见；
- 倒计时关闭按钮（强制等待）；
- 伪装成系统对话框的广告；
- 伪装成应用功能按钮的广告；
- 误导性广告内容（虚假下载按钮、虚假更新提示）；
- 广告与应用界面高度混淆。

该行为通过欺骗手段诱导用户点击，损害用户体验和权益。

---

## 4.6 Lock Screen Ads

检测应用在锁屏界面展示广告的行为。

包括：

- 替换系统锁屏并展示广告；
- 在锁屏上方叠加广告窗口；
- 利用 Device Admin 权限展示锁屏广告；
- 注册锁屏广播后弹出广告；
- 使用 SYSTEM_ALERT_WINDOW 权限在锁屏上叠加广告。

该行为严重影响用户设备使用，属于高优先级违规行为。

---

## 4.7 Notification Bar Ads without Consent

检测应用未经用户同意在通知栏推送广告的行为。

包括：

- 未经用户授权推送广告通知；
- 利用前台服务（Foreground Service）展示广告通知；
- 通知内容为广告但伪装为功能通知；
- 频繁推送广告通知干扰用户；
- 无法关闭或取消的广告通知通道；
- 用户关闭通知后仍通过其他渠道推送。

该行为违反通知使用规范，骚扰用户。

---

## 4.8 Ad SDK Abuse

检测应用中广告 SDK 的滥用行为。

包括：

- 集成多个广告 SDK 进行广告堆叠；
- 广告 SDK 后台持续收集用户数据；
- 广告 SDK 未经授权获取设备标识；
- 广告 SDK 在无广告展示时仍保持网络通信；
- 广告 SDK 使用过时版本存在已知漏洞；
- 广告 SDK 未遵守平台隐私政策；
- 未声明的广告 SDK 隐蔽集成；
- 广告 SDK 间相互调用形成广告联盟链。

Ad SDK 识别能力由 Security Knowledge Platform 提供，Advertisement Detection 负责风险判断。

---

## 4.9 Adult / Inappropriate Ad Content

检测应用展示成人内容或不适广告的行为。

包括：

- 广告内容包含色情图片或文字；
- 广告链接指向成人网站；
- 广告内容包含赌博推广；
- 广告内容包含暴力或违法信息；
- 儿童应用中展示不当广告；
- 广告内容与应用年龄评级不匹配。

该行为违反内容安全规范，对特定用户群体（如未成年人）影响严重。

---

## 4.10 Ad Data Harvesting

检测应用通过广告行为收集用户数据的行为。

包括：

- 广告 SDK 超范围采集设备信息；
- 通过广告请求携带用户隐私数据；
- 广告追踪跨应用用户行为；
- 利用广告标识符（Advertising ID）持续追踪用户；
- 广告 SDK 采集位置、联系人等敏感信息；
- 广告数据传输未加密或传输至未知服务器；
- 广告 SDK 将用户数据共享至第三方。

该行为可能构成隐私违规，与 Privacy Detection 存在协作关系。

---

## 4.11 Reward Ad Fraud

检测应用中的激励广告欺诈行为。

激励广告欺诈是指通过技术手段模拟激励广告观看或交互行为，骗取广告收益的行为。

包括：

- 自动模拟激励广告观看完成；
- 伪造广告观看回调；
- 绕过激励广告展示直接触发奖励；
- 多开/分身应用重复领取激励奖励；
- 修改本地时间绕过激励广告冷却期；
- 利用 Hook 框架伪造广告回调结果。

该行为损害广告主利益，破坏激励广告生态。

---

# 5. Detection Capability

Advertisement Detection 主要基于以下能力完成判断。

---

## 5.1 Ad SDK Identification and Fingerprinting

利用 Security Knowledge Platform 提供的广告 SDK 知识和 Analysis Engine 提供的静态分析结果。

包括：

- SDK 包名和类名匹配；
- SDK 资源文件指纹识别；
- SDK Manifest 特征识别；
- SDK 版本识别；
- SDK 网络域名特征匹配；
- SDK 加固/混淆对抗识别。

用于识别：

- 应用集成了哪些广告 SDK；
- 广告 SDK 版本是否存在已知风险；
- 是否存在未声明的隐蔽广告 SDK。

---

## 5.2 UI State Machine Analysis

利用动态分析结果，通过 UI 状态机建模分析广告展示上下文。

包括：

- Activity / Fragment 生命周期状态追踪；
- 应用前后台状态检测；
- 广告 View 层级分析；
- 广告展示时机与上下文关联；
- 用户交互事件与广告展示时序分析；
- 广告关闭行为分析。

用于判断：

- 广告是否在前台正常展示；
- 广告是否在后台违规弹出；
- 广告关闭按钮是否可操作；
- 广告展示是否具有欺骗性。

---

## 5.3 Network Traffic Analysis

利用动态分析结果，分析广告网络通信行为。

包括：

- 广告请求域名和目标识别；
- 广告 SDK 网络通信频率分析；
- 广告请求参数和内容检查；
- 广告数据传输量异常检测；
- 广告数据传输加密状态检查；
- 广告请求与展示行为关联分析。

用于发现：

- 广告 SDK 后台持续通信；
- 广告请求携带敏感数据；
- 广告数据传输至未知服务器；
- 广告网络行为与展示行为不一致。

---

## 5.4 Runtime Behavior Monitoring

利用动态分析结果，监控运行时广告行为。

包括：

- 广告展示时间与频率统计；
- 广告点击事件检测与来源分析；
- 后台广告行为监控；
- 广告触发的页面跳转检测；
- 广告相关的系统广播监听；
- 广告触发的应用启动行为；
- 广告相关权限使用监控。

用于发现：

- 自动点击行为；
- 后台广告展示；
- 强制跳转行为；
- 锁屏广告行为。

---

## 5.5 Policy Based Rule Detection

结合广告规则和平台策略。

例如：

```
Background Ad Display

+

No User Interaction Evidence

+

Ad View Visible

↓

Malicious Ad Behavior
```

策略规则覆盖：

- 广告展示位置规范；
- 广告关闭按钮规范；
- 广告频率限制；
- 后台广告禁止规则；
- 通知栏广告使用规范；
- 锁屏广告禁止规则；
- 儿童应用广告限制。

---

## 5.6 AI Assisted Ad Behavior Classification

利用 AI 能力辅助：

- 广告行为语义理解；
- 广告 UI 布局分类（欺骗性/正常）；
- 广告内容合规判断；
- 广告行为序列模式识别；
- 未知广告行为异常检测；
- 风险解释与证据关联。

AI 不替代广告规则，而用于增强分析能力，特别是：

- 复杂广告欺骗场景判断；
- 新型广告违规行为发现；
- 广告内容与上下文关联分析。

---

# 6. Detection Workflow

典型流程：

```
Application


    │


    ▼


Analysis Engine Result


    │


    ▼


Advertisement Detection


    ├── Ad SDK Identification


    ├── UI State Machine Analysis


    ├── Network Traffic Analysis


    ├── Runtime Behavior Monitoring


    ├── Policy Based Rule Detection


    └── AI Assisted Classification


    │


    ▼


Advertisement Detection Result
```

详细步骤：

1. **Ad SDK Identification** — 识别应用集成的广告 SDK，建立广告组件清单；

2. **UI State Machine Analysis** — 基于动态分析结果，构建 UI 状态变迁模型，分析广告展示上下文；

3. **Network Traffic Analysis** — 分析广告相关网络通信，识别异常数据传输和未知广告服务器；

4. **Runtime Behavior Monitoring** — 监控广告运行时行为，检测自动点击、后台弹窗、强制跳转等违规行为；

5. **Policy Based Rule Detection** — 将分析结果与广告规则匹配，判断是否违反平台策略；

6. **AI Assisted Classification** — 利用 AI 模型对复杂场景和未知行为进行分类判断；

7. **Result Aggregation** — 汇总各维度检测结果，生成广告风险报告。

---

# 7. Key Technologies

| 技术领域 | 关键技术 | 说明 |
|----------|---------|------|
| UI 状态检测 | UIAutomator | Android UI 自动化框架，用于检测应用界面状态、广告 View 层级和交互行为 |
| 广告 SDK 指纹识别 | Ad SDK Fingerprinting | 基于包名、类名、资源特征和 Manifest 特征的广告 SDK 识别与版本判定 |
| 网络流量分析 | Network Traffic Analysis | 广告网络通信分析，包括域名识别、请求频率统计、数据内容检查和传输量异常检测 |
| 运行时监控 | Frida | 运行时方法拦截与行为监控，用于检测自动点击、后台广告行为和广告回调伪造 |
| 广告 SDK 签名 | YARA | 广告 SDK 特征规则匹配，用于识别已知广告 SDK 和变种 |
| 广告行为分类 | CNN | 卷积神经网络，用于广告 UI 布局分类、广告行为模式识别和异常行为检测 |
| 行为画像 | Behavior Profiling | 广告行为序列建模与画像构建，用于建立正常广告行为基线和检测异常偏离 |

---

# 8. Technical Metrics

## 8.1 检测能力指标

| 指标名称 | 目标值 | 说明 |
|----------|--------|------|
| 恶意广告行为检出率 | ≥ 90% | 违规广告行为的识别覆盖度，包括后台弹窗、强制跳转、点击欺诈等 |
| 广告 SDK 识别覆盖率 | ≥ 95% | 主流广告 SDK 的指纹识别覆盖，包括国内主流和国际主流广告 SDK |
| 误报率 | ≤ 1% | 正常广告行为被误判为违规的比例 |
| 隐藏/不可见广告识别率 | ≥ 90% | 对隐藏广告视图、透明覆盖层等不可见广告的识别准确度 |
| 点击欺诈识别率 | ≥ 85% | 自动点击、透明覆盖点击等欺诈行为的识别准确度 |
| 广告内容合规识别率 | ≥ 85% | 成人/不当广告内容的识别准确度 |

## 8.2 性能指标

| 指标名称 | 目标值 | 说明 |
|----------|--------|------|
| 广告行为检测延迟 | ≤ 5 min | 单应用广告行为检测耗时（基于已有分析结果） |
| 端到端检测延迟 | ≤ 30 min | 从应用提交到广告检测报告输出的总耗时（含分析引擎耗时） |
| 日检测吞吐量 | ≥ 5,000 apps/day | 广告检测模块日处理应用数量 |
| 广告 SDK 指纹匹配延迟 | ≤ 10 sec | 单应用广告 SDK 指纹识别耗时 |

## 8.3 可靠性指标

| 指标名称 | 目标值 | 说明 |
|----------|--------|------|
| 检测结果可追溯率 | 100% | 所有广告风险判定可回溯至原始行为证据 |
| 规则生效延迟 | ≤ 24 hours | 新广告规则从发布到检测生效的时间 |
| 检测任务成功率 | ≥ 99.5% | 广告检测任务正常完成率 |

---

# 9. Input and Output

## Input

来自 Analysis Engine：

- Static Analysis Result；
- Dynamic Analysis Result；
- UI State Information；
- Ad View Hierarchy；
- Network Behavior；
- Runtime Behavior Evidence；
- Permission Information。

来自 Security Knowledge Platform：

- Ad Rules（广告展示规范）；
- Ad SDK Knowledge（SDK 指纹、版本、行为特征）；
- Ad Blacklist（违规广告域名、SDK 黑名单）；
- Compliance Requirements（平台广告合规要求）。

来自 External Signal：

- User Feedback（用户广告投诉）；
- Ad Content Sample（广告内容样本）。

---

## Output

输出：

```
Advertisement Detection Result


├── Ad Risk Type（广告风险类型）


├── Ad SDK Information（涉及的广告 SDK）


├── Ad Behavior Description（广告行为描述）


├── Violation Category（违规类别）


├── Evidence（行为证据）


├── Risk Level（风险等级）


├── Reason（判定原因）


└── Recommendation（处置建议）
```

---

# 10. Relationship with Other Modules

---

## 10.1 Relationship with Analysis Engine Layer

Analysis Engine 提供：

```
Application Ad Behavior Evidence
```

例如：

```
Ad SDK Loaded

Ad View Displayed (Background)

Auto Click Event Detected

Ad Network Request with Device ID
```

Advertisement Detection 判断：

```
是否违反广告规则
```

---

## 10.2 Relationship with Security Knowledge Platform

Security Knowledge Platform 提供：

- 广告 SDK 知识（指纹、版本、行为特征）；
- 广告展示规则；
- 违规广告黑名单；
- 合规要求。

Advertisement Detection 基于这些知识完成判断。

---

## 10.3 Relationship with Privacy Detection

Advertisement Detection 与 Privacy Detection 存在协作关系：

- Ad Data Harvesting 行为可能同时构成隐私违规；
- 广告 SDK 超范围数据采集由 Privacy Detection 负责数据合规判断；
- Advertisement Detection 负责广告行为违规判断；
- 两者共享广告 SDK 分析结果和行为证据。

---

## 10.4 Relationship with Malware Detection

Advertisement Detection 与 Malware Detection 存在协作关系：

- 部分恶意软件伪装为广告 SDK；
- 广告 SDK 漏洞可能被恶意代码利用；
- Advertisement Detection 识别广告行为违规，Malware Detection 识别恶意代码行为；
- 两者共享 SDK 分析结果。

---

## 10.5 Relationship with Security Operation Platform

Advertisement Detection 输出：

- 风险类型；
- 违规证据；
- 处置建议。

Security Operation Platform 根据结果：

- 人工审核；
- 整改通知；
- 广告治理；
- 应用下架。

---

# 11. Design Principles

---

## Behavior and Policy Combination

广告风险判断必须结合：

- 实际广告行为；
- 平台广告规则。

仅发现广告 SDK 集成不构成违规，需要行为证据支撑。

---

## Evidence Driven

每一个广告风险必须能够追溯到：

- 广告展示行为；
- 用户交互状态；
- 网络通信证据；
- UI 状态证据。

---

## Context Awareness

广告行为是否违规依赖上下文判断：

- 广告展示时应用是否在前台；
- 用户是否主动触发广告展示；
- 广告内容是否与用户预期一致；
- 广告行为是否超出正常范围。

---

## SDK Behavior Focus

Advertisement Detection 关注：

- 广告 SDK 的实际运行行为；

而不是：

- 广告 SDK 的声明功能。

SDK 声明正常但实际行为违规，仍然属于检测范围。

---

## Explainable Detection

输出：

- 展示了什么广告；
- 如何展示；
- 在什么上下文展示；
- 为什么判定违规。

---

## Continuous Rule Evolution

支持随着：

- 平台广告政策变化；
- 广告 SDK 版本更新；
- 新型广告违规行为出现；
- 用户投诉反馈；

持续更新检测能力。

---

# 12. Summary

Advertisement Detection 是移动应用安全检测平台的重要业务检测能力。

它基于 Analysis Engine Layer 提供的 UI 状态、网络流量、运行时行为和程序分析结果，结合广告规则、SDK 知识和 AI 能力，对应用的广告行为进行合规与安全判断。

Advertisement Detection 关注：

> 应用的广告行为是否违规、是否侵害用户权益、是否破坏广告生态公平性。

而不是：

> 应用是否包含恶意代码。

通过 Ad SDK 指纹识别、UI 状态机分析、网络流量分析、运行时行为监控、策略规则检测和 AI 辅助分类相结合，可以有效发现移动应用中的恶意广告行为、广告欺诈、违规展示等问题，支撑应用审核、广告治理和生态治理。
