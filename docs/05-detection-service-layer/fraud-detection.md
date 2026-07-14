# Fraud Detection

---

# 1. Overview

Fraud Detection（涉诈检测）负责识别移动应用中的诈骗风险和黑灰产活动，包括虚假服务、诈骗引流、恶意诱导、非法获利以及其他利用应用生态实施欺诈行为的应用。

与 Malware Detection 关注恶意代码不同，Fraud Detection 更关注：

> 应用是否被用于实施诈骗或欺诈活动。

涉诈应用可能：

- 不包含明显恶意代码；
- 使用正常技术实现；
- 通过业务流程和用户交互实施欺诈。

这意味着涉诈应用在技术层面可能完全合法，但其业务逻辑、内容呈现和交互流程被用于欺骗用户。例如：

- 仿冒正规金融机构的投资理财 App，通过虚假收益诱骗用户充值；
- 冒充官方客服的贷款 App，通过隐性收费和高利率实施掠夺性借贷；
- 利用社交功能实施"杀猪盘"诈骗，通过情感操纵诱导用户转账。

因此，Fraud Detection 需要结合：

- Application Behavior；
- Application Profile；
- Network Intelligence；
- Threat Intelligence；
- User Feedback；
- AI Risk Analysis；

对应用进行综合风险判断。

Fraud Detection 不负责程序分析本身，而是消费 Analysis Engine Layer 输出的程序事实和行为证据，结合 Security Knowledge Platform 提供的涉诈知识、威胁情报和关联图谱，完成涉诈风险识别与评估。

---

# 2. Design Objectives

Fraud Detection 的设计目标包括：

- **识别涉诈应用**：对已知涉诈模式的应用进行精准识别，覆盖钓鱼、虚假投资、虚假贷款、杀猪盘等主要诈骗类型；
- **发现黑灰产应用特征**：提炼涉诈应用的共性特征，建立涉诈应用特征库，支撑新型涉诈应用的快速发现；
- **分析诈骗业务模式**：深入理解涉诈应用的业务流程和运营模式，识别其诈骗手法、引流方式和获利途径；
- **建立应用风险画像**：构建涉诈应用的多维度风险画像，包括开发者画像、网络基础设施画像、业务模式画像；
- **支撑风险处置**：提供可解释的检测结果和完整的证据链，支撑人工审核、应用下架、风险预警等处置动作；
- **支持未知诈骗模式发现**：利用 AI 能力和图关联分析，发现尚未归类的潜在涉诈应用，实现未知诈骗模式的早期预警。

---

# 3. Position in Architecture

Fraud Detection 属于 Detection Service Layer。

整体关系：

```
              Detection Service Layer


                       │


              Fraud Detection


                       ▲


                       │


        Standardized Analysis Result
        + Threat Intelligence
        + User Feedback


                       ▲


                       │


             Analysis Engine Layer
             + Security Knowledge Platform
```

Fraud Detection 与同层其他检测服务的关系：

| Module | Focus | Difference |
|--------|-------|------------|
| Malware Detection | 恶意代码和恶意行为 | 关注代码层面的恶意性 |
| Privacy Detection | 数据处理合规性 | 关注隐私数据处理合规 |
| Advertisement Detection | 广告行为规范性 | 关注广告行为合规性 |
| **Fraud Detection** | **涉诈风险识别** | **关注业务逻辑和交互的欺诈性** |

Fraud Detection 可与其他检测服务协同工作。例如：

- Malware Detection 发现的远控行为可作为 Fraud Detection 的关联线索；
- Privacy Detection 发现的数据过度采集可辅助判断涉诈意图；
- Advertisement Detection 发现的恶意推广可关联诈骗引流行为。

---

# 4. Detection Scope

Fraud Detection 主要覆盖以下涉诈类型。

---

## 4.1 Phishing Apps（钓鱼应用）

检测仿冒合法服务窃取用户凭证的应用。

包括：

- 仿冒银行/支付应用的虚假登录界面；
- 仿冒电商平台窃取账户密码；
- 仿冒社交媒体窃取用户凭证；
- 仿冒政府/公共服务应用；
- 利用 OAuth/SSO 流程劫持的钓鱼应用；
- 通过 Deep Link 拦截合法应用的认证流程。

特征：

- UI 与合法应用高度相似；
- 域名/包名与合法应用相近；
- 后端指向攻击者控制的服务器；
- 窃取的凭证被转发至 C2 服务器。

---

## 4.2 Fake Investment/Financial Apps（虚假投资理财应用）

检测以虚假投资理财为名实施诈骗的应用。

包括：

- 虚假股票/基金/期货交易平台；
- 虚假数字货币/虚拟资产交易平台；
- 虚假理财产品推介应用；
- 操盘后台可操控收益的"杀猪盘"投资 App；
- 虚假 P2P 理财平台；
- 伪装成正规金融机构的应用。

特征：

- 展示虚假行情和收益数据；
- 后台可操控用户账户余额和交易结果；
- 初期允许小额提现建立信任，大额投入后无法提现；
- 通过微信群/QQ群/直播间引流；
- 常与"杀猪盘"模式结合。

---

## 4.3 Fake Loan Apps（虚假贷款应用）

检测以虚假贷款为名实施诈骗或掠夺性借贷的应用。

包括：

- 虚假贷款平台，要求先缴纳"保证金"或"手续费"；
- 高利贷/套路贷应用，隐性收费、砍头息；
- 超额索权贷款应用，强制获取通讯录等敏感信息用于催收；
- 以贷款为名窃取个人身份信息的应用；
- 虚假信用评估/征信修复应用。

特征：

- 宣称"无抵押、秒放款、低利率"；
- 实际年化利率远超法定上限；
- 隐性收费项目（服务费、管理费、保险费等）；
- 强制获取通讯录、短信等用于暴力催收；
- 合同条款存在重大不利约定。

---

## 4.4 Romance/Social Engineering Scam Apps（杀猪盘/社交工程诈骗应用）

检测利用社交功能和情感操纵实施诈骗的应用。

包括：

- "杀猪盘"交友诈骗应用；
- 虚假婚恋/交友平台；
- 利用聊天功能实施投资诈骗；
- 虚假身份社交应用（伪造高净值人设）；
- 诱导赌博的社交应用。

特征：

- 应用内置聊天/社交功能；
- 配合"话术"进行情感操纵；
- 从情感交流逐步诱导至投资/赌博/转账；
- 诈骗周期长，涉及"养猪"和"杀猪"两个阶段；
- 常跨应用协同（社交 App 引流 + 投资 App 实施诈骗）。

---

## 4.5 Gambling Apps（赌博应用）

检测提供网络赌博服务的应用。

包括：

- 在线赌场/博彩应用；
- 网络赌博平台（百家乐、老虎机、体育博彩等）；
- 变相赌博应用（以游戏、竞猜为名的赌博）；
- 代理/引流赌博的应用；
- 虚假赌博平台（后台操控结果的诈骗赌博）。

特征：

- 提供下注/充值/提现的赌博流程；
- 利用代理模式发展下线；
- 后台可操控赌局结果；
- 常与洗钱活动关联；
- 通过社交群组引流。

---

## 4.6 Pyramid Scheme Apps（传销应用）

检测以传销模式运营的应用。

包括：

- 多级分销模式应用；
- 以"拉人头"为主要获利方式的应用；
- 虚假"创业""兼职"平台；
- 变相传销（微商、社交电商名义）；
- 虚假返利/分红平台。

特征：

- 多层级推荐返佣机制；
- 收益主要来自发展下线而非产品销售；
- 要求缴纳"入门费"或"加盟费"；
- 宣扬"躺赚""被动收入"等话术；
- 具有明显的层级结构。

---

## 4.7 Fake Customer Service Scam Apps（虚假客服诈骗应用）

检测冒充客服实施诈骗的应用。

包括：

- 冒充银行客服的诈骗应用；
- 冒充电商平台客服的退款诈骗；
- 冒充运营商/快递公司客服；
- 虚假售后/维修服务应用；
- 冒充公检法的诈骗应用。

特征：

- 仿冒正规机构客服界面；
- 诱导用户"验证身份"或"安全转账"；
- 制造紧急情境（账户异常、包裹问题等）；
- 引导用户开启屏幕共享或远程控制；
- 要求向"安全账户"转账。

---

## 4.8 Tech Support Scam Apps（技术支持诈骗应用）

检测以技术支持为名实施诈骗的应用。

包括：

- 虚假系统优化/清理应用；
- 虚假安全/杀毒应用（恐吓软件）；
- 虚假技术支持远程协助应用；
- 伪造系统错误提示诱导付费修复；
- 虚假驱动更新/软件更新应用。

特征：

- 虚假扫描结果，夸大系统问题；
- 恐吓用户存在"严重风险"；
- 诱导付费购买"高级版"或"专业服务"；
- 实际不具备所宣称的技术功能；
- 通过弹窗/通知持续骚扰。

---

## 4.9 Smishing/SMS Fraud Apps（短信钓鱼/短信诈骗应用）

检测利用短信功能实施钓鱼和诈骗的应用。

包括：

- 发送钓鱼短信的应用；
- 群发诈骗短信的应用；
- 伪造短信发送者标识；
- 诱导用户点击恶意链接；
- 验证码拦截/转发应用；
- 利用短信通道实施钓鱼攻击。

特征：

- 批量发送短信功能；
- 短信内容包含钓鱼链接；
- 伪造银行/运营商/官方短信；
- 后台配置短信模板和目标号码；
- 与 C2 服务器通信获取指令。

---

# 5. Detection Capability

Fraud Detection 主要基于以下能力完成判断。

---

## 5.1 App Profile Risk Analysis（应用画像风险分析）

分析应用的基本信息，识别涉诈风险信号。

包括：

- **开发者画像**：开发者历史记录、关联应用、注册信息真实性；
- **证书分析**：签名证书关联分析、证书异常（自签名、过期、多人共享）；
- **元数据分析**：应用名称/描述/关键词中的涉诈暗示、与知名应用的相似度；
- **版本信息**：频繁更换包名/开发者、版本异常；
- **上架信息**：上架时间、分类、年龄分级异常。

用于识别：

> 应用的"身份"是否存在涉诈嫌疑。

---

## 5.2 Network Intelligence Analysis（网络情报分析）

分析应用的网络通信行为和后端基础设施。

包括：

- **域名信誉**：域名注册时间、Whois 信息、历史解析记录、域名相似度；
- **C2 检测**：识别命令与控制服务器通信模式；
- **IP 信誉**：服务器 IP 地址的信誉评分、地理位置、ASN 信息；
- **SSL 证书分析**：自签名证书、证书与域名不匹配；
- **网络流量模式**：异常通信模式、数据外传行为、加密流量特征；
- **基础设施关联**：与已知涉诈应用共享服务器/CDN/DNS。

用于识别：

> 应用的网络后端是否具有涉诈基础设施特征。

---

## 5.3 UI/Content Semantic Analysis（UI/内容语义分析）

分析应用的界面和内容，识别仿冒和虚假宣传。

包括：

- **仿冒品牌检测**：UI 元素与知名应用的相似度比对、Logo/图标/配色仿冒检测；
- **文本语义分析**：应用描述、界面文本、推送内容中的涉诈关键词和话术识别；
- **虚假宣传识别**：夸大收益承诺、虚假权威背书、紧急行动诱导等；
- **OCR 内容提取**：从应用截图和运行时界面提取文字内容进行涉诈分析；
- **视觉相似度计算**：与已知涉诈应用的 UI 模板相似度比对。

用于识别：

> 应用的呈现内容是否具有涉诈特征。

---

## 5.4 Behavioral Pattern Analysis（行为模式分析）

分析应用的运行时行为，识别涉诈操作模式。

包括：

- **数据窃取行为**：过度采集联系人、短信、通话记录等敏感信息；
- **短信操作**：发送/拦截/读取短信、发送 premium SMS；
- **屏幕操作**：屏幕录制、屏幕共享、远程控制功能；
- **金融操作**：异常支付流程、虚假交易逻辑、后台操控交易结果；
- **通信行为**：与 C2 服务器的指令通信、数据上报；
- **隐藏行为**：隐藏图标、后台静默运行、伪装系统应用。

用于识别：

> 应用的实际行为是否具有涉诈操作模式。

---

## 5.5 Graph-based Association Analysis（图关联分析）

基于知识图谱进行多维度关联分析，发现涉诈网络。

包括：

- **关联应用分析**：相同开发者、相同证书、相同服务器关联的应用群组；
- **基础设施共享**：共享域名、IP、CDN、支付通道的涉诈应用集群；
- **代码复用关联**：共享代码模板/框架的涉诈应用家族；
- **运营者关联**：通过开发者信息、注册邮箱、联系方式关联的涉诈运营者；
- **资金链路关联**：共享支付账户、提现渠道的涉诈应用网络；
- **引流链路分析**：从引流应用到核心涉诈应用的完整诈骗链路。

用于识别：

> 涉诈应用之间的关联关系和背后的黑灰产网络。

---

## 5.6 User Feedback Analysis（用户反馈分析）

分析用户投诉和反馈信息，识别涉诈线索。

包括：

- **投诉内容分析**：用户举报文本中的涉诈关键词提取和分类；
- **投诉模式识别**：相似投诉的聚集模式分析；
- **损失信息提取**：从投诉中提取金额损失、诈骗手法等信息；
- **投诉关联分析**：跨应用投诉关联，识别同一黑灰产团伙运营的多个应用；
- **舆情监控**：社交媒体和应用商店评论中的涉诈相关讨论。

用于识别：

> 用户反馈是否指向涉诈行为。

---

## 5.7 AI Risk Scoring（AI 风险评分）

利用 AI 模型对应用进行综合风险评分。

包括：

- **多特征融合评分**：综合应用画像、网络情报、行为模式、关联分析等多维度特征；
- **风险等级划分**：将风险评分映射为高/中/低风险等级；
- **置信度评估**：对每个风险判断给出置信度，辅助人工决策；
- **可解释性输出**：生成风险判断的依据说明和证据链；
- **动态评分更新**：随新情报和行为数据更新风险评分。

用于提供：

> 应用的综合涉诈风险评估结果。

---

# 6. Detection Workflow

典型流程：

```
Application


    │


    ▼


Analysis Engine Result + Threat Intelligence + User Feedback


    │


    ▼


Fraud Detection


    │


    ├── App Profile Risk Analysis
    │       ├── Developer Profile
    │       ├── Certificate Analysis
    │       └── Metadata Analysis
    │
    ├── Network Intelligence Analysis
    │       ├── Domain Reputation
    │       ├── C2 Detection
    │       └── Infrastructure Correlation
    │
    ├── UI/Content Semantic Analysis
    │       ├── Brand Impersonation Detection
    │       ├── Text Semantic Analysis
    │       └── OCR Content Extraction
    │
    ├── Behavioral Pattern Analysis
    │       ├── Data Exfiltration Detection
    │       ├── Premium SMS Detection
    │       └── Financial Operation Analysis
    │
    ├── Graph-based Association Analysis
    │       ├── Related App Discovery
    │       ├── Shared Infrastructure Analysis
    │       └── Campaign Linkage
    │
    ├── User Feedback Analysis
    │       ├── Complaint Pattern Recognition
    │       └── Loss Information Extraction
    │
    └── AI Risk Scoring
            ├── Multi-feature Fusion
            ├── Risk Level Assignment
            └── Explanation Generation


    │


    ▼


Fraud Detection Result
```

详细步骤：

1. **数据接入**：接收 Analysis Engine 提供的静态/动态分析结果、Security Knowledge Platform 提供的威胁情报和涉诈知识、用户反馈数据；
2. **应用画像分析**：评估应用基本信息中的涉诈风险信号；
3. **网络情报分析**：分析应用后端基础设施的涉诈特征；
4. **内容语义分析**：检测 UI 仿冒和内容中的涉诈话术；
5. **行为模式分析**：识别运行时行为中的涉诈操作模式；
6. **关联分析**：通过知识图谱发现涉诈应用网络和黑灰产团伙；
7. **用户反馈分析**：整合用户投诉和举报信息；
8. **AI 综合评分**：融合所有分析结果，生成综合风险评分和证据链；
9. **结果输出**：输出涉诈检测结果，包含风险类型、风险等级、证据和处置建议。

---

# 7. Key Technologies

| Technology | Application Scenario | Description |
|-----------|---------------------|-------------|
| Risk Scoring Model | AI 风险评分 | 基于多维度特征的集成学习模型，对应用进行综合涉诈风险评分，支持 XGBoost/LightGBM 等算法 |
| NLP - Text Classification | 文本语义分析 | 对应用描述、界面文本、推送内容进行涉诈分类，识别虚假宣传和诈骗话术 |
| NLP - NER（Named Entity Recognition） | 实体提取 | 从文本中提取涉诈关键实体：金融术语、品牌名称、联系方式、诈骗话术模板 |
| OCR | 界面内容提取 | 从应用截图和运行时界面提取文字内容，用于仿冒检测和虚假宣传识别 |
| Knowledge Graph | 关联分析 | 构建涉诈应用、开发者、基础设施、运营者的关联图谱，支撑图查询和关联推理 |
| Link Analysis | 链路分析 | 分析涉诈应用间的共享基础设施、代码复用、资金链路和引流关系 |
| Graph Neural Networks（GNN） | 图关联推理 | 利用图神经网络在知识图谱上进行节点分类和链路预测，发现潜在涉诈应用 |
| BERT/Transformer | 深度语义理解 | 对应用描述、用户投诉、界面文本进行深度语义编码，捕捉涉诈语义特征 |
| Risk Profile | 风险画像 | 构建应用的多维度风险画像，包括开发者画像、网络画像、行为画像、关联画像 |

---

# 8. Technical Metrics

| Metric | Target | Description |
|--------|--------|-------------|
| Fraud App Identification Rate | ≥ 85% | 对各类涉诈应用的综合识别率，覆盖已知涉诈模式和变体 |
| False Positive Rate | ≤ 1% | 正常应用被误判为涉诈应用的比例，保障应用开发者合法权益 |
| Phishing Detection Rate | ≥ 90% | 对钓鱼类应用的专项检测率，钓鱼是最常见且危害最直接的诈骗类型 |
| Fake Investment/Loan Detection Rate | ≥ 85% | 对虚假投资理财和虚假贷款类应用的检测率 |
| New Fraud Pattern Discovery Rate | ≥ 60% | 对尚未归类的未知涉诈模式的发现率，衡量早期预警能力 |
| Detection Latency | ≤ 30s | 单应用涉诈检测的端到端延迟（不含动态分析等待时间） |
| Throughput | ≥ 1000 apps/hour | 系统每小时可处理的涉诈检测应用数量 |
| Risk Scoring Accuracy | ≥ 90% | AI 风险评分与人工审核结果的一致率 |
| Association Discovery Recall | ≥ 70% | 图关联分析对涉诈应用集群的召回率 |

---

# 9. Input and Output

## Input

来自 Analysis Engine：

- Static Analysis Result；
- Dynamic Analysis Result；
- Permission Information；
- API Usage；
- Network Behavior；
- UI Element Information。

来自 Security Knowledge Platform：

- Fraud Knowledge（涉诈知识库）；
- Threat Intelligence（威胁情报）；
- Fraud App Signatures（涉诈应用特征库）；
- Brand Knowledge（品牌知识，用于仿冒检测）；
- Association Graph（关联图谱）。

来自 External Sources：

- User Complaints（用户投诉/举报）；
- App Store Reviews（应用商店评论）；
- Social Media Signals（社交媒体信号）；
- Regulatory Bulletins（监管通报）。

---

## Output

输出：

```
Fraud Detection Result


├── Fraud Type（涉诈类型）
│   ├── Phishing
│   ├── Fake Investment
│   ├── Fake Loan
│   ├── Romance Scam
│   ├── Gambling
│   ├── Pyramid Scheme
│   ├── Fake Customer Service
│   ├── Tech Support Scam
│   └── Smishing/SMS Fraud
│
├── Risk Level（风险等级）
│   ├── High
│   ├── Medium
│   └── Low
│
├── Confidence（置信度）
│
├── Evidence（证据链）
│   ├── App Profile Evidence
│   ├── Network Intelligence Evidence
│   ├── UI/Content Evidence
│   ├── Behavioral Evidence
│   ├── Association Evidence
│   └── User Feedback Evidence
│
├── Association Information（关联信息）
│   ├── Related Apps
│   ├── Shared Infrastructure
│   └── Suspected Operator
│
├── Risk Explanation（风险说明）
│
└── Recommendation（处置建议）
    ├── Reject（拒绝上架）
    ├── Remove（下架处理）
    ├── Warn（风险预警）
    └── Review（人工审核）
```

---

# 10. Relationship with Other Modules

---

## 10.1 Relationship with Analysis Engine Layer

Analysis Engine 提供：

```
Application Behavior Evidence
```

例如：

```
Network Communication to Suspicious Domain

SMS Send/Read Operations

Screen Recording Capability

Data Collection Behavior
```

Fraud Detection 判断：

```
行为是否指向涉诈活动
```

---

## 10.2 Relationship with Security Knowledge Platform

Security Knowledge Platform 提供：

- 涉诈应用特征库；
- 品牌仿冒知识；
- 威胁情报；
- 关联图谱；
- 诈骗模式知识。

Fraud Detection 基于这些知识完成判断，同时将新发现的涉诈特征和关联关系反馈至 Security Knowledge Platform。

---

## 10.3 Relationship with Other Detection Services

Fraud Detection 与同层其他检测服务协同：

- **Malware Detection**：Malware Detection 发现的远控、数据窃取等恶意行为可作为 Fraud Detection 的涉诈行为线索；
- **Privacy Detection**：Privacy Detection 发现的过度数据采集行为可辅助判断涉诈意图（如贷款 App 强制获取通讯录）；
- **Advertisement Detection**：Advertisement Detection 发现的恶意推广可关联诈骗引流行为。

---

## 10.4 Relationship with Security Operation Platform

Fraud Detection 输出：

- 涉诈类型和风险等级；
- 证据链和关联信息；
- 风险说明和处置建议。

Security Operation Platform 根据结果：

- 人工复核审核；
- 应用下架处置；
- 风险预警发布；
- 执法协作联动。

---

# 11. Design Principles

---

## Multi-dimensional Evidence Fusion

涉诈风险判断必须融合多维度证据：

- 应用画像证据；
- 网络情报证据；
- 内容语义证据；
- 行为模式证据；
- 关联分析证据；
- 用户反馈证据。

单一维度证据不足以判定涉诈风险。

---

## Behavior and Intent Combination

涉诈检测需要同时关注：

- 应用实际做了什么（行为）；
- 应用意图做什么（意图）。

涉诈应用的行为可能在技术层面合法，但其意图是实施欺诈。

---

## Evidence Driven

每一个涉诈风险判定必须能够追溯到：

- 具体的行为证据；
- 可验证的情报来源；
- 可复现的分析过程。

---

## Explainable Detection

输出：

- 判定为涉诈的依据是什么；
- 哪些证据支撑了该判断；
- 风险等级如何得出。

可解释性对支撑人工审核和执法协作至关重要。

---

## Dynamic and Evolving

涉诈手法持续演进，检测能力需要：

- 支持新型诈骗模式的快速适配；
- 持续更新涉诈特征库和检测规则；
- 利用 AI 发现未知涉诈模式；
- 从用户反馈中学习新的诈骗手法。

---

## Precision and Recall Balance

在涉诈检测中需要平衡：

- 误报对正常应用开发者的影响；
- 漏报对用户财产安全的威胁。

通过多维度证据交叉验证和 AI 辅助评分，在保障低误报率的同时提升检测覆盖面。

---

# 12. Summary

Fraud Detection 是移动应用安全检测平台的核心业务检测能力之一。

它基于 Analysis Engine Layer 提供的程序分析结果和行为证据，结合 Security Knowledge Platform 提供的涉诈知识、威胁情报和关联图谱，以及用户反馈数据，对应用的涉诈风险进行综合判断。

Fraud Detection 关注：

> 应用是否被用于实施诈骗或欺诈活动。

而不是：

> 应用是否包含恶意代码。

通过应用画像分析、网络情报分析、UI/内容语义分析、行为模式分析、图关联分析、用户反馈分析和 AI 风险评分的多维度融合，Fraud Detection 能够有效识别钓鱼应用、虚假投资理财应用、虚假贷款应用、杀猪盘应用、赌博应用、传销应用、虚假客服诈骗应用、技术支持诈骗应用和短信钓鱼应用等各类型涉诈应用，支撑应用审核、风险预警和生态治理。

Fraud Detection 的核心价值在于：

- 覆盖 Malware Detection 无法识别的"技术合法但业务涉诈"的应用；
- 通过图关联分析揭示涉诈应用背后的黑灰产网络；
- 通过 AI 能力实现未知诈骗模式的早期发现；
- 提供可解释的检测结果，支撑人工审核和执法协作。
