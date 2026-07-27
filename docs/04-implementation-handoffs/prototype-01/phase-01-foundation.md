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
Last Updated: 2026-07-28

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
- 上記機能に必要な入力・設定値の検証とエラー表示
- 上記機能を検証するための最小限のテスト基盤

Phase 1検証用表示では、少なくとも次を確認可能にする。

- セル境界
- セル中心
- 有効グリッド範囲
- 選択または指定したセル座標
- World座標から変換されたセル座標
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

- Main Scene候補: `Assets/Scenes/SampleScene.unity`
- Template Scene: `Assets/Settings/Scenes/URP2DSceneTemplate.unity`

既存Input Actionsは`Assets/InputSystem_Actions.inputactions`にあり、`Player`および`UI` Action Mapを含む。Phase 1専用Action Mapは存在しない。

Git管理対象は`Assets/`、`Packages/`、`ProjectSettings/`および`.meta`である。`Library/`、`Temp/`、`Obj/`、`Logs/`、`UserSettings/`、生成された`.csproj`および`.slnx`はGit管理対象外である。

Unity実装リポジトリにはAI実装ルールとして`AGENTS.md`が存在する。

### 5.2 提案として扱う事項

以下はRepository Findingsではなく、本Handoff Draftの提案またはOpen Decisionである。

- Phase 1のファイル配置とファイル名
- Phase 1確認用Sceneの利用方針
- namespace
- RuntimeおよびTest用`.asmdef`の導入可否
- TestのEditMode／PlayMode配置
- 既存Input Actions内へのPhase 1 Action Map追加可否
- 入力割り当て
- 検証表示の配置
- 具体的な設定値

これらは承認済み事項として扱わず、§18のOpen Decisionsで承認可否を判断する。

## 6. Proposed File Plan

以下はUnity実装リポジトリの現行構成に合わせたDraft推奨案であり、承認済みの確定パスではない。Implementation Handoff承認時に確定する。

| 種別 | 推奨パス／ファイル名 | 責務 | 依存・注意 |
|---|---|---|---|
| 新規候補 | `Assets/IYASAKA/Scripts/Grid/GridFoundation.cs` | グリッド設定、有効範囲、座標変換 | 住民、経路探索、建築の責務を持たせない |
| 新規候補 | `Assets/IYASAKA/Scripts/Camera/Phase01CameraController.cs` | 固定俯瞰カメラの移動とズーム | 自由回転、最終境界、追従機能を追加しない |
| 新規候補 | `Assets/IYASAKA/Scripts/Simulation/SimulationTimeController.cs` | 時間状態、倍率、累積Simulation Elapsed Time | 生産、住民、建築の更新を持たせない |
| 新規候補 | `Assets/IYASAKA/Scripts/Debug/Phase01VerificationDisplay.cs` | Phase 1検証情報の表示 | 完成版UIとして作らない |
| 新規候補 | `Assets/IYASAKA/Scripts/Phase01Bootstrap.cs` | Phase 1構成要素の最小限の接続 | 汎用DI基盤やサービスロケータを導入しない |
| 新規候補 | `Assets/IYASAKA/Scenes/Phase01Foundation.unity` | Phase 1の人間確認用Scene | Template Sceneを直接変更せず、後続Phaseの要素を置かない |
| 新規候補 | `Assets/IYASAKA/Tests/EditMode/GridFoundationTests.cs` | 座標変換、境界、無効入力 | Unity Test Framework `1.6.0`を使用する候補 |
| 新規候補 | `Assets/IYASAKA/Tests/EditMode/SimulationTimeControllerTests.cs` | 時間状態と累積値 | Unity Test Framework `1.6.0`を使用する候補 |
| 条件付き候補 | `Assets/IYASAKA/Tests/PlayMode/Phase01CameraControllerTests.cs` | Camera入力とRuntime統合 | PlayMode自動化が過剰でない範囲に限定する |

次の最小フォルダだけを作成候補とする。

- `Assets/IYASAKA/Scripts/Grid/`
- `Assets/IYASAKA/Scripts/Camera/`
- `Assets/IYASAKA/Scripts/Simulation/`
- `Assets/IYASAKA/Scripts/Debug/`
- `Assets/IYASAKA/Scenes/`
- `Assets/IYASAKA/Tests/EditMode/`
- `Assets/IYASAKA/Tests/PlayMode/`（PlayModeテストを採用する場合のみ）

Phase 2向けフォルダ、将来機能用の空フォルダ、Framework層、Domain／Application／Infrastructure等のレイヤーは作成しない。

### 6.1 asmdef Draft方針

現時点のUnity実装リポジトリには`.asmdef`が存在しない。

Draft推奨は、テストとRuntimeコードの参照境界を明確にするため、必要最小限の構成だけを導入することである。

- Runtime候補: `Assets/IYASAKA/Scripts/IYASAKA.Runtime.asmdef`
- EditMode Test候補: `Assets/IYASAKA/Tests/EditMode/IYASAKA.Tests.EditMode.asmdef`
- PlayMode Test候補: `Assets/IYASAKA/Tests/PlayMode/IYASAKA.Tests.PlayMode.asmdef`（PlayModeテストを採用する場合のみ）

この方針はDraft推奨であり、承認済み事項ではない。Phase 1の規模に対して不要と判断した場合、Runtime asmdefを作らず、テストに必要な最小構成だけを選択できる。将来機能向けのAssembly分割は行わない。

### 6.2 Scene Draft方針

Draft推奨は、`Assets/Settings/Scenes/URP2DSceneTemplate.unity`を直接変更せず、Unity Editor上でPhase 1確認用の新規Scene `Assets/IYASAKA/Scenes/Phase01Foundation.unity`を作成することである。

`Assets/Scenes/SampleScene.unity`を直接Phase 1確認用Sceneとして使用する案も残るが、既存Sceneを破壊的に扱わないことを優先する。Human Verificationでは、承認されたScene方針に従い、Phase 1確認用Sceneを一つだけ使用する。

## 7. Component and Class Responsibilities

実装時は責務を明確にするが、Phase 1のために必要以上に分割しない。

### 7.1 Grid Foundation

- グリッド幅、高さ、セルサイズ、原点を受け取る。
- 有効なグリッド範囲を定義する。
- Cell to WorldおよびWorld to Cellを提供する。
- 半開区間`[min, max)`の境界規則を守る。
- 無効設定または非有限座標を拒否し、観測可能な情報を残す。

### 7.2 Camera Controller

- 2D固定俯瞰の前提を維持する。
- 平面上のカメラ移動を処理する。
- ズームを設定範囲内で処理する。
- Phase 1では移動範囲を制限しない。
- 自由回転を実装しない。

### 7.3 Simulation Time Controller

- `Paused`、`Normal`、`Fast`を切り替える。
- 現在状態と適用倍率を公開する。
- `Simulation Elapsed Time`を累積する。
- 状態変更時も累積値の連続性を維持する。

Elapsed Timeだけのための独立コンポーネントは必須とせず、単純性が保てる場合はTime Controllerへ含める。

### 7.4 Verification Display Presenter

- グリッド、変換結果、時間状態、累積値、検証用エラー情報を観測可能にする。
- 完成版UIや汎用デバッグフレームワークの責務を持たない。

### 7.5 Bootstrap / Scene Composition

- Phase 1に必要な構成要素をScene内で接続する。
- 初期設定を検証し、無効な構成で実行状態へ移行しない。
- 後続Phase用の登録・拡張基盤を先行して作らない。

### 7.6 Validation / Error Reporting

- 各コンポーネントが自身の入力と設定を検証する。
- Phase 1専用の中央エラー管理基盤は新設しない。
- 拒否理由をConsoleログまたは検証表示で確認可能にする。

## 8. Data and Configuration

次の値は調整可能な設定として扱う。承認済み値とDraft提案を混同しない。

| 項目 | 状態 | Draft推奨／判断方針 |
|---|---|---|
| Grid Width / Height | Open Decision | Phase 1確認用の小規模固定値。Draft推奨は`32 x 32` |
| Cell Size | Open Decision | Draft推奨は`1` World Unit |
| Grid Origin | Open Decision | Draft推奨は`(0, 0)` |
| Camera Move Speed | Open Decision | Human Verificationで確認しやすいPhase 1仮値をHandoffレビューで承認する |
| Zoom Minimum / Maximum | Open Decision | 2D固定俯瞰を維持できるPhase 1仮値をHandoffレビューで承認する |
| Initial Zoom | Open Decision | 承認済みMin/Max内で定める |
| Zoom Speed | Open Decision | 承認済みInput Bindingに合わせた仮値を定める |
| Normal Multiplier | System Spec | `1x` |
| Fast Multiplier | Open Decision | Draft推奨は`4x`。正式値ではない |
| Input Bindings | Open Decision | §9およびOD-02で承認する |
| Verification Display | Open Decision | Phase 1では常時表示を基本候補とし、切替は必須にしない |

非有限値、ゼロ以下のセルサイズ、無効なGridサイズ、Zoom Min/Maxの逆転、範囲外Initial Zoomを有効な設定として受理しない。

## 9. Input Mapping

入力対象は次に限定する。

- カメラ移動
- ズーム
- `Paused`
- `Normal`
- `Fast`
- 任意の検証表示切替（導入する場合のみ）

Unity Input System `1.19.0`は導入済みであり、新しい入力Packageは不要である。既存の`Assets/InputSystem_Actions.inputactions`を使用し、新規`.inputactions`ファイルは作成しない方針をDraft推奨とする。

既存Action Assetには`Player`および`UI` Action Mapが存在する。Phase 1用入力を既存の`Player` Mapへ混在させず、同じAction AssetへPhase 1専用Action Mapを追加する案をDraft推奨とする。

Draft候補は次のとおりだが、承認済み入力ではない。

- Action Map: `Phase01`
- Camera movement: `WASD`および矢印キー
- Zoom: マウスホイール
- Pause: `Space`
- Normal: `1`
- Fast: `2`
- Debug display toggle: 必要な場合のみ`F1`

Action Map名、Action名、Bindingは§18のBlocking Open Decisionとして承認する。新旧Input Systemを併用したり、新しい入力スタックやPackageを追加したりしない。

## 10. Runtime Flow

実装後に必要となる実行上の流れを、Unityライフサイクルの具体的選択へ踏み込みすぎない範囲で示す。

1. Phase 1設定を読み取る。
2. Grid、Camera、Timeの設定値を検証する。
3. 無効設定がある場合は対象設定を拒否し、理由を表示して無効な実行状態へ移行しない。
4. Gridを初期化する。
5. Cameraを固定俯瞰の前提で初期化する。
6. Timeを初期状態で初期化する。
7. 検証表示を初期化する。
8. 各フレームで入力を受け取り、カメラと時間状態へ反映する。
9. 時間状態に従って`Simulation Elapsed Time`を更新する。
10. Grid情報、座標変換結果、時間状態、累積値、エラー情報を検証表示へ反映する。
11. 無効な実行時入力は無視し、有効な直前状態を維持して理由を観測可能にする。

## 11. Error and Edge-Case Requirements

- Grid WidthまたはHeightが無効な場合、設定を拒否する。
- Cell Sizeが非正値または非有限の場合、設定を拒否する。
- Grid Originを含むGrid設定値が`NaN`、`Positive Infinity`、`Negative Infinity`の場合、設定を拒否する。
- World to Cellへ渡すWorld座標が非有限の場合、入力を拒否する。
- Zoom Minimum、Maximum、Initial Zoomが非有限の場合、設定を拒否する。
- Zoom入力値が非有限の場合、入力を無視する。
- Zoom MinimumがMaximumを上回る場合、設定を拒否する。
- Initial Zoomが有効範囲外の場合、設定を拒否する。
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
2. §18のBlocking Open Decisionsを解決し、Handoffを承認する。
3. 承認されたファイル、Scene、入力、テスト／`.asmdef`構成を確定する。
4. Grid設定と座標変換を実装する。
5. Gridの自動テスト候補を実装・実行する。
6. Simulation TimeとElapsed Timeを実装する。
7. Timeの自動テスト候補を実装・実行する。
8. 固定俯瞰Cameraの移動とズームを実装する。
9. Phase 1検証表示を実装する。
10. Phase 1確認用Sceneへ最小限に接続する。
11. 自動テスト、Console確認、人間によるUnity動作確認を行う。
12. Completion Evidenceを収集し、Phase 1完了判定へ渡す。

## 13. Automated Test Candidates

導入済みのUnity Test Framework `1.6.0`を使用する。テストコードは本Handoff更新では作成しない。

### EditMode候補：Grid / Coordinate Conversion

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

### EditMode候補：Camera / Zoom設定検証

- Zoom Minimum／Maximum／Initial Zoomの検証
- Min/Max逆転の拒否
- 非有限Zoom設定と入力の拒否
- Zoomが承認済み範囲を超えない

### EditMode候補：Simulation Time

- `Paused`、`Normal`、`Fast`の状態遷移
- `Paused`中にElapsed Timeが増加しない
- `Normal`中に`1x`で増加する
- `Fast`中に指定倍率で増加する
- 状態変更後も累積値の連続性が維持される
- 複数回の切替でリセット、逆行、飛躍がない

### PlayMode候補：Camera入力とRuntime統合

- 承認済みInput ActionsによってCameraが移動する
- ズーム入力がRuntimeで適用される
- 自由回転が発生しない
- Phase 1ではCamera移動境界が適用されない

### Human Verification中心

- セル境界、セル中心、有効範囲、指定セル、変換結果の表示
- 現在の時間状態とSimulation Elapsed Timeの表示
- 無効入力または設定の拒否理由の表示
- Scene全体の固定俯瞰表示と操作感

## 14. Human Verification Procedure

1. Unity EditorでPhase 1確認用Sceneを開く。
   - 期待結果: Phase 2以降の要素がなく、Consoleに未処理例外がない。
2. Play開始直後のグリッド表示を確認する。
   - 期待結果: セル境界、セル中心、有効範囲を区別できる。
3. 最小境界、セル内部、セル境界直前、最大外周境界を指定する。
   - 期待結果: 指定座標とWorld to Cell結果が表示され、半開区間規則どおりに判定される。
4. カメラを上下左右へ移動する。
   - 期待結果: 2D固定俯瞰を維持し、自由回転せず、Phase 1では移動境界に制限されない。
5. ズーム入力を最小・最大まで行う。
   - 期待結果: 範囲内で連続的に変化し、範囲外へ移行しない。
6. `Paused`へ切り替え、Elapsed Timeを観察する。
   - 期待結果: 累積値が増加しない。
7. `Normal`へ切り替える。
   - 期待結果: 累積値が通常倍率で増加する。
8. `Fast`へ切り替える。
   - 期待結果: 累積値が承認済み倍率で増加する。
9. 時間状態を複数回切り替える。
   - 期待結果: 累積値がリセット、逆行、意図しない飛躍をしない。
10. 検証可能な方法で非有限座標、無効Grid設定、無効Zoom設定／入力を与える。
    - 期待結果: 設定拒否または入力無視となり、直前の有効状態を維持し、Consoleまたは表示で理由を確認できる。
11. Play終了後にConsoleを確認する。
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
- グリッド表示、座標変換、時間状態、Elapsed Timeを確認できるスクリーンショット
- 未解決事項、既知の制限、仕様との相違
- 実装コミットSHA

## 16. Acceptance Mapping

System SpecのAcceptance Criteriaを変更せず、実装対象と証拠へ接続する。承認時にはSystem Specの項目名・番号と照合する。

| System Spec Acceptance対象 | 実装対象 | 自動テスト候補 | 人間確認 | 完了証拠 |
|---|---|---|---|---|
| 正方形グリッドと有効範囲 | Grid Foundation | 設定値、有効範囲、境界 | 手順2、3 | テスト結果、スクリーンショット |
| Cell／World座標変換 | Grid Foundation | 既知値、往復、範囲外 | 手順3 | テスト結果、表示結果 |
| 半開区間と最大外周境界 | Grid Foundation | 境界値テスト | 手順3 | 境界テスト結果 |
| 非有限Grid／World入力の拒否 | Grid Foundation | 非有限値テスト | 手順10 | ログ、状態保持結果 |
| 固定俯瞰Camera移動 | Camera Controller | 必要に応じたPlayMode候補 | 手順4 | スクリーンショット、確認記録 |
| Zoom範囲と無効入力処理 | Camera Controller | 設定・入力検証 | 手順5、10 | テスト、ログ、確認記録 |
| Phase 1ではCamera境界なし | Camera Controller | 必要に応じたPlayMode候補 | 手順4 | 確認記録 |
| Paused／Normal／Fast | Simulation Time Controller | 状態遷移テスト | 手順6〜9 | テスト、表示結果 |
| Simulation Elapsed Time | Simulation Time Controller | 停止、倍率、連続性 | 手順6〜9 | テスト、表示結果 |
| Phase 1検証表示 | Verification Display | 表示モデルのテスト候補 | 手順2、3、6〜10 | スクリーンショット |
| 無効状態・クラッシュ防止 | 各対象コンポーネント | 無効入力テスト | 手順10、11 | ログ、テスト、Console結果 |

## 17. Stop Conditions

次のいずれかに該当した場合は、実装またはHandoff承認を停止して報告する。

- 承認済みGDD、PDD、System Spec、Roadmapの間に矛盾がある。
- 実装に必須の値または判断が未解決である。
- Unity実装リポジトリの基準HEADが変わり、Repository FindingsまたはFile Planと競合する。
- 承認済み入力方式、Scene方針、asmdef方針、テスト配置と実際のリポジトリ状態が一致しない。
- 既存コード、Scene、Prefab、命名規則と本提案が競合する。
- Phase 1 System Boundaryを超える必要が生じる。
- 新しいUnity Packageが必要になる。
- 既存SceneまたはPrefabを破損する可能性がある。
- 承認済みSystem Specの変更が必要になる。
- Acceptance Criteriaを検証できる環境が不足している。
- ユーザーからUnity実装開始の明示許可がない。

## 18. Open Decisions

### 18.1 Blocking Open Decisions

#### OD-01 Phase 1設定値

- Decision: Grid Width／Height、Cell Size、Grid Origin、Camera Move Speed、Zoom Minimum／Maximum／Initial／Speed、Fast Multiplierを承認する。
- Why: Acceptance CriteriaとHuman Verificationを同じ条件で再現するため。
- Recommended option: §8のDraft推奨を出発点とし、Phase 1検証用の仮値として承認する。
- Alternatives: レビュー時に別の小規模な検証値を指定する。
- Impact: Inspector設定、テスト期待値、Human Verification条件。完成版仕様として固定しない。
- Classification: Blocking before Handoff approval

#### OD-02 Input ActionsとBinding

- Decision: 既存`Assets/InputSystem_Actions.inputactions`へPhase 1専用Action Mapを追加するか、およびAction名とBindingを承認する。
- Why: Camera、Zoom、時間状態、検証表示を同じ入力条件で実装・確認するため。
- Recommended option: 既存Action Assetへ`Phase01` Mapを追加し、§9のDraft候補を使用する。新規`.inputactions`は作成しない。
- Alternatives: 既存`Player`／`UI` Mapを再利用する。ただしPhase 1固有入力との責務混在を避ける必要がある。
- Impact: Input Actions Asset、人間確認手順、PlayModeテスト。
- Classification: Blocking before Handoff approval

#### OD-03 Scene利用方針

- Decision: `SampleScene.unity`を利用するか、Phase 1確認用Sceneを新規作成するかを承認する。
- Why: Human Verification対象と変更ファイルを一意にするため。
- Recommended option: Template Sceneを直接変更せず、`Assets/IYASAKA/Scenes/Phase01Foundation.unity`を新規作成する。
- Alternatives: `Assets/Scenes/SampleScene.unity`をPhase 1確認用として使用する。
- Impact: Scene変更対象、Completion Evidence、破壊的変更のリスク。
- Classification: Blocking before Handoff approval

#### OD-04 asmdef・Test配置・namespace

- Decision: 最小asmdef構成、EditMode／PlayModeテスト配置、namespaceを承認する。
- Why: RuntimeとUnity Test Frameworkの参照関係を実装前に固定するため。
- Recommended option: §6.1の最小asmdef候補を採用し、namespaceは`IYASAKA.Prototype01`配下で責務別に分ける。
- Alternatives: Runtime asmdefを作らず、テストに必要なasmdefだけを作成する。既存Assembly-CSharpへ残す場合も理由を記録する。
- Impact: コンパイル境界、テスト参照、ファイル配置。将来向けの過剰分割は行わない。
- Classification: Blocking before Handoff approval

#### OD-05 Verification Display配置

- Decision: Phase 1検証表示をScene内のどの方式で配置し、常時表示または切替式のどちらにするかを承認する。
- Why: Acceptance Criteriaを人間が確実に観測できる状態を定義するため。
- Recommended option: Phase 1確認用Scene内へ専用Presenterを一つ配置し、初期状態では常時表示する。Toggleは必須にしない。
- Alternatives: `F1`で表示切替を行う。
- Impact: Scene構成、Input Binding、Human Verification。
- Classification: Blocking before Handoff approval

### 18.2 Non-Blocking Open Decisions

#### OD-06 ファイル名とクラス名の最終表記

- Decision: §6のDraft推奨名をそのまま採用するか、既存Unity命名に合わせて軽微調整する。
- Why: 現在C#スクリプトおよびnamespace規則が存在しないため。
- Recommended option: §6の名称を使用し、責務を変えない表記調整だけを許可する。
- Alternatives: Handoffレビューで同等の明確な名称を指定する。
- Impact: ファイル名とクラス名のみ。System BoundaryとAcceptance Criteriaは変更しない。
- Classification: Non-Blocking

## 19. Approval Readiness

- Blocking Open Decisions: あり（OD-01〜OD-05）
- Handoffレビュー: 可能
- Handoff承認: Blocking Open Decisions解決後に可能
- Unity実装開始: 不可

Repository Findingsに起因していた旧Blocking Open Decisionsは解消済みである。残るBlocking項目は、Handoff承認前に必要な具体値および実装対象の選択に限定される。

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
- Blocking Open Decisionが残る間、本HandoffをApprovedへ変更しない。

## 22. Phase 2 Protection

- Phase 2以降の住民、移動、経路探索を先行実装しない。
- Phase 1のGridを完成版向け汎用フレームワークへ拡張しない。
- Cameraへ追従、回転、最終マップ境界を追加しない。
- Timeへ住民、生産、建築などの更新責務を追加しない。
- 検証表示を完成版UIへ発展させない。
- 将来利用を理由に抽象クラス、汎用イベント基盤、サービスロケータ、DI基盤を追加しない。
- 後続Phaseの要求が確定した時点で、必要な変更をそのPhaseのSystem SpecとHandoffで判断する。
