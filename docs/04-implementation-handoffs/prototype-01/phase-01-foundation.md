# Prototype 01 Phase 1 Implementation Handoff

Status: Draft  
Version: 0.1  
Approved: Pending  
Source System Spec: [Prototype 01 Phase 1 System Spec v1.0](../../03-system-specs/prototype-01/phase-01-foundation.md)  
Source main HEAD: `8c33fa12049d8e2d79d5a46d294c31307d1b319d`  
Implementation Use: Prohibited  
Unity Implementation: Prohibited  
Last Updated: 2026-07-27

> この文書は実装指示のDraftである。承認されるまで実装判断に使用してはならない。承認後も、Unity実装の明示的な許可が別途与えられるまで実装を開始してはならない。

## 1. Purpose

本書は、承認済みのPrototype 01 Phase 1 System Specを、Codexまたは実装担当AIへ渡す具体的な作業指示へ変換するためのImplementation Handoff Draftである。

対象はPhase 1「基盤構築」に限定する。本書はゲーム仕様やSystem Specを追加・変更せず、実装対象、確認方法、成果物、停止条件を明確にする。

## 2. Source of Truth

実装時は、次の順序で承認済み文書を参照する。

1. [Prototype 01 Phase 1 System Spec v1.0](../../03-system-specs/prototype-01/phase-01-foundation.md)
2. [Prototype 01 PDD v1.0](../../02-prototypes/prototype-01/pdd.md)
3. [GDD v1.0](../../01-gdd/gdd.md)
4. [Prototype 01 Roadmap](../../02-prototypes/prototype-01/roadmap.md)
5. 本Implementation Handoff

本書は上位文書を上書きしない。文書間の矛盾、意味の分岐、必要情報の欠落を発見した場合は、推測で実装せず停止して報告する。

実装開始時には、承認済み文書のバージョンと対象コミットを再確認する。

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

基準main HEAD `8c33fa12049d8e2d79d5a46d294c31307d1b319d`を読み取り専用で確認した結果は次のとおり。

- 承認済みGDD、PDD、Phase 1 System Spec、Roadmapが存在する。
- `docs/03-system-specs/README.md`にPhase 1 System Specが登録されている。
- `Assets/`、`Packages/manifest.json`、`ProjectSettings/ProjectVersion.txt`を確認できなかった。
- Unityバージョンを確認できなかった。
- Render Pipelineを確認できなかった。
- Input Systemの導入状況を確認できなかった。
- Unity Test Frameworkの導入状況を確認できなかった。
- 既存スクリプト、`.asmdef`、テスト、Scene、Prefabを確認できなかった。
- 既存のC#命名規則およびnamespace規則を確認できなかった。
- Implementation Handoff用READMEまたはテンプレートを確認できなかった。

この確認結果は「存在しない」と断定するものではなく、対象HEADのGitHubリポジトリ内で確認できなかったことを示す。

### 5.2 提案として扱う事項

以下は既存構造から確認できた事実ではなく、本Handoff Draftで示す新規作成候補である。

- Unityプロジェクト内の配置先
- Scene名、スクリプト名、テスト名
- namespace
- `.asmdef`およびテスト用`.asmdef`の要否
- 入力割り当て
- 具体的な設定値

Unityプロジェクトの所在と既存規則を確認するまで、これらを確定しない。

## 6. Proposed File Plan

以下はすべて新規作成候補であり、承認済みの確定パスではない。Unityプロジェクトと既存規則の確認後に、Implementation Handoff承認時または実装開始前の明示判断で確定する。

| 種別 | 推奨パス／ファイル名 | 責務 | 依存・注意 |
|---|---|---|---|
| 新規候補 | `Assets/ProjectIYASAKA/Prototype01/Phase01/Runtime/Grid/GridFoundation.cs` | グリッド設定、有効範囲、座標変換 | 住民、経路探索、建築の責務を持たせない |
| 新規候補 | `Assets/ProjectIYASAKA/Prototype01/Phase01/Runtime/Camera/Phase01CameraController.cs` | 固定俯瞰カメラの移動とズーム | 自由回転、最終境界、追従機能を追加しない |
| 新規候補 | `Assets/ProjectIYASAKA/Prototype01/Phase01/Runtime/Time/SimulationTimeController.cs` | 時間状態、倍率、累積Simulation Elapsed Time | 生産、住民、建築の更新を持たせない |
| 新規候補 | `Assets/ProjectIYASAKA/Prototype01/Phase01/Runtime/Debug/Phase01VerificationDisplay.cs` | Phase 1検証情報の表示 | 完成版UIとして作らない |
| 新規候補 | `Assets/ProjectIYASAKA/Prototype01/Phase01/Runtime/Bootstrap/Phase01Bootstrap.cs` | Phase 1構成要素の最小限の接続 | 汎用DI基盤やサービスロケータを導入しない |
| 新規候補 | `Assets/ProjectIYASAKA/Scenes/Prototype01/Phase01Foundation.unity` | Phase 1の人間確認用Scene | 後続Phaseのオブジェクトを置かない |
| 新規候補 | `Assets/ProjectIYASAKA/Tests/EditMode/Prototype01/Phase01/GridFoundationTests.cs` | 座標変換、境界、無効入力の自動テスト候補 | Test Framework確認後に確定 |
| 新規候補 | `Assets/ProjectIYASAKA/Tests/EditMode/Prototype01/Phase01/SimulationTimeControllerTests.cs` | 時間状態と累積値の自動テスト候補 | Test Framework確認後に確定 |
| 条件付き候補 | Phase 1 Runtime/Test用`.asmdef` | コンパイル境界とテスト参照 | 既存方針がある場合は従い、必要性がなければ作らない |

既存Unityプロジェクトに同等のフォルダ、Scene、基盤コンポーネントがある場合は、新規作成せず既存規則を優先する。既存コードとの責務競合がある場合は停止条件に該当する。

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
| Camera Move Speed | Open Decision | Unityプロジェクト確認後に人間確認しやすい仮値を承認する |
| Zoom Minimum / Maximum | Open Decision | Unity Camera設定と投影方式確認後に承認する |
| Initial Zoom | Open Decision | 承認済みMin/Max内で定める |
| Zoom Speed | Open Decision | 入力方式確認後に仮値を承認する |
| Normal Multiplier | System Spec | `1x` |
| Fast Multiplier | Open Decision | Draft推奨は`4x`。正式値ではない |
| Input Bindings | Open Decision | 既存入力方式を確認後に決定する |
| Verification Display | Draft提案 | Phase 1では常時表示を基本候補とし、切替は必須にしない |

非有限値、ゼロ以下のセルサイズ、無効なGridサイズ、Zoom Min/Maxの逆転、範囲外Initial Zoomを有効な設定として受理しない。

## 9. Input Mapping

入力対象は次に限定する。

- カメラ移動
- ズーム
- `Paused`
- `Normal`
- `Fast`
- 任意の検証表示切替（導入する場合のみ）

現時点ではInput System導入状況を確認できないため、入力方式と具体的な割り当てはBlocking Open Decisionである。

Draft候補は次のとおりだが、承認済み入力ではない。

- カメラ移動: `WASD`または矢印キー
- ズーム: マウスホイール
- `Paused`: `Space`
- `Normal`: `1`
- `Fast`: `2`
- 検証表示切替: 必須にせず、必要な場合のみ`F1`

既存Unityプロジェクトに入力方式がある場合はそれに従う。新旧Input Systemを併用したり、新しい入力スタックを独断で導入したりしない。

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

1. Unityプロジェクト、Unityバージョン、Input System、Test Framework、既存規則を確認する。
2. Blocking Open Decisionsを解決し、Handoffを承認する。
3. 必要なフォルダとテスト／`.asmdef`構成を既存規則に合わせて確定する。
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

Test Framework確認後、次を自動テスト候補とする。EditMode／PlayModeの最終分類は、対象APIのUnity依存度と既存テスト方針に従う。

### Grid / Coordinate Conversion

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

### Camera / Zoom

- Zoom Minimum／Maximum／Initial Zoomの検証
- Min/Max逆転の拒否
- 非有限Zoom設定と入力の拒否
- Zoomが承認済み範囲を超えない

### Simulation Time

- `Paused`、`Normal`、`Fast`の状態遷移
- `Paused`中にElapsed Timeが増加しない
- `Normal`中に`1x`で増加する
- `Fast`中に指定倍率で増加する
- 状態変更後も累積値の連続性が維持される
- 複数回の切替でリセット、逆行、飛躍がない

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
- Unityプロジェクトの所在またはUnityバージョンが確認できない。
- 使用中の入力方式を確認できない。
- Test Frameworkまたはテスト実行環境を確認できない。
- 既存コード、Scene、Prefab、命名規則と本提案が競合する。
- Phase 1 System Boundaryを超える必要が生じる。
- 新しいUnity Packageが必要になる。
- 既存SceneまたはPrefabを破損する可能性がある。
- 承認済みSystem Specの変更が必要になる。
- Acceptance Criteriaを検証できる環境が不足している。
- ユーザーからUnity実装開始の明示許可がない。

## 18. Open Decisions

### 18.1 Blocking Open Decisions

#### OD-01 Unityプロジェクトと実行環境

- Decision: Unityプロジェクトの格納場所、Unityバージョン、Render Pipelineを確認する。
- Why: 作成パス、Scene、Camera、テスト方式を確定できないため。
- Recommended option: `Assets/`、`Packages/`、`ProjectSettings/`を含む正式Unityプロジェクトを同一リポジトリへ配置し、使用バージョンを固定してからHandoffを更新する。
- Alternatives: 別リポジトリまたは未反映ローカルプロジェクトを正式参照先として明示する。
- Impact: 未解決のままではファイル計画と実装環境を確定できない。
- Classification: Blocking

#### OD-02 入力方式

- Decision: 既存Input Systemの有無と採用する入力方式を確認する。
- Why: 入力実装と依存Packageを独断で決められないため。
- Recommended option: 既存Unityプロジェクトで採用済みの方式を使用する。未採用の場合は、別途承認を得る。
- Alternatives: Legacy Input ManagerまたはUnity Input System。
- Impact: 未解決のままではCameraと時間制御の入力実装を確定できない。
- Classification: Blocking

#### OD-03 テスト基盤

- Decision: Unity Test Framework、既存`.asmdef`、テスト配置規則を確認する。
- Why: 自動テストを追加・実行できる構造を確定できないため。
- Recommended option: 既存構成を優先し、存在しない場合はPhase 1に必要な最小限のEditModeテスト基盤だけを承認する。
- Alternatives: PlayMode併用、または人間確認のみ。ただしSystem Specの検証可能性を満たす必要がある。
- Impact: 未解決のままでは自動テスト成果物と完了証拠を確定できない。
- Classification: Blocking

### 18.2 Non-Blocking Open Decisions

#### OD-04 ファイルパス、namespace、Scene名

- Decision: 既存規則に合わせた正式名称を決める。
- Why: 現在のリポジトリから規則を確認できないため。
- Recommended option: §6の候補を出発点にし、Unityプロジェクト確認後に最小限の構成へ調整する。
- Alternatives: 既存プロジェクト固有の規則を採用する。
- Impact: 名称と配置のみ。System Boundaryは変えない。
- Classification: Non-Blocking after repository confirmation

#### OD-05 Phase 1設定値

- Decision: Grid、Camera、Zoom、Fast倍率の検証用仮値を承認する。
- Why: System Specで具体値が未確定のため。
- Recommended option: §8のDraft推奨値を人間確認し、Handoff承認時に確定する。
- Alternatives: Unityプロジェクトの既存設定値を採用する。
- Impact: 検証条件とInspector値。完成版仕様として固定しない。
- Classification: Non-Blocking for document review; Blocking before implementation

#### OD-06 入力割り当て

- Decision: 入力方式確定後にPhase 1の具体的なキーを決める。
- Why: 検証操作に必要だが完成版入力ではないため。
- Recommended option: §9のDraft候補を使用し、既存割り当てと競合する場合は既存規則を優先する。
- Alternatives: 既存プロジェクトの入力アセットに合わせる。
- Impact: 人間確認手順。ゲーム仕様は変更しない。
- Classification: Non-Blocking for document review; Blocking before implementation

## 19. Approval Gate

- 本書が`Draft`かつ`Approved: Pending`である間、Implementation Useは`Prohibited`である。
- 本書の承認前にUnity実装を開始してはならない。
- 承認済みSystem Specは本Handoff作成の入力として使用できるが、それ自体はUnity実装開始許可ではない。
- 本Handoffが承認された後も、ユーザーによる明示的なUnity実装許可が別途必要である。
- mainへのマージだけではUnity実装開始を許可しない。
- 承認後、Phase 1実装専用のCodex向け実装プロンプトを別途作成・承認する。
- Blocking Open Decisionが残る間、本HandoffをApprovedへ変更しない。

## 20. Phase 2 Protection

- Phase 2以降の住民、移動、経路探索を先行実装しない。
- Phase 1のGridを完成版向け汎用フレームワークへ拡張しない。
- Cameraへ追従、回転、最終マップ境界を追加しない。
- Timeへ住民、生産、建築などの更新責務を追加しない。
- 検証表示を完成版UIへ発展させない。
- 将来利用を理由に抽象クラス、汎用イベント基盤、サービスロケータ、DI基盤を追加しない。
- 後続Phaseの要求が確定した時点で、必要な変更をそのPhaseのSystem SpecとHandoffで判断する。
