<div align="center">
<img src="https://download.alianblank.com/gameframex/gameframex_logo_320.png" alt="Game Frame X Logo" width="160" />

# {'en': 'GameFrameX OperationKeyboard (WeChat Mini Game)', 'zh-CN': 'GameFrameX OperationKeyboard 键盘组件（微信小游戏）', 'zh-TW': 'GameFrameX OperationKeyboard 鍵盤組件（微信小遊戲）', 'ja': 'GameFrameX OperationKeyboard（微信ミニゲーム）', 'ko': 'GameFrameX OperationKeyboard(위챗 미니게임)'}

[![License](https://img.shields.io/github/license/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame)](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/blob/main/LICENSE.md)
[![Version](https://img.shields.io/github/v/release/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame)](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/releases)
[![Unity Version](https://img.shields.io/badge/Unity-2019.4-black?logo=unity)](https://unity.com/)
[![Documentation](https://img.shields.io/badge/Documentation-docs-blue)](https://gameframex.doc.alianblank.com)

独立游戏前后端一体化解决方案 · 独立游戏开发者的圆梦大使

<br />

[文档](https://gameframex.doc.alianblank.com) · [快速开始](#快速开始) · QQ群: 467608841 / 233840761

<br />

[English](README.md) | **简体中文** | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [한국어](README.ko.md)

</div>

## 目录导航

- [项目简介](#项目简介)
- [快速开始](#快速开始)
- [使用示例](#使用示例)
- [文档与资源](#文档与资源)
- [社区与支持](#社区与支持)
- [开源协议](#开源协议)

---

## 项目简介

GameFrameX OperationKeyboard 键盘组件（微信小游戏） 是 微信小游戏 平台的键盘适配渠道包。实现 `IOperationKeyboardManager`（`WechatMiniGameOperationKeyboardManager`，直通 `WX.ShowKeyboard` / `WX.HideKeyboard` / `WX.UpdateKeyboard` plus `On/OffKeyboardInput/Confirm/Complete/HeightChange`），并通过 `WechatMiniGameOperationKeyboardBootstrap` 在 `BeforeSceneLoad` 阶段自注册进主包 `OperationKeyboardRegistry`（宏 `UNITY_WEBGL && ENABLE_WECHAT_MINI_GAME && ENABLE_WECHAT_MINI_GAME_OPERATIONKEYBOARD` 未启用时代码被裁剪，注册不会发生；引用即注册）。无配置面：键盘参数由核心门面从字段契约推导。

### 核心特性

- **微信小游戏 键盘** - 接入原生 SDK 的 showKeyboard 家族与单槽回调事件
- **自注册** - `BeforeSceneLoad` 阶段注册进 `OperationKeyboardRegistry`（引用即注册）
- **零耦合** - 核心包不引用渠道 SDK；SDK 由目标工程自带

### 系统要求

- Unity 2019.4+

---

## 快速开始

### 安装

选择以下任一方式：

1. 编辑 Unity 项目的 `Packages/manifest.json`，添加 `scopedRegistries` 部分：
   ```json
   {
     "scopedRegistries": [
       {
         "name": "GameFrameX",
         "url": "https://gameframex.upm.alianblank.uk",
         "scopes": [
           "com.gameframex"
         ]
       }
     ],
     "dependencies": {
       "com.gameframex.unity.operationkeyboard.wechatminigame": "1.0.0"
     }
   }
   ```

   `scopes` 控制哪些包通过此注册表解析。只有以 `com.gameframex` 开头的包才会从这个注册表获取。

2. 直接在 `manifest.json` 的 `dependencies` 节点下添加以下内容：
   ```json
   {
      "com.gameframex.unity.operationkeyboard.wechatminigame": "https://github.com/gameframex/com.gameframex.unity.operationkeyboard.wechatminigame.git"
   }
   ```
3. 在 Unity 的 `Package Manager` 中使用 `Git URL` 的方式添加库，地址为： `https://github.com/gameframex/com.gameframex.unity.operationkeyboard.wechatminigame.git`
4. 直接下载仓库放置到 Unity 项目的 `Packages` 目录下，会自动加载识别。

## 使用示例

### 渠道注册（Bootstrap 自注册）

无需任何启动配置。本包与 微信小游戏 SDK（`com.qq.weixin.minigame`）一起安装后渠道自动注册；UI 输入框经核心门面驱动（安装 `com.gameframex.unity.operationkeyboard.ugui` 可自动绑定 `InputField` / `TMP_InputField`）。

```csharp
using GameFrameX.OperationKeyboard.Runtime;

// UI 绑定包在聚焦 / 失焦时自动调用；手动使用（自定义 IOperationKeyboardField 实现）：
// OperationKeyboard.Attach(field);  /  OperationKeyboard.Detach(field);
OperationKeyboard.UpdateValue("程序赋值");
OperationKeyboard.OnHeightChanged(height => { /* 避让布局 */ });
```

渠道 `ChannelId` 常量为 `"wechatminigame"`；`OperationKeyboardRegistry.ResolveManager()` 按注册序解析渠道，也可用 `GetManager("wechatminigame")` 显式获取本渠道。

### 项目结构

```
Runtime/
├── WechatMiniGame/
│   ├── WechatMiniGameOperationKeyboardManager.cs  # 微信小游戏 键盘管理器
│   └── WechatMiniGameOperationKeyboardBootstrap.cs  # 渠道启动注册（自注册）
└── GameFrameXOperationKeyboardWechatMiniGameCroppingHelper.cs  # 防裁剪助手
```

### 主要类

- **`WechatMiniGameOperationKeyboardManager`**：面向 微信小游戏 键盘 SDK 实现 `IOperationKeyboardManager`（`public const string ChannelId`）；`Hide` 时注销全部平台事件。
- **`WechatMiniGameOperationKeyboardBootstrap`**：渠道启动注册；`BeforeSceneLoad` 阶段自注册进主包 `OperationKeyboardRegistry`。
- **`GameFrameXOperationKeyboardWechatMiniGameCroppingHelper`**：构建裁剪优化用的防裁剪助手。

### 注意事项

- `Hide()` 注销全部键盘事件（`OffKeyboardInput/Confirm/Complete/HeightChange`），防止跨会话串扰。
- `maxLength` 原样传 -1（微信不限长语义）；按 `characterLimit` 传硬上限会掐断拼音组合、无法输入中文。

---

## 文档与资源

- **完整文档**: [https://gameframex.doc.alianblank.com](https://gameframex.doc.alianblank.com)
- **问题反馈**: [GitHub Issues](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/issues)

---

## 社区与支持

- **QQ 讨论群**: [467608841](https://qm.qq.com/cgi-bin/qm/qr?k=sYFd1nv6m2KZIWFLorZ5pBR0AE5ZhbuL&jump_from=webapi&authKey=oCu+uoL3n35fT5SEt7iLgGtROPxh31n/rHUxRlp0w1f+j38W4tKBuWyRH3KEdwHN)
- **功能建议**: [GitHub Discussions](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/discussions)

---

## 依赖
| 包 | 说明 |
|----|------|
| com.gameframex.unity.operationkeyboard | GameFrameX OperationKeyboard 核心包 |
| com.gameframex.unity | GameFrameX 核心 |
| com.qq.weixin.minigame | 由目标工程自带 |

## 更新日志

查看 [Releases](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/releases) 了解更新日志。

## 开源协议

详见 [LICENSE.md](LICENSE.md) 文件。
