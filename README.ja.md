<div align="center">
<img src="https://download.alianblank.com/gameframex/gameframex_logo_320.png" alt="Game Frame X Logo" width="160" />

# {'en': 'GameFrameX OperationKeyboard (WeChat Mini Game)', 'zh-CN': 'GameFrameX OperationKeyboard 键盘组件（微信小游戏）', 'zh-TW': 'GameFrameX OperationKeyboard 鍵盤組件（微信小遊戲）', 'ja': 'GameFrameX OperationKeyboard（微信ミニゲーム）', 'ko': 'GameFrameX OperationKeyboard(위챗 미니게임)'}

[![License](https://img.shields.io/github/license/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame)](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/blob/main/LICENSE.md)
[![Version](https://img.shields.io/github/v/release/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame)](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/releases)
[![Unity Version](https://img.shields.io/badge/Unity-2019.4-black?logo=unity)](https://unity.com/)
[![Documentation](https://img.shields.io/badge/Documentation-docs-blue)](https://gameframex.doc.alianblank.com)

インディーゲーム開発のオールインワンソリューション · インディーデベロッパーの夢を応援

<br />

[ドキュメント](https://gameframex.doc.alianblank.com) · [クイックスタート](#クイックスタート) · QQグループ: 467608841 / 233840761

<br />

[English](README.md) | [简体中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | **日本語** | [한국어](README.ko.md)

</div>

## 目次

- [プロジェクト概要](#プロジェクト概要)
- [クイックスタート](#クイックスタート)
- [使用例](#使用例)
- [ドキュメントとリソース](#ドキュメントとリソース)
- [コミュニティとサポート](#コミュニティとサポート)
- [ライセンス](#ライセンス)

---

## プロジェクト概要

GameFrameX OperationKeyboard（微信ミニゲーム） は 微信ミニゲーム 平台向けキーボード適応チャネルパッケージです。`IOperationKeyboardManager` を実装し（`WechatMiniGameOperationKeyboardManager`、`WX.ShowKeyboard` / `WX.HideKeyboard` / `WX.UpdateKeyboard` plus `On/OffKeyboardInput/Confirm/Complete/HeightChange` を直通）、`WechatMiniGameOperationKeyboardBootstrap` が `BeforeSceneLoad` 段階でメインパッケージの `OperationKeyboardRegistry` に自己登録します（マクロ `UNITY_WEBGL && ENABLE_WECHAT_MINI_GAME && ENABLE_WECHAT_MINI_GAME_OPERATIONKEYBOARD` が有効でない場合コードは除去され、登録も発生しません。参照即登録）。設定面はありません。キーボードオプションはコアファサードがフィールド契約から導出します。

### 主な機能

- **微信ミニゲーム キーボード** - ネイティブ SDK の showKeyboard 系と単一スロットコールバックイベントを統合
- **自己登録** - `BeforeSceneLoad` 段階で `OperationKeyboardRegistry` に登録（参照即登録）
- **ゼロ結合** - コアパッケージはチャネル SDK を参照しません。SDK は対象プロジェクトが用意します

### システム要件

- Unity 2019.4+

---

## クイックスタート

### インストール

以下のいずれかの方法を選択してください：

1. Unity プロジェクトの `Packages/manifest.json` を編集し、`scopedRegistries` セクションを追加します：
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

   `scopes` はこのレジストリで解決されるパッケージを制御します。`com.gameframex` で始まるパッケージのみこのレジストリから取得されます。

2. `manifest.json` の `dependencies` に以下を追加します：
   ```json
   {
      "com.gameframex.unity.operationkeyboard.wechatminigame": "https://github.com/gameframex/com.gameframex.unity.operationkeyboard.wechatminigame.git"
   }
   ```
3. Unity の `Package Manager` で `Git URL` を使って追加します： `https://github.com/gameframex/com.gameframex.unity.operationkeyboard.wechatminigame.git`
4. リポジトリをクローンして Unity プロジェクトの `Packages` ディレクトリに配置すると自動的に読み込まれます。

## 使用例

### チャネル登録（Bootstrap 自己登録）

起動時の設定は不要です。本パッケージを 微信ミニゲーム SDK（`com.qq.weixin.minigame`）と一緒にインストールするとチャネルは自動登録されます。UI 入力フィールドはコアファサード経由で駆動されます（`com.gameframex.unity.operationkeyboard.ugui` をインストールすると `InputField` / `TMP_InputField` を自動バインド）。

```csharp
using GameFrameX.OperationKeyboard.Runtime;

// UI バインディングパッケージがフォーカス / ブラー時に自動呼び出し。手動利用（カスタム IOperationKeyboardField）：
// OperationKeyboard.Attach(field);  /  OperationKeyboard.Detach(field);
OperationKeyboard.UpdateValue("プログラム値");
OperationKeyboard.OnHeightChanged(height => { /* 回避レイアウト */ });
```

チャネルの `ChannelId` 定数は `"wechatminigame"` です。`OperationKeyboardRegistry.ResolveManager()` は登録順に解決し、`GetManager("wechatminigame")` で明示的に取得することもできます。

### プロジェクト構成

```
Runtime/
├── WechatMiniGame/
│   ├── WechatMiniGameOperationKeyboardManager.cs  # 微信ミニゲーム キーボードマネージャー
│   └── WechatMiniGameOperationKeyboardBootstrap.cs  # チャネル起動登録（自己登録）
└── GameFrameXOperationKeyboardWechatMiniGameCroppingHelper.cs  # コードクリッピングヘルパー
```

### 主要なクラス

- **`WechatMiniGameOperationKeyboardManager`**：微信ミニゲーム キーボード SDK 向けの `IOperationKeyboardManager` 実装（`public const string ChannelId`）。`Hide` 时に全ての平台イベントを解除。
- **`WechatMiniGameOperationKeyboardBootstrap`**：チャネル起動登録。`BeforeSceneLoad` 段階でメインパッケージの `OperationKeyboardRegistry` に自己登録。
- **`GameFrameXOperationKeyboardWechatMiniGameCroppingHelper`**：ビルド最適化用のコードクリッピングヘルパー。

### 注意事項

- `Hide()` は全てのキーボードイベントを解除し（`OffKeyboardInput/Confirm/Complete/HeightChange`）、セッション間の漏れを防ぎます。
- `maxLength` は -1 をそのまま渡します（微信の無制限 semantics）。`characterLimit` のようなハード上限はピンイン組成を切断し、中国語入力ができなくなります。

---

## ドキュメントとリソース

- **ドキュメント**: [https://gameframex.doc.alianblank.com](https://gameframex.doc.alianblank.com)
- **課題報告**: [GitHub Issues](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/issues)

---

## コミュニティとサポート

- **QQ グループ**: [467608841](https://qm.qq.com/cgi-bin/qm/qr?k=sYFd1nv6m2KZIWFLorZ5pBR0AE5ZhbuL&jump_from=webapi&authKey=oCu+uoL3n35fT5SEt7iLgGtROPxh31n/rHUxRlp0w1f+j38W4tKBuWyRH3KEdwHN)
- **機能リクエスト**: [GitHub Discussions](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/discussions)

---

## 依存関係
| パッケージ | 説明 |
|----|------|
| com.gameframex.unity.operationkeyboard | GameFrameX OperationKeyboard コア |
| com.gameframex.unity | GameFrameX コア |
| com.qq.weixin.minigame | 対象プロジェクトが用意 |

## 変更履歴

更新履歴は [Releases](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/releases) を参照してください。

## ライセンス

ライセンスの詳細は [LICENSE.md](LICENSE.md) を参照してください。
