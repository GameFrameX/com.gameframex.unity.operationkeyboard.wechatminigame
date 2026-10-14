<div align="center">
<img src="https://download.alianblank.com/gameframex/gameframex_logo_320.png" alt="Game Frame X Logo" width="160" />

# {'en': 'GameFrameX OperationKeyboard (WeChat Mini Game)', 'zh-CN': 'GameFrameX OperationKeyboard 键盘组件（微信小游戏）', 'zh-TW': 'GameFrameX OperationKeyboard 鍵盤組件（微信小遊戲）', 'ja': 'GameFrameX OperationKeyboard（微信ミニゲーム）', 'ko': 'GameFrameX OperationKeyboard(위챗 미니게임)'}

[![License](https://img.shields.io/github/license/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame)](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/blob/main/LICENSE.md)
[![Version](https://img.shields.io/github/v/release/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame)](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/releases)
[![Unity Version](https://img.shields.io/badge/Unity-2019.4-black?logo=unity)](https://unity.com/)
[![Documentation](https://img.shields.io/badge/Documentation-docs-blue)](https://gameframex.doc.alianblank.com)

인디 게임 개발 올인원 솔루션 · 인디 개발자의 꿈을 응원

<br />

[문서](https://gameframex.doc.alianblank.com) · [빠른 시작](#빠른-시작) · QQ그룹: 467608841 / 233840761

<br />

[English](README.md) | [简体中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | **한국어**

</div>

## 목차

- [프로젝트 개요](#프로젝트-개요)
- [빠른 시작](#빠른-시작)
- [사용 예시](#사용-예시)
- [문서 및 자료](#문서-및-자료)
- [커뮤니티 및 지원](#커뮤니티-및-지원)
- [라이선스](#라이선스)

---

## 프로젝트 개요

GameFrameX OperationKeyboard(위챗 미니게임)은(는) 위챗 미니게임 플랫폼용 키보드 적응 채널 패키지입니다. `IOperationKeyboardManager`를 구현하고(`WechatMiniGameOperationKeyboardManager`, `WX.ShowKeyboard` / `WX.HideKeyboard` / `WX.UpdateKeyboard` plus `On/OffKeyboardInput/Confirm/Complete/HeightChange` 직통), `WechatMiniGameOperationKeyboardBootstrap`이 `BeforeSceneLoad` 단계에서 메인 패키지의 `OperationKeyboardRegistry`에 자가 등록합니다(매크로 `UNITY_WEBGL && ENABLE_WECHAT_MINI_GAME && ENABLE_WECHAT_MINI_GAME_OPERATIONKEYBOARD`가 활성화되지 않으면 코드가 제거되어 등록도 발생하지 않습니다. 참조 곧 등록). 설정 면은 없습니다. 키보드 옵션은 코어 파사드가 필드 계약에서 도출합니다.

### 주요 기능

- **위챗 미니게임 키보드** - 네이티브 SDK의 showKeyboard 계열과 단일 슬롯 콜백 이벤트 통합
- **자가 등록** - `BeforeSceneLoad` 단계에서 `OperationKeyboardRegistry`에 등록(참조 곧 등록)
- **제로 결합** - 코어 패키지는 채널 SDK를 참조하지 않습니다. SDK는 대상 프로젝트가 제공합니다

### 시스템 요구사항

- Unity 2019.4+

---

## 빠른 시작

### 설치

다음 방법 중 하나를 선택하세요:

1. Unity 프로젝트의 `Packages/manifest.json`을 편집하여 `scopedRegistries` 섹션을 추가합니다:
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

   `scopes`는 이 레지스트리를 통해 해석되는 패키지를 제어합니다. `com.gameframex`로 시작하는 패키지만 이 레지스트리에서 가져옵니다.

2. `manifest.json`의 `dependencies`에 다음을 추가합니다:
   ```json
   {
      "com.gameframex.unity.operationkeyboard.wechatminigame": "https://github.com/gameframex/com.gameframex.unity.operationkeyboard.wechatminigame.git"
   }
   ```
3. Unity의 `Package Manager`에서 `Git URL`로 추가합니다: `https://github.com/gameframex/com.gameframex.unity.operationkeyboard.wechatminigame.git`
4. 저장소를 클론하여 Unity 프로젝트의 `Packages` 디렉토리에 배치하면 자동으로 로드됩니다.

## 사용 예시

### 채널 등록 (Bootstrap 자가 등록)

시작 설정이 필요 없습니다. 이 패키지를 위챗 미니게임 SDK(`com.qq.weixin.minigame`)와 함께 설치하면 채널이 자동 등록됩니다. UI 입력 필드는 코어 파사드를 통해 구동됩니다(`com.gameframex.unity.operationkeyboard.ugui` 설치 시 `InputField` / `TMP_InputField` 자동 바인딩).

```csharp
using GameFrameX.OperationKeyboard.Runtime;

// UI 바인딩 패키지가 포커스 / 블러 시 자동 호출. 수동 사용(커스텀 IOperationKeyboardField):
// OperationKeyboard.Attach(field);  /  OperationKeyboard.Detach(field);
OperationKeyboard.UpdateValue("프로그램 값");
OperationKeyboard.OnHeightChanged(height => { /* 회피 레이아웃 */ });
```

채널 `ChannelId` 상수는 `"wechatminigame"`입니다. `OperationKeyboardRegistry.ResolveManager()`가 등록 순으로 해석하며, `GetManager("wechatminigame")`로 명시적으로 가져올 수도 있습니다.

### 프로젝트 구조

```
Runtime/
├── WechatMiniGame/
│   ├── WechatMiniGameOperationKeyboardManager.cs  # 위챗 미니게임 키보드 매니저
│   └── WechatMiniGameOperationKeyboardBootstrap.cs  # 채널 시작 등록(자가 등록)
└── GameFrameXOperationKeyboardWechatMiniGameCroppingHelper.cs  # 코드 크로핑 헬퍼
```

### 주요 클래스

- **`WechatMiniGameOperationKeyboardManager`**: 위챗 미니게임 키보드 SDK용 `IOperationKeyboardManager` 구현(`public const string ChannelId`). `Hide` 시 모든 플랫폼 이벤트 해제.
- **`WechatMiniGameOperationKeyboardBootstrap`**: 채널 시작 등록. `BeforeSceneLoad` 단계에서 메인 패키지의 `OperationKeyboardRegistry`에 자가 등록.
- **`GameFrameXOperationKeyboardWechatMiniGameCroppingHelper`**: 빌드 최적화용 코드 크로핑 헬퍼.

### 주의 사항

- `Hide()`는 모든 키보드 이벤트를 해제합니다(`OffKeyboardInput/Confirm/Complete/HeightChange`). 세션 간 누출을 방지합니다.
- `maxLength`는 -1을 그대로 전달합니다(위챗 무제한 의미론). `characterLimit` 같은 하드 상한은 병음 조합을 잘라 중국어 입력을 막습니다.

---

## 문서 및 자료

- **문서**: [https://gameframex.doc.alianblank.com](https://gameframex.doc.alianblank.com)
- **이슈 제보**: [GitHub Issues](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/issues)

---

## 커뮤니티 및 지원

- **QQ 그룹**: [467608841](https://qm.qq.com/cgi-bin/qm/qr?k=sYFd1nv6m2KZIWFLorZ5pBR0AE5ZhbuL&jump_from=webapi&authKey=oCu+uoL3n35fT5SEt7iLgGtROPxh31n/rHUxRlp0w1f+j38W4tKBuWyRH3KEdwHN)
- **기능 제안**: [GitHub Discussions](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/discussions)

---

## 의존성
| 패키지 | 설명 |
|----|------|
| com.gameframex.unity.operationkeyboard | GameFrameX OperationKeyboard 코어 |
| com.gameframex.unity | GameFrameX 코어 |
| com.qq.weixin.minigame | 대상 프로젝트 제공 |

## 변경 로그

변경 이력은 [Releases](https://github.com/GameFrameX/com.gameframex.unity.operationkeyboard.wechatminigame/releases)를 참조하세요.

## 라이선스

라이선스에 대한 자세한 내용은 [LICENSE.md](LICENSE.md)를 참조하세요.
