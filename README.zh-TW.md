<div align="center">
<img src="https://download.alianblank.com/gameframex/gameframex_logo_320.png" alt="Game Frame X Logo" width="160" />

# {'en': 'GameFrameX OperationKeyboard (WeChat Mini Game)', 'zh-CN': 'GameFrameX OperationKeyboard 键盘组件（微信小游戏）', 'zh-TW': 'GameFrameX OperationKeyboard 鍵盤組件（微信小遊戲）', 'ja': 'GameFrameX OperationKeyboard（微信ミニゲーム）', 'ko': 'GameFrameX OperationKeyboard(위챗 미니게임)'}

[![License](https://img.shields.io/github/license/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame)](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/blob/main/LICENSE.md)
[![Version](https://img.shields.io/github/v/release/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame)](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/releases)
[![Unity Version](https://img.shields.io/badge/Unity-2019.4-black?logo=unity)](https://unity.com/)
[![Documentation](https://img.shields.io/badge/Documentation-docs-blue)](https://gameframex.doc.alianblank.com)

獨立遊戲前後端一體化解決方案 · 獨立遊戲開發者的圓夢大使

<br />

[文檔](https://gameframex.doc.alianblank.com) · [快速開始](#快速開始) · QQ群: 467608841 / 233840761

<br />

[English](README.md) | [简体中文](README.zh-CN.md) | **繁體中文** | [日本語](README.ja.md) | [한국어](README.ko.md)

</div>

## 目錄導航

- [項目簡介](#項目簡介)
- [快速開始](#快速開始)
- [使用範例](#使用範例)
- [文檔與資源](#文檔與資源)
- [社區與支援](#社區與支援)
- [開源協議](#開源協議)

---

## 項目簡介

GameFrameX OperationKeyboard 鍵盤組件（微信小遊戲） 是 微信小遊戲 平台的鍵盤適配渠道包。實現 `IOperationKeyboardManager`（`WechatMiniGameOperationKeyboardManager`，直通 `WX.ShowKeyboard` / `WX.HideKeyboard` / `WX.UpdateKeyboard` plus `On/OffKeyboardInput/Confirm/Complete/HeightChange`），並通過 `WechatMiniGameOperationKeyboardBootstrap` 在 `BeforeSceneLoad` 階段自註冊進主包 `OperationKeyboardRegistry`（宏 `UNITY_WEBGL && ENABLE_WECHAT_MINI_GAME && ENABLE_WECHAT_MINI_GAME_OPERATIONKEYBOARD` 未啟用時代碼被裁剪，註冊不會發生；引用即註冊）。無配置面：鍵盤參數由核心門面從字段契約推導。

### 核心特性

- **微信小遊戲 鍵盤** - 接入原生 SDK 的 showKeyboard 家族與單槽回調事件
- **自註冊** - `BeforeSceneLoad` 階段註冊進 `OperationKeyboardRegistry`（引用即註冊）
- **零耦合** - 核心包不引用渠道 SDK；SDK 由目標工程自帶

### 系統需求

- Unity 2019.4+

---

## 快速開始

### 安裝

選擇以下任一方式：

1. 編輯 Unity 項目的 `Packages/manifest.json`，添加 `scopedRegistries` 部分：
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

   `scopes` 控制哪些包通過此註冊表解析。只有以 `com.gameframex` 開頭的包才會從這個註冊表獲取。

2. 直接在 `manifest.json` 的 `dependencies` 節點下添加以下內容：
   ```json
   {
      "com.gameframex.unity.operationkeyboard.wechatminigame": "https://github.com/gameframex/com.gameframex.unity.operationkeyboard.wechatminigame.git"
   }
   ```
3. 在 Unity 的 `Package Manager` 中使用 `Git URL` 的方式添加庫，地址為： `https://github.com/gameframex/com.gameframex.unity.operationkeyboard.wechatminigame.git`
4. 直接下載倉庫放置到 Unity 項目的 `Packages` 目錄下，會自動加載識別。

## 使用範例

### 渠道註冊（Bootstrap 自註冊）

無需任何啟動配置。本包與 微信小遊戲 SDK（`com.qq.weixin.minigame`）一起安裝後渠道自動註冊；UI 輸入框經核心門面驅動（安裝 `com.gameframex.unity.operationkeyboard.ugui` 可自動綁定 `InputField` / `TMP_InputField`）。

```csharp
using GameFrameX.OperationKeyboard.Runtime;

// UI 綁定包在聚焦 / 失焦時自動調用；手動使用（自定義 IOperationKeyboardField 實現）：
// OperationKeyboard.Attach(field);  /  OperationKeyboard.Detach(field);
OperationKeyboard.UpdateValue("程序賦值");
OperationKeyboard.OnHeightChanged(height => { /* 避讓佈局 */ });
```

渠道 `ChannelId` 常量為 `"wechatminigame"`；`OperationKeyboardRegistry.ResolveManager()` 按註冊序解析渠道，也可用 `GetManager("wechatminigame")` 顯式獲取本渠道。

### 項目結構

```
Runtime/
├── WechatMiniGame/
│   ├── WechatMiniGameOperationKeyboardManager.cs  # 微信小遊戲 鍵盤管理器
│   └── WechatMiniGameOperationKeyboardBootstrap.cs  # 渠道啟動註冊（自註冊）
└── GameFrameXOperationKeyboardWechatMiniGameCroppingHelper.cs  # 防裁剪助手
```

### 主要類

- **`WechatMiniGameOperationKeyboardManager`**：面向 微信小遊戲 鍵盤 SDK 實現 `IOperationKeyboardManager`（`public const string ChannelId`）；`Hide` 時註銷全部平台事件。
- **`WechatMiniGameOperationKeyboardBootstrap`**：渠道啟動註冊；`BeforeSceneLoad` 階段自註冊進主包 `OperationKeyboardRegistry`。
- **`GameFrameXOperationKeyboardWechatMiniGameCroppingHelper`**：構建裁剪優化用的防裁剪助手。

### 注意事項

- `Hide()` 註銷全部鍵盤事件（`OffKeyboardInput/Confirm/Complete/HeightChange`），防止跨會話串擾。
- `maxLength` 原樣傳 -1（微信不限長語義）；按 `characterLimit` 傳硬上限會掐斷拼音組合、無法輸入中文。

---

## 文檔與資源

- **完整文檔**: [https://gameframex.doc.alianblank.com](https://gameframex.doc.alianblank.com)
- **問題反饋**: [GitHub Issues](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/issues)

---

## 社區與支援

- **QQ 討論群**: [467608841](https://qm.qq.com/cgi-bin/qm/qr?k=sYFd1nv6m2KZIWFLorZ5pBR0AE5ZhbuL&jump_from=webapi&authKey=oCu+uoL3n35fT5SEt7iLgGtROPxh31n/rHUxRlp0w1f+j38W4tKBuWyRH3KEdwHN)
- **功能建議**: [GitHub Discussions](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/discussions)

---

## 依賴
| 包 | 說明 |
|----|------|
| com.gameframex.unity.operationkeyboard | GameFrameX OperationKeyboard 核心包 |
| com.gameframex.unity | GameFrameX 核心 |
| com.qq.weixin.minigame | 由目標工程自帶 |

## 更新日誌

查看 [Releases](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/releases) 了解更新日誌。

## 開源協議

詳見 [LICENSE.md](LICENSE.md) 文件。
