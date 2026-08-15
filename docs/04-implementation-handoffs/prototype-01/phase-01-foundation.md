# Prototype 01 Phase 1 Implementation Handoff — Legacy

Status: Legacy  
Version: 1.5  
Historical Approval: 2026-08-02  
Migration Status: Migrated to System Spec  
Superseded By: [Prototype 01 Phase 1 System Spec v2.1](../../03-system-specs/prototype-01/phase-01-foundation.md)  
Source System Spec: Prototype 01 Phase 1 System Spec v1.4（Historical）  
Source main HEAD: `4f11c9595b29bc8cfc3b9615c38589f323fa42f3`  
Unity Repository: [undershot0704/Project-IYASAKA-Unity](https://github.com/undershot0704/Project-IYASAKA-Unity)  
Unity Repository HEAD: `7c52d3e2089eb080577f7779c2f5d5e6c42eb95a`  
Implementation Use: Prohibited  
Normative Authority: None  
Unity Implementation: Prohibited  
Last Updated: 2026-08-15

> **Legacy Document:** 本文はCamera v1までの変更管理・実装記録として凍結保存する。Prototype 01 Phase 1 Camera v2以降の正式仕様、実装判断、停止条件、承認GateまたはCodex Promptの根拠として使用しない。Phase 1の唯一の正式実装仕様は、現行の[Phase 1 System Spec](../../03-system-specs/prototype-01/phase-01-foundation.md)である。本Legacy HandoffをCamera v2へ更新せず、Phase 1では今後Implementation Handoffを新規作成・更新しない。

## 1. Purpose

本書は、承認済みのPrototype 01 Phase 1 System Specを、Codexまたは実装担当AIへ渡す具体的な作業指示へ変換するためのImplementation Handoffである。

対象はPhase 1「基盤構築」に限定する。本書はゲーム仕様やSystem Specを追加・変更せず、実装対象、確認方法、成果物、停止条件を明確にする。

## 2. Source of Truth

実装時は、次の順序で承認済み文書を参照する。

1. [Prototype 01 Phase 1 System Spec v1.3](../../03-system-specs/prototype-01/phase-01-foundation.md)
2. [Prototype 01 PDD v1.0](../../02-prototypes/prototype-01/pdd.md)
3. [GDD v1.0](../../01-gdd/gdd.md)
4. 承認済みの本Implementation Handoff
5. Unity実装リポジトリの`AGENTS.md`
6. [Prototype 01 Roadmap](../../02-prototypes/prototype-01/roadmap.md)

本書は上位文書を上書きしない。文書間の矛盾、意味の分岐、必要情報の欠落を発見した場合は、推測で実装せず停止して報告する。

実装開始時には、承認済み文書のバージョンと対象コミットを再確認する。

Unity実装リポジトリの`AGENTS.md`は実装時の必須参照文書だが、承認済みSystem Spec、PDD、GDD、Implementation Handoffを上書きしない。

## 3. Implementation Scope

Phase 1で実装対象とする成果は次のとおり。

- 正方形グリッドの基盤
- セル座標とWorld座標の相互変換
- 2D固定俯瞰を維持したカメラ移動
- ズーム
- `Paused`、`Normal`、`Fast`の時間制御
- 累積`Simulation Elapsed Time`
- Phase 1検証用表示
- Phase 2準備用のStart CellとDestination Cellの指定・確認
- 上記機能に必要な入力・設定値の検証とエラー表示
- 上記機能を検証するための最小限のテスト基盤

Phase 1検証用表示では、少なくとも次を確認可能にする。

- セル境界
- セル中心
- 有効グリッド範囲
- 選択または指定したセル座標
- World座標から変換されたセル座標
- Start Cell座標と未指定状態
- Destination Cell座標と未指定状態
- 最後の無効なセル指定結果
- 現在の時間状態
- 累積`Simulation Elapsed Time`
- 拒否された無効入力または設定の確認情報

これは完成版UIではない。

## 4. Explicit Out of Scope

次はPhase 1では実装しない。

- 建築、資源、採取、生産、住民、仕事、物流
- 経路探索および住民移動
- セーブ・ロード
- 完成版UI、完成版アート、完成版入力設定
- カメラの自由回転
- Middle Mouse Drag、Right Mouse Drag、Touch Pan、Pinch Zoom、Gamepad Camera、Edge Scroll
- Camera慣性、Camera加減速、Camera境界、Camera追従、Camera Focus
- 完成版Input設定、Input Remapping UI、汎用Click／Drag Framework、汎用Camera Framework
- Project Settings変更、新規Package、外部Asset、Tilemap
- 最終マップ境界およびPhase 1でのカメラ移動範囲制限
- Phase 2以降の機能
- 後続Phaseを想定した先行実装
- 不要な汎用フレームワーク
- 未検証の拡張ポイント
- 早すぎる最適化
- 必要以上のクラス分割、抽象化、パッケージ追加

## 5. Repository Findings

### 5.1 確認できた事実

仕様リポジトリの基準main HEADは`8c33fa12049d8e2d79d5a46d294c31307d1b319d`である。

Unity実装リポジトリは次へバインドする。

- Repository: [undershot0704/Project-IYASAKA-Unity](https://github.com/undershot0704/Project-IYASAKA-Unity)
- Visibility: Private
- Branch: `main`
- 基準HEAD: `8adb90fe0c8ddf8cfcdb035d3e8a09a8b48a2058`
- Unity: `6000.3.20f1`（Unity 6.3 LTS）
- Template: Universal 2D
- Render Pipeline: Universal Render Pipeline `17.3.0`
- Renderer: 2D Renderer
- Input System: `1.19.0`
- Unity Test Framework: `1.6.0`
- Asset Serialization: Force Text
- `.meta`: 既存Assetに対応するファイルを確認済み
- `.asmdef`: なし
- C# scripts: なし

Scene構成は次のとおり。

- Existing Scene: `Assets/Scenes/SampleScene.unity`
- Template Scene: `Assets/Settings/Scenes/URP2DSceneTemplate.unity`

既存Input Actionsは`Assets/InputSystem_Actions.inputactions`にあり、`Player`および`UI` Action Mapを含む。Phase 1専用Action Mapは存在しない。

Git管理対象は`Assets/`、`Packages/`、`ProjectSettings/`および`.meta`である。`Library/`、`Temp/`、`Obj/`、`Logs/`、`UserSettings/`、生成された`.csproj`および`.slnx`はGit管理対象外である。

Unity実装リポジトリにはAI実装ルールとして`AGENTS.md`が存在する。

### 5.2 本Handoffで確定した事項

本Handoffでは、Phase 1の実装準備に必要な次の事項を確定する。

- Phase 1の検証用Grid、Camera、Zoom、Simulation Time設定値
- Phase 1のファイル配置とファイル名
- Phase 1確認用Sceneの新規作成方針
- namespace
- RuntimeおよびTest用`.asmdef`の最小構成
- TestのEditMode／PlayMode配置
- 既存Input Actions内への`Phase01` Action Map追加
- 入力割り当て
- 検証表示の配置と方式

これらはPrototype 01 Phase 1の検証用方針であり、完成版のマップ規模、操作設定、UI構成を確定するものではない。

## 6. File Plan

以下をPhase 1の確定File Planとする。フォルダは実際にファイルが必要になった時点で作成し、空フォルダや将来用フォルダを先行作成しない。

| 種別 | 推奨パス／ファイル名 | 責務 | 依存・注意 |
|---|---|---|---|
| 新規 | `Assets/IYASAKA/Scripts/Grid/GridFoundation.cs` | グリッド設定、有効範囲、座標変換 | 住民、経路探索、建築の責務を持たせない |
| 新規 | `Assets/IYASAKA/Scripts/Camera/Phase01CameraController.cs` | 固定俯瞰カメラのKeyboard移動、Mouse Wheel Zoom、Left Drag Pan | 自由回転、最終境界、追従機能を追加しない |
| 新規 | `Assets/IYASAKA/Scripts/Simulation/SimulationTimeController.cs` | 時間状態、倍率、累積Simulation Elapsed Time | 生産、住民、建築の更新を持たせない |
| 新規 | `Assets/IYASAKA/Scripts/Debug/Phase01VerificationDisplay.cs` | Phase 1検証情報、常時表示のGame View Grid／Start／Destination、F1切替のVerification Overlay | 完成版UI・汎用Grid Rendererとして作らない |
| 新規 | `Assets/IYASAKA/Scripts/Phase01Bootstrap.cs` | Phase 1構成要素の最小限の接続 | 汎用DI基盤やサービスロケータを導入しない |
| 新規 | `Assets/IYASAKA/Scenes/Phase01Foundation.unity` | Phase 1の主要確認Scene | Template SceneとSampleSceneを直接変更しない |
| 新規 | `Assets/IYASAKA/Tests/EditMode/GridFoundationTests.cs` | 座標変換、境界、無効入力 | Unity Test Framework `1.6.0`を使用する |
| 新規 | `Assets/IYASAKA/Tests/EditMode/SimulationTimeControllerTests.cs` | 時間状態と累積値 | Unity Test Framework `1.6.0`を使用する |
| 条件付き | `Assets/IYASAKA/Tests/PlayMode/Phase01CameraControllerTests.cs` | Camera入力とRuntime統合 | EditModeで確認不能な場合のみ作成する |

次の最小フォルダだけを作成候補とする。

- `Assets/IYASAKA/Scripts/Grid/`
- `Assets/IYASAKA/Scripts/Camera/`
- `Assets/IYASAKA/Scripts/Simulation/`
- `Assets/IYASAKA/Scripts/Debug/`
- `Assets/IYASAKA/Scenes/`
- `Assets/IYASAKA/Tests/EditMode/`
- `Assets/IYASAKA/Tests/PlayMode/`（PlayModeテストを採用する場合のみ）

Phase 2向けフォルダ、将来機能用の空フォルダ、Framework層、Domain／Application／Infrastructure等のレイヤーは作成しない。

### 6.1 asmdef方針

現時点のUnity実装リポジトリには`.asmdef`が存在しない。

Phase 1ではRuntimeとTestsを分離するため、必要最小限の構成だけを導入する。

- Runtime: `Assets/IYASAKA/Scripts/IYASAKA.Runtime.asmdef`
- EditMode Tests: `Assets/IYASAKA/Tests/EditMode/IYASAKA.Tests.EditMode.asmdef`
- PlayMode Tests: `Assets/IYASAKA/Tests/PlayMode/IYASAKA.Tests.PlayMode.asmdef`（PlayModeテストが必要な場合のみ）

Runtime asmdefは`Assets/IYASAKA/Scripts/`配下を対象とし、EditMode TestsはRuntimeを参照する。PlayMode TestsはEditModeで確認不能な挙動を検証する場合のみ作成し、空構成を先行作成しない。Phase 2以降を想定したAssembly分割、およびGrid／Camera／Simulationごとの個別asmdefは作成しない。

### 6.2 Scene方針

Phase 1の主要確認Sceneとして`Assets/IYASAKA/Scenes/Phase01Foundation.unity`を新規作成する。

`Assets/Settings/Scenes/URP2DSceneTemplate.unity`は直接変更しない。`Assets/Scenes/SampleScene.unity`はテンプレート由来Sceneとして保持し、Phase 1実装の主要確認Sceneには使用しない。

### 6.3 Namespace方針

RuntimeおよびTestのルートnamespaceは`IYASAKA`とする。必要に応じて次のサブnamespaceを使用できる。

- `IYASAKA.Grid`
- `IYASAKA.Camera`
- `IYASAKA.Simulation`
- `IYASAKA.Debugging`
- `IYASAKA.Tests`

不要に深いnamespace階層は作成しない。

## 7. Component and Class Responsibilities

実装時は責務を明確にするが、Phase 1のために必要以上に分割しない。

### 7.1 Grid Foundation

- 検証用設定として幅`64`、高さ`64`、セルサイズ`1` Unity Unit、原点`(0, 0)`を使用する。
- 有効なグリッド範囲を定義する。
- Cell to WorldおよびWorld to Cellを提供する。
- X軸・Y軸ともにグリッド全体を半開区間`[0, 64)`として扱い、各セルも`[min, max)`の境界規則を守る。
- グリッド全体の最大外周境界は範囲外として扱う。
- 無効設定または非有限座標を拒否し、観測可能な情報を残す。

これらの値はPhase 1の検証条件であり、完成版の最終マップ規模を確定しない。

### 7.2 Camera Controller

- 2D固定俯瞰の前提を維持する。
- Orthographic Cameraを使用し、固定回転を維持する。
- Runtime中のカメラ回転を禁止する。
- 平面上のカメラ移動を`12` Unity Units／秒で処理する。
- ズームをMinimum `4`、Maximum `24`、Initial `10`の範囲で処理する。
- 時間依存のZoom Speedは使用せず、Zoom Units Per Notchを`20 / 3`（約`6.67` Orthographic Size Units／標準刻み）とする。
- Mouse Scroll Yを標準的なホイール刻みへ正規化し、約3刻みで`4`から`24`、または`24`から`4`へ到達させ、最終値を必ず`4`〜`24`へClampする。
- Trackpad等の連続スクロールは正規化前の連続性を尊重しつつ、単一入力で極端な飛躍や操作不能を起こさない。
- Left Mouse ButtonのPointer Down、Pointer Position／Delta、Pointer Upを受け、画面上の累積移動距離がDrag Thresholdを超えた場合にCamera Panを開始する。
- Drag Thresholdの初期値は`8 pixels`とし、Human Verificationで通常のClickを誤ってDragにしない範囲（目安`5`〜`10 pixels`）だけを調整対象とする。
- Screen PositionをCamera平面上のWorld Positionへ変換し、前回位置との差分をCameraへ逆向きに適用する。右へドラッグした場合はマップ内容が右へ動いて見える向きとする。
- Drag移動量へdelta timeを重ねて乗算せず、Pointer変位そのものへ追従させる。Zoom `4`と`24`のどちらでも自然なWorld Space移動量とする。
- WASD／Arrow Keys移動、Drag、ZoomはフレームレートおよびSimulation Time Multiplierの影響を受けず、simulation-scaled delta timeを使用しない。
- `Paused`、`Normal`、`Fast`のいずれでも同じKeyboard移動、Drag追従、Zoom量を使用し、`Paused`中もすべて処理する。
- Phase 1では移動範囲を制限しない。
- 自由回転を実装しない。
- 起動時のCamera初期XYは、グリッド中心を観察できる`(32, 32)`とする。
- 起動時のOrthographic Sizeは`10`とする。
- Z座標は新しいゲーム仕様として固定せず、既存Unity 2D Scene／Camera構成に適した値を使用し、既存の2D Camera構成を破壊しない。

後続Phaseでカメラ境界を追加できる構造を妨げないが、Phase 1では最終マップ境界を先取りしない。

### 7.3 Simulation Time Controller

- `Paused`、`Normal`、`Fast`を切り替える。
- 初期状態は`Normal`、初期倍率は`1x`とする。
- 適用倍率は`Paused = 0x`、`Normal = 1x`、`Fast = 4x`とする。
- 現在状態と適用倍率を公開する。
- `Simulation Elapsed Time`を累積する。
- `Simulation Elapsed Time`の増加量へ現在のSimulation Multiplierを反映し、`Paused`中は増加させない。
- Space相当のPause／Resume要求をToggleとして処理する。
- NormalまたはFastからPausedへ入る際、直前の非Paused状態を保存する。
- Paused中のSpace相当要求では保存済みのNormalまたはFastへ復帰し、復帰先未定義時はNormalへ安全に復帰する。
- Paused中のKeyboard `1`／`2`相当要求ではNormal／Fastへ直接移行する。
- 状態変更後の次回Pause時は、新しい非Paused状態を復帰先として記憶する。
- 状態変更時も累積値をリセット、逆行、または不正に飛躍させず、連続性を維持する。

Elapsed Timeだけのための独立コンポーネントは必須とせず、単純性が保てる場合はTime Controllerへ含める。

### 7.4 Verification Display Presenter

- Scene Viewでは、グリッド外周、セル境界、セル中心、有効範囲、選択または指定したセル、World座標から変換されたセル座標を観測可能にする。
- Scene ViewではStart CellとDestination Cellを色、ラベル、形状のいずれかで明確に区別し、同一セルの場合も両方が指定済みであることを確認可能にする。
- Game Viewでは、グリッド外周とセル境界をCamera移動およびZoomに追従して常時表示する。
- Game Viewでは、Start CellとDestination Cellを別セル・同一セルのどちらでも常時識別可能にする。
- Game View左上のVerification Overlayでは、Grid Width、Grid Height、Cell Size、Grid Origin、Valid Cell Range、Grid Configurationの検証状態を観測可能にする。
- Verification Overlayでは、Camera Position、Orthographic Size、Simulation Time State、Time Multiplier、Elapsed Time、World Position、Cell Position、Start Cell座標、Destination Cell座標、各セルの未指定状態、Invalid操作結果（最後の無効指定結果）、その他Debug情報またはエラーを観測可能にする。
- `F1`はGame View左上のVerification Overlayだけを切り替える。Grid外周、セル境界、Start Cell、Destination Cell、同一Cell表示は`F1`の状態にかかわらず表示を維持する。
- 今回のUnity修正対象は原則`Phase01VerificationDisplay.cs`だけとし、Camera Controller、Grid Foundation、Simulation Time Controller、Bootstrap、Input Actions、Sceneの責務または接続を変更しない。
- Game View GridはPhase 1専用の簡易検証表示とし、64×64表示で実用上問題となる著しい負荷を発生させない。
- 完成版UI、完成版アート、Tilemap等の将来用ゲーム基盤、汎用Grid Renderer Framework、汎用デバッグフレームワークの責務を持たない。
- Unity標準機能によるPhase 1専用の最小表示とし、新しいUIフレームワークを導入しない。
- Scene View表示はGizmos／Handles等、Game View表示は追加Packageを必要としないUnity標準の簡易表示を推奨する。ただし、既存Unity構成との整合上、要件を満たすより単純な標準方式があれば採用できる。
- Scene View／Game Viewの具体的な描画方式は、Approved仕様の責務と表示要件を満たす範囲でCodexの最小実装裁量とする。
- 外部Asset、完成版UI、汎用UI基盤、将来用フレームワークを追加しない。
- Left Mouse操作がDrag Thresholdを超えずにPointer Upした場合だけ、Release位置をWorld to Cell変換し、有効セルの場合のみStart Cellを更新する。
- Drag開始後は、そのPointer操作のDrag中およびPointer UpでStart Cellを更新しない。
- Drag前のStart Cell／Destination Cellを維持し、無効位置でDragを終了しても既存指定を失わない。
- マウス右クリック位置をWorld to Cell変換し、有効セルの場合のみDestination Cellを更新する。Right Clickの挙動は変更しない。
- Start CellとDestination Cellは別の状態として保持し、同一セルの指定を許可する。
- 無効セルまたはグリッド範囲外の指定では既存の有効指定を維持し、無効結果を観測可能にする。
- セル指定によって経路探索、住民移動、経路可否判定を開始しない。

### 7.5 Bootstrap / Scene Composition

- Phase 1に必要な構成要素をScene内で接続する。
- `SetStartCell`のPress／Release、Pointer Position／Delta、Camera Panを最小構成で接続する。
- Pointer Down位置、累積Pixel距離、Drag開始済み状態を一箇所で管理し、Click／Drag判定責務をCamera ControllerとVerification Displayへ重複させない。
- Drag判定後はCamera ControllerへScreen Position差分を渡し、Click判定後だけVerification DisplayへRelease位置を渡す。
- 初期設定を検証し、無効な構成で実行状態へ移行しない。
- 後続Phase用の登録・拡張基盤を先行して作らない。
- GameObject名、Scene Hierarchy、Componentの接続方法は、Approved仕様の責務と表示要件を満たす範囲でCodexの最小実装裁量とする。
- 指定済み5 Scriptsの責務を不必要に分割せず、新規Package、外部Asset、完成版UI、汎用UI基盤、将来用フレームワークを追加しない。

### 7.6 Validation / Error Reporting

- 各コンポーネントが自身の入力と設定を検証する。
- Phase 1専用の中央エラー管理基盤は新設しない。
- 拒否理由をConsoleログまたは検証表示で確認可能にする。

## 8. Data and Configuration

次の値をPhase 1の検証条件として使用する。これらは完成版の最終値ではない。

| 項目 | 状態 | Phase 1設定 |
|---|---|---|
| Grid Width / Height | Resolved | `64 x 64` |
| Cell Size | Resolved | `1` Unity Unit |
| Grid Origin | Resolved | `(0, 0)` |
| Camera Projection / Rotation | Resolved | Orthographic／固定回転 |
| Camera Initial XY / Z | Resolved | XY `(32, 32)`。Zは既存Unity 2D Camera構成に適した値 |
| Camera Move Speed | Resolved | `12` Unity Units／秒 |
| Zoom Minimum / Maximum | Resolved | `4`／`24` |
| Initial Zoom | Resolved | `10` |
| Zoom Units Per Notch | Resolved | `20 / 3`（約`6.67` Orthographic Size Units／標準刻み） |
| Left Drag Threshold | Resolved | 初期値`8 pixels`。Human Verificationで`5`〜`10 pixels`目安内を調整 |
| Paused / Normal / Fast | Resolved | `0x`／`1x`／`4x` |
| Initial Time State / Multiplier | Resolved | `Normal`／`1x` |
| Start Cell / Destination Cell | Resolved | 初期状態は未指定。別状態として保持し、同一セル指定を許可 |
| Input Bindings | Resolved | §9の`Phase01` Action Map |
| Verification Display | Resolved | Scene View表示、Game Viewの常時Grid／Start／Destination表示、および左上Verification Overlayだけを切り替える`F1` |

- 非有限値、ゼロ以下のCell Size、無効なGrid Width／Height、Zoom Min/Maxの逆転を有効な設定として受理しない。
- 有限なInitial Zoomが`4`未満なら`4`へ、`24`を超えるなら`24`へ制限し、`4`〜`24`ならその値を使用する。非有限値は拒否する。
- Camera Move Speedは`0`以上の有限値を受理し、負または非有限な値を拒否する。Phase 1の正式値は`12` Unity Units／秒とする。
- Zoom Units Per Notchは正の有限値だけを受理し、非有限値または`0`以下を拒否する。Phase 1の正式値は`20 / 3`とする。
- Drag Thresholdは正の有限なPixel値を使用し、初期値は`8 pixels`とする。過剰な設定UIまたは汎用入力設定へ拡張しない。
- Fast Multiplierは`1`より大きい有限値を受理し、`1`以下または非有限な値を拒否する。Phase 1の正式値は`4x`とする。
- Start CellとDestination Cellは個別の未指定状態または有効セル座標を保持し、無効指定では既存の有効値を上書きしない。
- 制限または拒否の結果をログまたはVerification Displayで確認可能にする。

## 9. Input Mapping

Unity Input System `1.19.0`と既存の`Assets/InputSystem_Actions.inputactions`を使用し、新規`.inputactions`ファイルまたは新しい入力Packageは追加しない。既存の`Player`および`UI` Mapへ混在させず、次の`Phase01` Action Mapを追加する。

| Action | Action Type | Control Type | Binding |
|---|---|---|---|
| `CameraMove` | Value | Vector2 | `WASD`およびArrow Keys |
| `CameraZoom` | Value | Axis | Mouse Scroll Y |
| `CameraPan` | Pass Through | Vector2 | Pointer Delta。Left Button押下中だけ使用 |
| `PointerPosition` | Pass Through | Vector2 | Pointer Position |
| `Pause` | Button | — | `Space`（Pause／Resume Toggle） |
| `NormalSpeed` | Button | — | Keyboard `1` |
| `FastSpeed` | Button | — | Keyboard `2` |
| `ToggleVerificationDisplay` | Button | — | `F1` |
| `SetStartCell` | Button | — | Mouse Left Button |
| `SetDestinationCell` | Button | — | Mouse Right Button |

既存`SetStartCell`のLeft Mouse Button Bindingと`SetDestinationCell`のRight Mouse Button Bindingは維持する。`SetStartCell`のPress／Releaseと`PointerPosition`／`CameraPan`をBootstrapで一体として扱い、Threshold未満のReleaseだけをStart Cell指定へ渡す。SpaceはPause専用ではなく、直前の非Paused状態へ復帰するPause／Resume Toggleとして扱う。今回のVerification Display変更では`ToggleVerificationDisplay`を含むActionまたはBindingを変更しない。

Middle Mouse Drag、Right Mouse Drag、Touch Pan、Pinch Zoom、Gamepad Camera、Edge Scroll、Camera慣性／加減速／境界／追従／FocusはPhase 1へ追加しない。完成版Input設定、Input Remapping UI、汎用Click／Drag Framework、汎用Camera Framework、新旧Input Systemの併用、新しい入力スタック、新しいPackage、Project Settings変更も追加しない。

## 10. Runtime Flow

実装後に必要となる実行上の流れを、Unityライフサイクルの具体的選択へ踏み込みすぎない範囲で示す。

1. Phase 1設定を読み取る。
2. Grid、Camera、Timeの設定値を検証する。
3. 無効設定がある場合は対象設定を拒否し、理由を表示して無効な実行状態へ移行しない。
4. Gridを初期化する。
5. Cameraを固定俯瞰の前提で初期化し、初期XYをグリッド中心`(32, 32)`へ配置する。Zは既存Unity 2D Camera構成に適した値を維持する。
6. Timeを`Normal`、`1x`で初期化する。
7. 検証表示を初期化する。
8. 各フレームで入力を受け取り、simulation-scaled delta timeを使わず、フレームレートに依存しない形でKeyboard移動と正規化したMouse Wheel Zoomを処理し、時間状態へ反映する。
9. Left Pointer Downで開始位置と既存指定を保持し、Pointer移動の累積Pixel距離がThresholdを超えた時点でDragへ確定する。
10. Drag中はScreen PositionからWorld Positionへの差分をCameraへ適用し、Start Cell指定を抑止する。
11. Dragへ確定せずPointer Upした場合だけ、Release位置をWorld to Cell変換し、有効セルの場合だけStart Cellを更新する。Right Clickでは従来どおりDestination Cellだけを更新する。
12. 時間状態に従って`Simulation Elapsed Time`を更新する。
13. 無効セルまたは範囲外のClick／Drag終了では既存の有効指定を維持し、無効結果を記録する。
14. Grid外周、セル境界、Start Cell、Destination Cell、同一Cell表示をGame Viewへ常時反映し、Grid情報、座標変換結果、時間状態、累積値、エラー情報をF1切替のVerification Overlayへ反映する。
15. その他の無効な実行時入力は無視し、有効な直前状態を維持して理由を観測可能にする。

## 11. Error and Edge-Case Requirements

- Grid WidthまたはHeightが無効な場合、設定を拒否する。
- Cell Sizeが非正値または非有限の場合、設定を拒否する。
- Grid Originを含むGrid設定値が`NaN`、`Positive Infinity`、`Negative Infinity`の場合、設定を拒否する。
- World to Cellへ渡すWorld座標が非有限の場合、入力を拒否する。
- Zoom Minimum、Maximum、Initial Zoomが非有限の場合、設定を拒否する。
- Zoom入力値が非有限の場合、入力を無視する。
- Zoom Units Per Notchが`0`以下または非有限の場合は拒否し、現在の有効値を上書きしない。
- Mouse Scroll Yの正規化後も最終Zoom値を必ず`4`〜`24`へClampする。
- Drag Thresholdが無効な場合は既定の`8 pixels`を使用し、Clickを無条件にDragへ変換しない。
- Drag開始後はPointer Up位置が有効セルでもStart Cellを更新しない。
- Drag中またはDrag終了時にStart Cell／Destination Cellを失わない。
- Zoom MinimumがMaximumを上回る場合、設定を拒否する。
- 有限なInitial Zoomが`4`未満の場合は`4`へ、`24`を超える場合は`24`へ制限する。
- 有限なInitial Zoomが`4`〜`24`の場合は、その値を使用する。
- Initial Zoomが`NaN`、`Positive Infinity`、`Negative Infinity`の場合は設定を拒否する。
- Initial Zoomの制限または拒否結果をConsoleログまたはVerification Displayで確認可能にする。
- Camera Move Speedは`0`以上の有限値だけを受理する。負の値、`NaN`、`Positive Infinity`、`Negative Infinity`を拒否し、現在の有効値を上書きしない。
- Camera Move Speedの検証結果をConsoleログまたはVerification Displayで確認可能にする。
- 初期Time状態は`Normal`、初期倍率は`1x`とする。
- 未定義のTime状態を拒否し、現在の有効状態を維持して理由をConsoleログまたはVerification Displayで確認可能にする。
- Fast Multiplierが`1`以下、`NaN`、`Positive Infinity`、`Negative Infinity`の場合は拒否し、内部状態を更新しない。Phase 1の有効値は`4x`とする。
- 無効なFast Multiplierの拒否理由をConsoleログまたはVerification Displayで確認可能にする。
- Start CellまたはDestination Cellの指定位置が無効セルまたはグリッド範囲外の場合、対応する既存の有効指定を維持し、無効指定で上書きしない。
- Start CellとDestination Cellが同一セルである場合は、両方の有効な指定として保持する。
- 無効なセル指定結果をConsoleログまたはVerification Displayで確認可能にする。
- 無効入力や設定によってクラッシュ、未定義動作、無効な内部状態への遷移を起こさない。
- 拒否後も直前の有効状態を維持する。
- 拒否理由をConsoleログまたは検証表示で確認可能にする。
- 各セルは`[min, max)`として扱い、最小境界を含み最大境界を含まない。
- グリッド全体の最大外周境界上の座標は範囲外とする。
- `Paused`中は`Simulation Elapsed Time`を増加させない。
- `Normal`では`1x`で増加させる。
- `Fast`では承認済み倍率で増加させる。
- Pause／ResumeおよびNormal／Fast切替で累積値をリセット、逆行、または不正に飛躍させない。
- Paused中のSpace相当要求では、保存された直前のNormalまたはFastへ復帰する。
- 復帰先が未定義の場合はNormalへ安全に復帰する。
- Game View Gridは無効なGrid設定を正常表示として扱わない。

## 12. Implementation Order

1. Unity実装リポジトリのHEADが本書のRepository Bindingと一致することを確認する。
2. 本Handoffをレビューし、承認する。
3. 承認されたファイル、Scene、入力、テスト／`.asmdef`構成を実装計画で再確認する。
4. Grid設定と座標変換を実装する。
5. Gridの自動テスト候補を実装・実行する。
6. Simulation TimeとElapsed Timeを実装する。
7. Timeの自動テスト候補を実装・実行する。
8. 固定俯瞰Cameraの移動とズームを実装する。
9. Start CellとDestination Cellの検証用指定・状態保持を実装する。
10. Phase 1検証表示を実装する。
11. Phase 1確認用Sceneへ最小限に接続する。
12. 自動テスト、Console確認、人間によるUnity動作確認を行う。
13. Completion Evidenceを収集し、Phase 1完了判定へ渡す。

## 13. Automated Test Candidates

導入済みのUnity Test Framework `1.6.0`を使用する。テストコードは本Handoff更新では作成しない。

### EditMode：Grid / Coordinate Conversion

- Cell to Worldの既知入力と期待値
- World to Cellの既知入力と期待値
- 往復変換の整合
- セルの最小境界を含む
- セルの最大境界を含まない
- グリッド全体の最大外周境界が範囲外
- 負座標、範囲外座標の扱い
- `NaN`、`Positive Infinity`、`Negative Infinity`の拒否
- 無効Grid設定の拒否
- 拒否後に有効状態が保持される
- 有効セル指定によるStart CellとDestination Cellの個別更新
- 無効セルまたは範囲外指定による既存指定の維持
- Start CellとDestination Cellへの同一セル指定
- Drag Threshold未満のPointer操作をClickとしてStart Cell指定へ渡す
- Drag Threshold超過後にStart Cell指定を発火しない
- Drag後もStart Cell／Destination Cellを維持する

### EditMode：Camera / Zoom／Pan設定検証

- Zoom Minimum／Maximum／Initial Zoomの検証
- Min/Max逆転の拒否
- 非有限Zoom設定と入力の拒否
- 有限な範囲外Initial Zoomの`4`または`24`への制限
- Zoomが承認済み範囲を超えない
- 標準ホイール3刻み相当で`4`から`24`、`24`から`4`へ到達する
- Zoom Units Per Notch `20 / 3`の受理
- 非有限または`0`以下のZoom Units Per Notchの拒否と有効値の維持
- 異なるZoom値でScreen Position差分が自然なWorld Space Panへ変換される
- Camera Move Speed `12`の受理
- Camera Move Speed `0`の受理
- 負または非有限なCamera Move Speedの拒否と有効値の維持

### EditMode：Simulation Time

- 初期状態が`Normal`、初期倍率が`1x`
- NormalからSpace相当操作でPausedへ移行する
- NormalからPauseした後のResumeでNormalへ戻る
- FastからSpace相当操作でPausedへ移行する
- FastからPauseした後のResumeでFastへ戻る
- Pause／ResumeでElapsed Timeがリセットされない
- Pause／ResumeでElapsed Timeが逆行または不正に飛躍しない
- Paused中はElapsed Timeが増加しない
- Paused中にNormalを選択できる
- Paused中にFastを選択できる
- 状態変更後の次回Pauseで新しい非Paused状態を記憶する
- 復帰先未定義時はNormalへ安全に復帰する
- `Normal`中に`1x`で増加する
- `Fast`中に指定倍率で増加する
- 未定義のTime状態の拒否と有効状態の維持
- Fast Multiplierが`1`以下または非有限の場合の拒否と有効状態の維持
- Phase 1のFast Multiplier `4x`の受理

### EditModeまたはPlayMode：Game View Grid（合理的な範囲）

- Game View用Grid表示Componentまたは描画処理がSceneへ接続されている
- Grid設定と表示範囲が一致する
- Start Cell／Destination Cell表示状態を受け取れる
- Verification OverlayのON／OFF状態とGame View Grid／Start Cell／Destination Cell／同一Cell表示の描画状態を分離できる
- Verification OverlayをOFFにしても常時表示対象が描画され、ONにすると必須Debug情報だけが追加表示される
- 無効Grid設定を正常表示として扱わない
- 視認性そのものはHuman Verificationを正とする

### PlayMode：Camera入力とRuntime統合（必要な場合のみ）

EditModeで確認不能な場合に限り、次をPlayModeで検証する。

- `Phase01` Input ActionsによってCameraが移動する
- ズーム入力がRuntimeで適用され、標準ホイール約3刻みで全範囲を移動する
- Left DragでCamera Panし、Drag後に`SetStartCell`を発火しない
- Threshold未満のLeft ClickとRight Clickが`SetStartCell`と`SetDestinationCell`を個別に更新する
- Drag前後でStart Cell／Destination Cellを維持する
- Zoom `4`と`24`でPointerへ追従するWorld Space Panになる
- 自由回転が発生しない
- Phase 1ではCamera移動境界が適用されない
- `Paused`中もKeyboard移動、Left Drag、Zoomが機能する
- `Paused`、`Normal`、`Fast`でKeyboard移動速度、Drag追従量、Zoom量が変化しない

### Human Verification中心

- セル境界、セル中心、有効範囲、指定セル、Start Cell、Destination Cell、変換結果の表示
- 同一セルをStart CellとDestination Cellへ指定した場合の両指定の識別
- Game View左上のVerification OverlayでのGrid Width、Grid Height、Cell Size、Origin、Valid Cell Range、Grid状態、Camera Position、Orthographic Size、Simulation Time State、Time Multiplier、Elapsed Time、World Position、Cell Position、Start Cell座標、Destination Cell座標、未指定状態、Invalid操作結果、その他Debug情報またはエラーの表示
- `F1`によるVerification Overlayだけの切替と、Overlay OFF中のGrid外周、セル境界、Start Cell、Destination Cell、同一Cell表示の維持
- 無効入力または設定の拒否理由の表示
- Scene全体の固定俯瞰表示と操作感

## 14. Human Verification Procedure

1. Unity Editorで`Assets/IYASAKA/Scenes/Phase01Foundation.unity`を開く。
   - 期待結果: Phase 2以降の要素がなく、Consoleに未処理例外がない。
2. Play開始直後のグリッド表示とCamera初期状態を確認する。
   - 期待結果: 原点`(0, 0)`、`64 x 64`、セルサイズ`1` Unity UnitのグリッドをScene ViewとGame Viewで確認できる。Camera初期XYは`(32, 32)`、Initial Orthographic Sizeは`10`であり、初期Time状態は`Normal 1x`である。
3. Game Viewでグリッド外周とセル境界を確認する。
   - 期待結果: 外周と各セル境界を視認できる。
4. CameraをWASD／矢印キーおよびLeft Mouse Dragで上下左右へ移動する。Zoom `4`付近と`24`付近でもLeft Dragし、Drag前後のStart Cell／Destination Cellを比較する。
   - 期待結果: 右へDragするとマップ内容が右へ動く掴み操作になり、各方向と両Zoom値でPointerへ自然に追従する。Game View Gridが正しく追従し、固定回転とPhase 1の境界なし移動を維持する。Drag中・終了時にStart Cell／Destination Cellは変化しない。
5. Mouse ScrollでZoom Minimum `4`からMaximum `24`、MaximumからMinimumへ移動し、標準ホイール刻み数を記録する。Trackpad等の連続スクロールも利用可能なら確認する。
   - 期待結果: 両方向とも約3標準刻みで全範囲へ到達し、Orthographic Sizeは`4`〜`24`を超えない。1刻みごとの変化を視認でき、連続入力で極端な飛躍や操作不能がなく、Game View Gridが正しく追従する。
6. Pointerをほぼ動かさない短いLeft Click、Drag Threshold未満の微小移動、Thresholdを超えるLeft Dragを行い、別の有効セルへRight Clickする。
   - 期待結果: 短いClickとThreshold未満の操作だけがRelease位置のStart Cellを指定する。Threshold超過後はCamera PanとなりStart Cellを指定しない。Right Clickは従来どおりDestination Cellだけを指定し、Scene View、Game View Grid、Game View左上表示で両者を区別できる。
7. Start CellとDestination Cellを同一の有効セルへ指定する。
   - 期待結果: Game Viewでも両方の指定を識別できる。
8. 無効セルまたはグリッド範囲外を左右それぞれでクリックする。
   - 期待結果: 既存の有効指定が維持され、無効結果を観測できる。経路探索や住民移動は開始しない。
9. Normal `1x`でSpaceを押し、もう一度Spaceを押す。
   - 期待結果: `Normal 1x → Paused 0x → Normal 1x`と遷移する。
10. Keyboard `2`でFast `4x`へ移行し、Spaceを2回押す。
    - 期待結果: `Fast 4x → Paused 0x → Fast 4x`と遷移する。
11. Pause／Resume前後のSimulation Elapsed Timeを観察する。
    - 期待結果: リセット、逆行、不正な飛躍がなく、Paused中は増加しない。
12. Paused中にKeyboard `1`を押す。
    - 期待結果: Normal `1x`へ移行する。
13. Paused中にKeyboard `2`を押す。
    - 期待結果: Fast `4x`へ移行する。
14. Paused中にWASD／Arrow Keys移動、Left Drag、Zoomを行い、Normal／Fastでも同じ入力を比較する。
    - 期待結果: Paused中もすべて操作でき、Keyboard速度、Drag追従量、1標準刻みあたりのZoom量はSimulation Time倍率に依存しない。Drag後もStart Cell／Destination Cellは維持される。
15. Game View Grid表示中にClick、Drag、Zoomを連続して操作し、操作性を確認する。
    - 期待結果: 64×64表示による著しい操作遅延、入力遅延、描画負荷がなく、意図しないClick／Drag判定が頻発しない。
16. 最小境界、セル内部、セル境界直前、最大外周境界を指定する。
    - 期待結果: 半開区間規則どおりに判定される。
17. 有限なInitial Zoomとして`4`未満、`4`〜`24`、`24`超の値を与える。
    - 期待結果: 範囲外はClampされ、範囲内はその値を使用する。
18. 検証可能な方法で非有限座標、無効Grid設定、非有限Zoom設定／入力、非有限または0以下のZoom Units Per Notch、負または非有限なCamera Move Speed、未定義Time状態、不正Fast Multiplierを与える。
    - 期待結果: 設定拒否または入力無視となり、直前の有効状態を維持して理由を観測できる。
19. Start CellとDestination Cellを別セルおよび同一セルへ指定した状態で、`F1`によりGame View左上のVerification OverlayをOFF／ONする。
    - 期待結果: OFFではVerification Overlayだけが消え、Grid外周、セル境界、Start Cell、Destination Cell、同一Cell表示は見える。ONでは常時表示を維持したまま、Game View左上に必須Debug情報が表示される。
20. Play終了後にConsoleを確認する。
    - 期待結果: Console Error、未処理例外、無限ログ、継続的な警告出力が`0`であり、新規Warningが原則`0`である。Game View Grid由来のError／Warningがない。

## 15. Completion Evidence

実装担当は、次を必須のCompletion Evidenceとして報告する。

### 15.1 必須

- Commit SHA
- 変更ファイル一覧
- 新規ファイル一覧
- 自動テスト結果
- Console Error／Warning
- Human Verification結果
- Inspector実測値（必要なもの）
- Scope外変更なし
- 既知制限
- 未解決事項

Human Verification結果には、§14の全手順について実施済みであることと、その成否を記録する。自動テスト結果、Console結果、Commit履歴およびGitHub PR履歴と合わせて、実装完了を追跡可能にする。

### 15.2 任意

- スクリーンショット
- 動画
- GIF
- その他の視覚的証跡

視覚的証跡はHuman Verificationを補助する目的で、必要に応じて取得する。スクリーンショット未取得のみを理由に、Draft解除、Ready for Reviewへの変更またはMergeを拒否しない。

## 16. Acceptance Mapping

System SpecのAcceptance Criteriaを変更せず、実装対象と証拠へ接続する。Human Verificationの参照番号は§14の現行20手順を正とし、手順タイトルを併記する。

| System Spec Acceptance対象 | 実装対象 | 自動テスト候補 | 人間確認 | 完了証拠 |
|---|---|---|---|---|
| 正方形グリッドと有効範囲 | Grid Foundation | 設定値、有効範囲、境界 | 手順2「初期グリッド表示」、手順16「境界判定」、手順18「無効設定」 | テスト結果、Grid全体のHuman Verification結果、状態保持結果 |
| Cell／World座標変換 | Grid Foundation | 既知値、往復、範囲外 | 手順6「別セル指定」、手順7「同一セル指定」、手順8「無効セル指定」、手順16「境界判定」 | テスト結果、座標変換の表示結果 |
| 半開区間と最大外周境界 | Grid Foundation | 境界値テスト | 手順16「境界判定」 | 境界テスト結果、確認記録 |
| 非有限Grid／World入力の拒否 | Grid Foundation | 非有限値テスト | 手順18「無効設定・入力」 | ログ、状態保持結果 |
| Grid ConfigurationのOverlay表示確認 | Verification Display | Overlay表示モデルのテスト候補 | 手順2「初期グリッド表示」、手順19「Verification Overlay切替」 | F1 ONの設定表示に関するHuman Verification結果 |
| Start CellとDestination Cellの指定 | Verification Display / Grid Foundation | 有効指定、個別状態、同一セル指定 | 手順6「別セル指定」、手順7「同一セル指定」 | テスト結果、別セル・同一セルのHuman Verification結果 |
| 無効セル指定で既存指定を維持 | Verification Display / Grid Foundation | 無効・範囲外指定後の状態維持 | 手順8「無効セル指定」 | テスト、ログ、状態維持結果 |
| Phase 2へのStart／Destination指定結果 | Verification Display / Grid Foundation | 指定状態の参照確認 | 手順6「別セル指定」〜手順8「無効セル指定」 | Start／Destination指定記録 |
| Phase 2以降の機能が未実装 | File Plan / Explicit Out of Scope / Phase 2 Protection / 実装変更範囲の制約 | 変更ファイル一覧、新規Script／Scene／Input Action／asmdef、住民・経路探索・道路効果関連、Phase 1外フォルダ／将来用ファイルの確認 | 手順1「Scene構成」、手順8「無効指定」、およびScene Hierarchy／Project構成確認 | 変更ファイル一覧、Scene構成確認結果、Project構成確認結果、Phase 2以降の機能未実装チェック結果、Scope外実装なしの明示報告 |
| 固定俯瞰Camera移動とLeft Drag Pan | Camera Controller / Bootstrap | Pointer差分、Threshold、必要に応じたPlayMode候補 | 手順4「Keyboard移動・Left Drag Pan」 | Pan前後のHuman Verification結果、Zoom別確認記録 |
| Left Click／Drag判定と指定状態維持 | Bootstrap / Verification Display / Grid Foundation | Threshold未満Click、超過Drag、Drag後の非発火と状態維持 | 手順4「Keyboard移動・Left Drag Pan」、手順6「Click／Drag判定と別セル指定」、手順8「無効セル指定」 | Start Cell指定結果、Drag後のStart不変記録、Destination影響なしの確認結果 |
| Zoom範囲、約3刻み操作、無効入力処理 | Camera Controller | 3刻み相当、Clamp、Zoom Step検証、連続入力 | 手順5「約3刻みZoom」、手順17「Initial Zoom範囲」、手順18「無効設定・入力」 | 両方向の到達結果、Zoom 4／24のHuman Verification結果、テスト、ログ |
| Camera Move Speedの検証 | Camera Controller | `12`、`0`、負値、非有限値 | 手順4「Camera移動」、手順18「無効設定・入力」 | テスト、ログ、状態維持結果 |
| Phase 1ではCamera境界なし | Camera Controller | 必要に応じたPlayMode候補 | 手順4「Camera移動」 | 確認記録 |
| Paused中のKeyboard移動、Left Drag、Zoom | Camera Controller / Bootstrap | 必要に応じたPlayMode候補 | 手順14「Paused／Normal／FastのCamera操作」 | Paused中のDrag結果、確認記録 |
| Simulation Multiplierから独立したCamera操作 | Camera Controller | Time状態別のKeyboard速度、Drag追従量、Zoom量 | 手順4「Keyboard移動・Left Drag Pan」、手順5「約3刻みZoom」、手順14「Paused／Normal／FastのCamera操作」 | Time状態別確認記録 |
| Initial Time StateがNormal | Simulation Time Controller | 初期状態と`1x`倍率 | 手順2「初期グリッド表示」 | テスト、初期表示結果 |
| Pause／Resume Toggleと直前状態復帰 | Simulation Time Controller | Normal／FastからのPause／Resume、Paused中の1／2、fallback | 手順9「Normal Pause／Resume」〜手順13「Paused中のFast選択」 | テスト、表示結果、両遷移の確認結果 |
| Game View Gridと指定Cellの常時表示 | Verification Display / Scene Composition | Scene接続、表示範囲、選択状態、Overlay状態との分離、無効設定 | 手順2「初期グリッド表示」〜手順7「同一セル指定」、手順15「Grid表示負荷」、手順19「Verification Overlay切替」、手順20「Console確認」 | Grid／指定CellとF1 OFF／ONのHuman Verification結果、Camera／Zoom追従結果、負荷・Console結果 |
| 不正Time状態とFast Multiplierの拒否 | Simulation Time Controller | 未定義状態、`1`以下、非有限値 | 手順18「無効設定・入力」 | テスト、ログ、状態維持結果 |
| Simulation Elapsed Time | Simulation Time Controller | 停止、倍率、連続性 | 手順9「Normal Pause／Resume」〜手順13「Paused中のFast選択」 | テスト、表示結果、Elapsed Time連続性 |
| Phase 1 Verification Overlay | Verification Display | Overlay表示モデルと常時表示描画状態の分離 | 手順2「初期グリッド表示」、手順6「別セル指定」〜手順8「無効セル指定」、手順11「Elapsed Time観察」、手順19「Verification Overlay切替」 | F1 OFF／ONのHuman Verification結果、Overlay切替結果、常時表示維持結果 |
| 無効状態・クラッシュ防止 | 各対象コンポーネント | 無効入力テスト | 手順8「無効セル指定」、手順17「Initial Zoom範囲」、手順18「無効設定・入力」、手順20「Console確認」 | ログ、テスト、Console結果 |

## 17. Stop Conditions

次のいずれかに該当した場合は、実装またはHandoff承認を停止して報告する。

- 承認済みGDD、PDD、System Spec、Roadmapの間に矛盾がある。
- Approved System Specと本Handoffの間に矛盾がある。
- 実装に必須の値または判断が未解決である。
- Unity実装リポジトリの基準HEADが変わり、Repository FindingsまたはFile Planと競合する。
- 基準Unity Repository HEADと実際のリポジトリに重大な差異がある。
- 承認済み入力方式、Scene方針、asmdef方針、テスト配置と実際のリポジトリ状態が一致しない。
- 既存Input構成ではStart Cell／Destination Cellの指定方式を実装できない。
- 既存コード、Scene、Prefab、命名規則と本提案が競合する。
- Phase 1 System Boundaryを超える必要が生じる。
- 新しいUnity Packageが必要になる。
- 既存SceneまたはPrefabを破損する可能性がある。
- 承認済みSystem Specの変更が必要になる。
- Acceptance Criteriaを検証できる環境が不足している。
- 解消不能なUnity Console Errorが発生する。
- ユーザーからUnity実装開始の明示許可がない。

## 18. Open Decisions

### 18.1 Resolved Decisions

| ID | 解決内容 | 状態 |
|---|---|---|
| OD-01 | Grid、Camera、Zoom、Simulation TimeのPhase 1検証値を§7および§8のとおり確定 | Resolved |
| OD-02 | 既存Input Actionsへ§9の`Phase01` Action Mapを追加し、Start／Destination指定を含むBindingを確定 | Resolved |
| OD-03 | `Assets/IYASAKA/Scenes/Phase01Foundation.unity`を新規作成し、Template SceneとSampleSceneは直接変更しない | Resolved |
| OD-04 | §6の最小asmdef、Test配置、namespaceを採用 | Resolved |
| OD-05 | Scene View表示と、Game ViewのGrid／指定Cell表示および左上Verification Overlayを配置 | Resolved |
| OD-06 | §6のファイル名とクラス名を採用 | Resolved |
| OD-07 | Camera初期XYをグリッド中心`(32, 32)`とし、Zは既存2D Camera構成に適した値を使用 | Resolved |
| OD-08 | GameObject名、Scene Hierarchy、Component接続、検証表示方式を制約内の最小実装裁量とする | Resolved |
| OD-09 | Console Error、新規Warning、既存Warningの完了基準を§23で確定 | Resolved |
| OD-10 | Codexは実装・検証・証拠整理・コミット・Draft PR作成まで行い、Draft PR作成後に停止 | Resolved |
| OD-11 | Game ViewでもGrid外周、セル境界、Start／Destinationを識別できるPhase 1専用最小表示を採用 | Resolved |
| OD-12 | Spaceを直前のNormal／Fastへ復帰するPause／Resume Toggleとする | Resolved |
| OD-13 | Mouse Wheel Zoomを約3標準刻みで全範囲移動できるZoom Units Per Notchへ変更 | Resolved |
| OD-14 | Left ClickをStart Cell指定、Drag Threshold超過後をCamera Panとし、Dragでは指定状態を変更しない | Resolved |
| OD-15 | `F1`はGame View左上のVerification Overlayだけを切り替え、Grid外周、セル境界、Start／Destination、同一Cell表示を常時維持 | Resolved |
| OD-16 | Human Verification結果を正式なCompletion Evidenceとし、スクリーンショット等の視覚的証跡を任意とする | Resolved |

### 18.2 Remaining Open Decisions

なし。

実装計画で責務を変えない軽微な表記確認が必要になった場合も、新しい機能、System Boundary、Acceptance Criteriaの変更には使用しない。

## 19. Approval Readiness

- Blocking Open Decisions: なし
- Open Decisions: なし
- Handoff Review: Completed
- Handoff Approval: Approved
- Codex Prompt Preparation: Permitted
- Unity Implementation: Prohibited

承認前に必要だった具体値および実装対象の判断はすべて解決済みであり、新たなBlocking Open Decisionは確認されていない。本書は`Approved`、`Version: 1.5`、`Implementation Use: Permitted`であり、Codex用実装プロンプト作成に使用できる。ただし、`Unity Implementation: Prohibited`であり、Unity実装を開始できない。

## 20. Repository Rules

Unity実装時は次を必須参照順として扱う。

1. Approved System Spec
2. Approved PDD
3. Approved GDD
4. Approved Implementation Handoff
5. Unity実装リポジトリの`AGENTS.md`
6. Roadmap

`AGENTS.md`はPackage、Scene／Prefab、`.meta`、生成フォルダ、検証報告等の実装安全ルールを定義する。ただし、上位の承認済み仕様文書を上書きしない。

## 21. Approval Gate

- 本書は`Approved`であり、Codex用実装プロンプト作成に使用できる。
- Unity実装時は、Approved GDD、Approved PDD、Approved System Spec、Approved Implementation Handoff、およびUnity実装リポジトリの`AGENTS.md`をすべて参照する。
- Unity Repository HEADが本書の基準値と異なる場合は、実装前に変更内容と本書との整合性を再確認する。
- 本Handoffの承認後も、ユーザーによる明示的なUnity実装開始指示が別途必要である。
- mainへのマージだけではUnity実装開始を許可しない。
- Phase 1実装専用のCodex向け実装プロンプトを別途作成・承認する。
- Blocking Open Decisionが新たに確認された場合は、Unity実装を開始せず停止して報告する。
- 明示的なUnity実装開始許可後、CodexはPhase 1実装、自動テスト、Human Verification、Completion Evidence整理、実装コミット、Draft PR作成まで行い、Draft PR作成後に停止する。
- Draft解除、Ready for Reviewへの変更、PRのMerge、`main`への直接反映、自動Merge設定は行わない。Ready化およびMergeは人間の確認後にのみ行う。

## 22. Phase 2 Protection

- Phase 2以降の住民、移動、経路探索を先行実装しない。
- Start CellとDestination Cellの指定はPhase 2準備用の状態保持と表示に限定し、経路探索、住民移動、経路可否判定を行わない。
- Phase 1のGridを完成版向け汎用フレームワークへ拡張しない。
- Cameraへ追従、回転、最終マップ境界を追加しない。
- Timeへ住民、生産、建築などの更新責務を追加しない。
- 検証表示を完成版UIへ発展させない。
- 将来利用を理由に抽象クラス、汎用イベント基盤、サービスロケータ、DI基盤を追加しない。
- 後続Phaseの要求が確定した時点で、必要な変更をそのPhaseのSystem SpecとHandoffで判断する。


## 23. Console Completion Standard

- Console Error: `0`
- 未処理例外: `0`
- Phase 1実装により新規発生したWarning: 原則`0`
- 無限ログまたは継続的な警告出力: `0`
- 実装前から存在するWarningは、次をすべて満たす場合のみ許容する。
  - 実装前後の件数と内容を記録する。
  - Phase 1変更によって増加していない。
  - Phase 1機能の動作を阻害していない。
  - Completion Evidenceへ具体的に記載する。
  - Codexが独断でScope外修正を行わない。
- 既存Warningの解消にScope外変更が必要な場合は、修正せず停止して報告する。

## 24. Codex Delivery and Draft PR Gate

明示的なUnity実装開始許可を受けたCodexは、次の地点まで実施する。

1. Phase 1実装
2. 自動テスト
3. Human Verification
4. Completion Evidence整理
5. 実装コミット
6. Draft PR作成

Draft PR作成後に停止する。次はCodexが行ってはならない。

- Draft解除
- Ready for Reviewへの変更
- PRのMerge
- `main`への直接反映
- 自動Mergeの設定
- Human Verificationを省略した完了宣言

Ready化およびMergeは、人間の確認後にのみ行う。
