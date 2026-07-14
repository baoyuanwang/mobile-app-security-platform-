# 4.1 Package Parser（应用包解析器）

> Chapter 4.1
>
> Package Parser

---

# 1. 模块定位

Package Parser 是 Analysis Engine Layer 的入口组件。

它负责解析开发者提交的应用安装包（APK/HAP），提取应用基础元数据、程序结构和资源信息，并生成统一的数据模型，为后续静态分析和动态分析提供输入。

Package Parser **不负责安全分析，也不负责风险判断**，其职责是将不同平台、不同格式的应用安装包转换为统一的应用描述对象（Application Model）。

一句话概括：

> **Package Parser 负责回答："这个安装包里面有什么？"**

---

# 2. 架构位置

```text
                Application Package

                        │
                        ▼

                Package Parser
                        │
        ┌───────────────┴───────────────┐
        ▼                               ▼

Static Analysis Engine        Dynamic Analysis Engine
```

Package Parser 是 Static Analysis Engine 和 Dynamic Analysis Engine 的共同入口。

所有分析均基于 Package Parser 输出的数据模型开展。

---

# 3. 功能职责

Package Parser 主要承担以下职责：

| 功能 | 描述 |
|------|------|
| 安装包识别 | 识别 APK、AAB、HAP 等格式 |
| 文件解析 | 解压安装包，构建文件树 |
| Manifest 解析 | 提取应用配置及组件信息 |
| DEX / ELF 解析 | 提取可执行代码 |
| 签名解析 | 提取开发者签名信息 |
| Resource 解析 | 提取资源文件 |
| SDK 初步识别 | 提取第三方 SDK 信息 |
| Metadata 构建 | 输出统一应用元数据 |

---

# 4. 支持的应用格式

目前支持：

## Android

- APK
- AAB
- DEX
- ODEX
- VDEX
- ELF
- SO Library

## HarmonyOS

- HAP
- APP Pack
- Native Library

后续可扩展：

- iOS IPA（预留）
- 小程序包（预留）

---

# 5. Package Parser 总体架构

```text
                Application Package

                        │

                        ▼

                 Package Loader

                        │

         ┌──────────────┼──────────────┐
         ▼              ▼              ▼

    Manifest      Code Parser     Resource Parser

         │              │              │

         ▼              ▼              ▼

 Signature Parser   SDK Scanner   Metadata Builder

                 └──────────────┬──────────────┘
                                ▼

                    Application Model
```

Package Parser 最终输出统一的 **Application Model**。

---

# 6. 核心技术

## 6.1 Package Loader

负责识别安装包格式。

主要工作：

- 文件合法性校验
- 完整性检查
- 解压安装包
- 文件树构建

输出：

```text
Application Directory
```

---

## 6.2 Manifest Parser

解析：

Android：

```
AndroidManifest.xml
```

Harmony：

```
module.json
app.json5
```

提取：

- Package Name
- Version
- Application ID
- Permissions
- Activity
- Service
- Receiver
- Provider
- Intent Filter
- Exported Component
- Min SDK
- Target SDK

---

## 6.3 Code Parser

负责解析程序代码。

Android：

- classes.dex
- classes2.dex
- ODEX

Harmony：

- ArkTS
- Native Library

输出：

```text
Code Object
```

供 Static Analysis Engine 使用。

---

## 6.4 Resource Parser

解析：

```
res/

assets/

drawable/

layout/

xml/

string/

icon/

```

输出：

- 图片资源
- UI布局
- 文本资源
- 配置资源

这些资源将在：

- 内容检测
- 仿冒检测
- OCR检测

中使用。

---

## 6.5 Signature Parser

解析：

应用签名。

提取：

- Certificate
- SHA1
- SHA256
- Issuer
- Subject
- Valid Time

应用：

- 开发者身份识别
- 同源应用分析
- 签名异常检测

---

## 6.6 SDK Scanner

扫描：

第三方 SDK。

识别：

- 广告 SDK
- 推送 SDK
- 支付 SDK
- 地图 SDK
- 统计 SDK
- AI SDK
- 加固 SDK

输出：

```text
SDK Inventory
```

SDK 的安全分析将在 Static Analysis Engine 中进一步完成。

---

# 7. Application Model

Package Parser 输出统一应用对象。

示例：

```json
{
  "packageName": "...",
  "version": "...",
  "certificate": "...",
  "permissions": [],
  "activities": [],
  "services": [],
  "receivers": [],
  "providers": [],
  "sdkList": [],
  "dexFiles": [],
  "nativeLibraries": [],
  "resources": []
}
```

Application Model 是整个 Analysis Engine 的统一输入模型。

---

# 8. 输入与输出

输入：

```
APK

HAP

```

输出：

```
Application Model
```

供：

- Static Analysis Engine
- Dynamic Analysis Engine

共同使用。

---

# 9. 技术指标

| 指标 | 目标 |
|------|------:|
| APK/HAP 解析成功率 | ≥99.5% |
| Manifest 解析覆盖率 | 100% |
| DEX 提取成功率 | ≥99% |
| SO 文件识别率 | ≥99% |
| SDK 识别准确率 | ≥95% |
| 平均解析时间 | ≤5 秒（100MB 应用） |

---

# 10. 本章总结

Package Parser 是整个 Analysis Engine 的入口。

它负责将不同平台、不同格式的应用安装包解析为统一的 Application Model，为静态分析和动态分析提供一致的数据输入。

需要强调的是，Package Parser **不承担任何安全检测职责**，它的目标是建立标准化的应用描述模型，为后续分析提供可靠的数据基础。
