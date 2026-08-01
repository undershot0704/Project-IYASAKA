# Prototype 01 Phase 1 Implementation Handoff

Status: Draft  
Version: 0.1  
Approved: Pending  
Source System Spec: [Prototype 01 Phase 1 System Spec v1.0](../../03-system-specs/prototype-01/phase-01-foundation.md)  
Source main HEAD: `8c33fa12049d8e2d79d5a46d294c31307d1b319d`  
Unity Repository: [undershot0704/Project-IYASAKA-Unity](https://github.com/undershot0704/Project-IYASAKA-Unity)  
Unity Repository HEAD: `8adb90fe0c8ddf8cfcdb035d3e8a09a8b48a2058`  
Implementation Use: Prohibited  
Unity Implementation: Prohibited  
Last Updated: 2026-08-01

> この文書は実装指示のDraftである。承認されるまで実装判断に使用してはならない。承認後も、Unity実装の明示的な許可が別途与えられるまで実装を開始してはならない。

## 1. Purpose

本書は、承認済みのPrototype 01 Phase 1 System Specを、Codexまたは実装担当AIへ渡す具体的な作業指示へ変換するためのImplementation Handoff Draftである。

対象はPhase 1「基盤構築」に限定する。本書はゲーム仕様やSystem Specを追加・変更せず、実装対象、確認方法、成果物、停止条件を明確にする。

## 2. Source of Truth

実装時は、次の順序で承認済み文書を参照する。

1. [Prototype 01 Phase 1 System Spec v1.0](../../03-system-specs/prototype-01/phase-01-foundation.md)
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
| 新規 | `Assets/IYASAKA/Scripts/Camera/Phase01CameraController.cs` | 固定俯瞰カメラの移動とズーム | 自由回転、最終境界、追従機能を追加しない |
| 新規 | `Assets/IYASAKA/Scripts/Simulation/SimulationTimeController.cs` | 時間状態、倍率、累積Simulation Elapsed Time | 生産、住民、建築の更新を持たせない |
| 新規 | `Assets/IYASAKA/Scripts/Debug/Phase01VerificationDisplay.cs` | Phase 1検証情報の表示 | 完成版UIとして作らない |
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
- Zoom Speedは`8` Orthographic Size Units／秒相当とする。
- 移動とズームはフレームレートに依存しない。
- Camera処理はSimulation Time Multiplierの影響を受けず、simulation-scaled delta timeを使用しない。
- `Paused`、`Normal`、`Fast`のいずれでも同じ移動速度を使用し、`Paused`中も移動とズームを処理する。
- Phase 1では移動範囲を制限しない。
- 自由回転を実装しない。

後続Phaseでカメラ境界を追加できる構造を妨げないが、Phase 1では最終マップ境界を先取りしない。

### 7.3 Simulation Time Controller

- `Paused`、`Normal`、`Fast`を切り替える。
- 初期状態は`Normal`、初期倍率は`1x`とする。
- 適用倍率は`Paused = 0x`、`Normal = 1x`、`Fast = 4x`とする。
- 現在状態と適用倍率を公開する。
- `Simulation Elapsed Time`を累積する。
- `Simulation Elapsed Time`の増加量へ現在のSimulation Multiplierを反映し、`Paused`中は増加させない。
- 状態変更時も累積値の連続性を維持する。

Elapsed Timeだけのための独立コンポーネントは必須とせず、単純性が保てる場合はTime Controllerへ含める。

### 7.4 Verification Display Presenter

- Scene Viewでは、グリッド外周、セル境界、セル中心、有効範囲、選択または指定したセル、World座標から変換されたセル座標を観測可能にする。
- Scene ViewではStart CellとDestination Cellを色、ラベル、形状のいずれかで明確に区別し、同一セルの場合も両方が指定済みであることを確認可能にする。
- Game View左上では、Grid Width、Grid Height、Cell Size、Grid Origin、Valid Cell Range、Grid Configurationの検証状態を観測可能にする。
- Game View左上では、カメラ位置、Orthographic Size、時間状態、適用倍率、累積Simulation Elapsed Time、現在のWorld座標、変換されたセル座標、Start Cell座標、Destination Cell座標、各セルの未指定状態、最後の無効指定結果、その他の検証結果またはエラーを観測可能にする。
- Game Viewの検証表示は`F1`で切り替え可能にする。
- 完成版UIや汎用デバッグフレームワークの責務を持たない。
- Unity標準機能によるPhase 1専用の最小表示とし、新しいUIフレームワークを導入しない。
- マウス左クリック位置をWorld to Cell変換し、有効セルの場合のみStart Cellを更新する。
- マウス右クリック位置をWorld to Cell変換し、有効セルの場合のみDestination Cellを更新する。
- Start CellとDestination Cellは別の状態として保持し、同一セルの指定を許可する。
- 無効セルまたはグリッド範囲外の指定では既存の有効指定を維持し、無効結果を観測可能にする。
- セル指定によって経路探索、住民移動、経路可否判定を開始しない。

### 7.5 Bootstrap / Scene Composition

- Phase 1に必要な構成要素をScene内で接続する。
- 初期設定を検証し、無効な構成で実行状態へ移行しない。
- 後続Phase用の登録・拡張基盤を先行して作らない。

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
| Camera Move Speed | Resolved | `12` Unity Units／秒 |
| Zoom Minimum / Maximum | Resolved | `4`／`24` |
| Initial Zoom | Resolved | `10` |
| Zoom Speed | Resolved | `8` Orthographic Size Units／秒相当 |
| Paused / Normal / Fast | Resolved | `0x`／`1x`／`4x` |
| Initial Time State / Multiplier | Resolved | `Normal`／`1x` |
| Start Cell / Destination Cell | Resolved | 初期状態は未指定。別状態として保持し、同一セル指定を許可 |
| Input Bindings | Resolved | §9の`Phase01` Action Map |
| Verification Display | Resolved | Scene View表示、およびGame View左上の`F1`切替表示 |

- 非有限値、ゼロ以下のCell Size、無効なGrid Width／Height、Zoom Min/Maxの逆転を有効な設定として受理しない。
- 有限なInitial Zoomが`4`未満なら`4`へ、`24`を超えるなら`24`へ制限し、`4`〜`24`ならその値を使用する。非有限値は拒否する。
- Camera Move Speedは`0`以上の有限値を受理し、負または非有限な値を拒否する。Phase 1の正式値は`12` Unity Units／秒とする。
- Fast Multiplierは`1`より大きい有限値を受理し、`1`以下または非有限な値を拒否する。Phase 1の正式値は`4x`とする。
- Start CellとDestination Cellは個別の未指定状態または有効セル座標を保持し、無効指定では既存の有効値を上書きしない。
- 制限または拒否の結果をログまたはVerification Displayで確認可能にする。

## 9. Input Mapping

Unity Input System `1.19.0`と既存の`Assets/InputSystem_Actions.inputactions`を使用し、新規`.inputactions`ファイルまたは新しい入力Packageは追加しない。既存の`Player`および`UI` Mapへ混在させず、次の`Phase01` Action Mapを追加する。

| Action | Action Type | Control Type | Binding |
|---|---|---|---|
| `CameraMove` | Value | Vector2 | `WASD`およびArrow Keys |
| `CameraZoom` | Value | Axis | Mouse Scroll Y |
| `Pause` | Button | — | `Space` |
| `NormalSpeed` | Button | — | Keyboard `1` |
| `FastSpeed` | Button | — | Keyboard `2` |
| `ToggleVerificationDisplay` | Button | — | `F1` |
| `SetStartCell` | Button | — | Mouse Left Button |
| `SetDestinationCell` | Button | — | Mouse Right Button |

中ボタンドラッグ、エッジスクロール、カメラ回転、ゲームパッド、タッチ入力はPhase 1へ追加しない。新旧Input Systemの併用、新しい入力スタック、新しいPackageも追加しない。

## 10. Runtime Flow

実装後に必要となる実行上の流れを、Unityライフサイクルの具体的選択へ踏み込みすぎない範囲で示す。

1. Phase 1設定を読み取る。
2. Grid、Camera、Timeの設定値を検証する。
3. 無効設定がある場合は対象設定を拒否し、理由を表示して無効な実行状態へ移行しない。
4. Gridを初期化する。
5. Cameraを固定俯瞰の前提で初期化する。
6. Timeを`Normal`、`1x`で初期化する。
7. 検証表示を初期化する。
8. 各フレームで入力を受け取り、simulation-scaled delta timeを使わず、フレームレートに依存しない形でカメラ移動とズームを処理し、時間状態へ反映する。
9. 時間状態に従って`Simulation Elapsed Time`を更新する。
10. `SetStartCell`または`SetDestinationCell`入力がある場合、クリック位置をWorld to Cell変換し、有効セルの場合だけ対応する指定を更新する。
11. 無効セルまたは範囲外のセル指定では既存の有効指定を維持し、無効結果を記録する。
12. Grid情報、座標変換結果、Start Cell、Destination Cell、時間状態、累積値、エラー情報を検証表示へ反映する。
13. その他の無効な実行時入力は無視し、有効な直前状態を維持して理由を観測可能にする。

## 11. Error and Edge-Case Requirements

- Grid WidthまたはHeightが無効な場合、設定を拒否する。
- Cell Sizeが非正値または非有限の場合、設定を拒否する。
- Grid Originを含むGrid設定値が`NaN`、`Positive Infinity`、`Negative Infinity`の場合、設定を拒否する。
- World to Cellへ渡すWorld座標が非有限の場合、入力を拒否する。
- Zoom Minimum、Maximum、Initial Zoomが非有限の場合、設定を拒否する。
- Zoom入力値が非有限の場合、入力を無視する。
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
- 時間状態の切替で累積値をリセット、逆行、飛躍させない。

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

### EditMode：Camera / Zoom設定検証

- Zoom Minimum／Maximum／Initial Zoomの検証
- Min/Max逆転の拒否
- 非有限Zoom設定と入力の拒否
- 有限な範囲外Initial Zoomの`4`または`24`への制限
- Zoomが承認済み範囲を超えない
- Camera Move Speed `12`の受理
- Camera Move Speed `0`の受理
- 負または非有限なCamera Move Speedの拒否と有効値の維持

### EditMode：Simulation Time

- `Paused`、`Normal`、`Fast`の状態遷移
- 初期状態が`Normal`、初期倍率が`1x`
- 未定義のTime状態の拒否と有効状態の維持
- Fast Multiplierが`1`以下または非有限の場合の拒否と有効状態の維持
- Phase 1のFast Multiplier `4x`の受理
- `Paused`中にElapsed Timeが増加しない
- `Normal`中に`1x`で増加する
- `Fast`中に指定倍率で増加する
- 状態変更後も累積値の連続性が維持される
- 複数回の切替でリセット、逆行、飛躍がない

### PlayMode：Camera入力とRuntime統合（必要な場合のみ）

EditModeで確認不能な場合に限り、次をPlayModeで検証する。

- `Phase01` Input ActionsによってCameraが移動する
- ズーム入力がRuntimeで適用される
- `SetStartCell`と`SetDestinationCell`入力が別の指定を更新する
- 自由回転が発生しない
- Phase 1ではCamera移動境界が適用されない
- `Paused`中もCamera移動とズームが機能する
- `Paused`、`Normal`、`Fast`でCamera移動速度が変化しない

### Human Verification中心

- セル境界、セル中心、有効範囲、指定セル、Start Cell、Destination Cell、変換結果の表示
- 同一セルをStart CellとDestination Cellへ指定した場合の両指定の識別
- Game View左上でのGrid Configuration、Valid Cell Range、検証状態、カメラ位置、Orthographic Size、時間状態、倍率、Simulation Elapsed Time、World座標、変換されたセル座標、Start Cell、Destination Cell、未指定状態、最後の無効指定結果、その他の検証結果またはエラーの表示
- `F1`によるGame View検証表示の切替
- 無効入力または設定の拒否理由の表示
- Scene全体の固定俯瞰表示と操作感

## 14. Human Verification Procedure

1. Unity Editorで`Assets/IYASAKA/Scenes/Phase01Foundation.unity`を開く。
   - 期待結果: Phase 2以降の要素がなく、Consoleに未処理例外がない。
2. Play開始直後のグリッド表示を確認する。
   - 期待結果: 原点`(0, 0)`、`64 x 64`、セルサイズ`1` Unity Unitのグリッドについて、外周、セル境界、セル中心、有効範囲をScene Viewで区別できる。初期Time状態は`Normal`、初期倍率は`1x`と表示される。
3. 最小境界、セル内部、セル境界直前、最大外周境界を指定する。
   - 期待結果: 指定座標とWorld to Cell結果が表示され、半開区間規則どおりに判定される。
4. Grid Configuration表示を確認する。
   - 期待結果: Width `64`、Height `64`、Cell Size `1`、Origin `(0, 0)`、Valid Cell Range、Grid Configurationの有効状態を確認できる。
5. 有効セルをマウス左クリックし、別の有効セルをマウス右クリックする。
   - 期待結果: 左クリック位置がStart Cell、右クリック位置がDestination Cellとして個別に更新され、Scene ViewとGame View左上で区別できる。
6. 同一の有効セルをマウス左クリックと右クリックで指定する。
   - 期待結果: 同一セルについてStart CellとDestination Cellの両方が指定済みであると確認できる。
7. Start CellとDestination Cellを有効に指定した後、無効セルまたはグリッド範囲外を左右それぞれでクリックする。
   - 期待結果: 既存の有効指定が維持され、最後の無効指定結果をConsoleまたはVerification Displayで確認できる。経路探索、住民移動、経路可否判定は行われない。
8. カメラを上下左右へ移動する。
   - 期待結果: `WASD`および矢印キーで`12` Unity Units／秒のフレームレート非依存移動を行い、2D固定俯瞰と固定回転を維持し、Phase 1では移動境界に制限されない。
9. ズーム入力を最小・最大まで行う。
   - 期待結果: Mouse Scroll YでOrthographic Sizeが初期値`10`から`4`〜`24`の範囲内を`8` Units／秒相当でフレームレート非依存に変化し、範囲外へ移行しない。
10. `Paused`へ切り替え、Elapsed TimeとCamera操作を観察する。
    - 期待結果: `Space`で倍率`0x`となり、累積値が増加しない。Camera移動とズームは機能し、移動速度はNormalおよびFastと同じである。
11. `Normal`へ切り替え、Cameraを移動する。
   - 期待結果: Keyboard `1`で倍率`1x`となり、累積値が通常倍率で増加する。Camera移動速度はPausedおよびFastと同じである。
12. `Fast`へ切り替え、Cameraを移動する。
   - 期待結果: Keyboard `2`で倍率`4x`となり、累積値が指定倍率で増加する。Camera移動速度はPausedおよびNormalと同じである。
13. 時間状態を複数回切り替える。
   - 期待結果: 累積値がリセット、逆行、意図しない飛躍をしない。
14. 有限なInitial Zoomとして`4`未満、`4`〜`24`、`24`超の値を与える。
    - 期待結果: `4`未満は`4`、`24`超は`24`へ制限され、範囲内はその値を使用する。結果をConsoleまたはVerification Displayで確認できる。
15. 検証可能な方法で非有限座標、無効Grid設定、非有限Zoom設定／入力、負または非有限なCamera Move Speed、未定義Time状態、`1`以下または非有限なFast Multiplierを与える。
    - 期待結果: 要件に従って設定拒否または入力無視となり、直前の有効状態を維持し、ConsoleまたはVerification Displayで理由を確認できる。Camera Move Speed `0`は有効な停止値として受理される。
16. `F1`でGame View左上の検証表示を切り替える。
    - 期待結果: Grid Configuration、Valid Cell Range、カメラ位置、Orthographic Size、時間状態、倍率、Elapsed Time、World座標、変換されたセル座標、Start Cell、Destination Cell、未指定状態、最後の無効指定結果、その他の検証結果またはエラーの表示と非表示を切り替えられる。
17. Play終了後にConsoleを確認する。
    - 期待結果: 期待された検証ログ以外のエラー、例外、無限ログがない。

## 15. Completion Evidence

実装担当は、少なくとも次を報告する。

- 変更ファイル一覧
- 新規作成ファイル一覧
- 実行した自動テストと結果
- Unity Consoleのエラー／警告状況
- 使用した確認用Scene
- Inspectorまたは設定ファイル上の実測設定値
- 人間確認手順のチェック結果
- Initial Zoomの制限・拒否、Camera Move Speed、Time設定の検証結果
- Paused中のCamera移動・ズームと、全Time状態でCamera速度が変化しないことの確認結果
- グリッド表示、Grid Configuration、座標変換、Start Cell、Destination Cell、時間状態、Elapsed Timeを確認できるスクリーンショット
- Start CellとDestination Cellの指定結果、および無効指定後も既存指定が維持されたことの記録
- 未解決事項、既知の制限、仕様との相違
- 実装コミットSHA

## 16. Acceptance Mapping

System SpecのAcceptance Criteriaを変更せず、実装対象と証拠へ接続する。承認時にはSystem Specの項目名・番号と照合する。

| System Spec Acceptance対象 | 実装対象 | 自動テスト候補 | 人間確認 | 完了証拠 |
|---|---|---|---|---|
| 正方形グリッドと有効範囲 | Grid Foundation | 設定値、有効範囲、境界 | 手順2〜4 | テスト結果、スクリーンショット |
| Cell／World座標変換 | Grid Foundation | 既知値、往復、範囲外 | 手順3 | テスト結果、表示結果 |
| 半開区間と最大外周境界 | Grid Foundation | 境界値テスト | 手順3 | 境界テスト結果 |
| 非有限Grid／World入力の拒否 | Grid Foundation | 非有限値テスト | 手順15 | ログ、状態保持結果 |
| Grid Configurationの表示確認 | Verification Display | 表示モデルのテスト候補 | 手順4 | 設定表示のスクリーンショット |
| Start CellとDestination Cellの指定 | Verification Display / Grid Foundation | 有効指定、個別状態、同一セル指定 | 手順5、6 | テスト結果、両指定のスクリーンショット |
| 無効セル指定で既存指定を維持 | Verification Display / Grid Foundation | 無効・範囲外指定後の状態維持 | 手順7 | テスト、ログ、状態維持結果 |
| Phase 2へのStart／Destination指定結果 | Verification Display / Grid Foundation | 指定状態の参照確認 | 手順5〜7 | Start／Destination指定記録 |
| Phase 2以降の機能が未実装 | File Plan / Explicit Out of Scope / Phase 2 Protection / 実装変更範囲の制約 | 変更ファイル一覧、新規Script／Scene／Input Action／asmdef、住民・経路探索・道路効果関連、Phase 1外フォルダ／将来用ファイルの確認 | 手順1、7、およびScene Hierarchy／Project構成確認 | 変更ファイル一覧、Scene構成確認結果、Project構成確認結果、Phase 2以降の機能未実装チェック結果、Scope外実装なしの明示報告 |
| 固定俯瞰Camera移動 | Camera Controller | 必要に応じたPlayMode候補 | 手順8 | スクリーンショット、確認記録 |
| Zoom範囲と無効入力処理 | Camera Controller | 設定、制限、拒否、入力検証 | 手順9、14、15 | テスト、ログ、確認記録 |
| Camera Move Speedの検証 | Camera Controller | `12`、`0`、負値、非有限値 | 手順8、15 | テスト、ログ、状態維持結果 |
| Phase 1ではCamera境界なし | Camera Controller | 必要に応じたPlayMode候補 | 手順8 | 確認記録 |
| Paused中のCamera移動とZoom | Camera Controller | 必要に応じたPlayMode候補 | 手順10 | 確認記録 |
| Simulation Multiplierから独立したCamera速度 | Camera Controller | Time状態別のCamera統合テスト候補 | 手順10〜12 | Time状態別確認記録 |
| Initial Time StateがNormal | Simulation Time Controller | 初期状態と`1x`倍率 | 手順2 | テスト、初期表示結果 |
| Paused／Normal／Fast | Simulation Time Controller | 状態遷移テスト | 手順10〜13 | テスト、表示結果 |
| 不正Time状態とFast Multiplierの拒否 | Simulation Time Controller | 未定義状態、`1`以下、非有限値 | 手順15 | テスト、ログ、状態維持結果 |
| Simulation Elapsed Time | Simulation Time Controller | 停止、倍率、連続性 | 手順10〜13 | テスト、表示結果 |
| Phase 1検証表示 | Verification Display | 表示モデルのテスト候補 | 手順2〜7、10〜16 | スクリーンショット |
| 無効状態・クラッシュ防止 | 各対象コンポーネント | 無効入力テスト | 手順7、14、15、17 | ログ、テスト、Console結果 |

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
| OD-05 | Scene ViewとGame View左上へGrid Configuration、Start／Destinationを含むPhase 1検証表示を配置し、Game View表示を`F1`で切替 | Resolved |
| OD-06 | §6のファイル名とクラス名を採用 | Resolved |

### 18.2 Remaining Open Decisions

なし。

実装計画で責務を変えない軽微な表記確認が必要になった場合も、新しい機能、System Boundary、Acceptance Criteriaの変更には使用しない。

## 19. Approval Readiness

- Blocking Open Decisions: なし
- Handoffレビュー: 可能
- Handoff承認: 可能
- Unity実装開始: 不可

承認前に必要だった具体値および実装対象の判断はすべて解決済みであり、新たなBlocking Open Decisionは確認されていない。本書はレビューおよび承認へ進められるが、現在は`Draft`、`Approved: Pending`、`Implementation Use: Prohibited`、`Unity Implementation: Prohibited`であり、Unity実装を開始できない。

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

- 本書が`Draft`かつ`Approved: Pending`である間、Implementation Useは`Prohibited`である。
- 本書の承認前にUnity実装を開始してはならない。
- 承認済みSystem Specは本Handoff作成の入力として使用できるが、それ自体はUnity実装開始許可ではない。
- 本Handoffが承認された後も、ユーザーによる明示的なUnity実装許可が別途必要である。
- mainへのマージだけではUnity実装開始を許可しない。
- 承認後、Phase 1実装専用のCodex向け実装プロンプトを別途作成・承認する。
- Blocking Open Decisionが新たに確認された場合、本HandoffをApprovedへ変更しない。

## 22. Phase 2 Protection

- Phase 2以降の住民、移動、経路探索を先行実装しない。
- Start CellとDestination Cellの指定はPhase 2準備用の状態保持と表示に限定し、経路探索、住民移動、経路可否判定を行わない。
- Phase 1のGridを完成版向け汎用フレームワークへ拡張しない。
- Cameraへ追従、回転、最終マップ境界を追加しない。
- Timeへ住民、生産、建築などの更新責務を追加しない。
- 検証表示を完成版UIへ発展させない。
- 将来利用を理由に抽象クラス、汎用イベント基盤、サービスロケータ、DI基盤を追加しない。
- 後続Phaseの要求が確定した時点で、必要な変更をそのPhaseのSystem SpecとHandoffで判断する。
