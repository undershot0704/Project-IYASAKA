# Prototype 01 Phase 2 Implementation Handoff

Status: Approved  
Version: 1.0  
Approved: 2026-08-04  
Source System Spec: [Prototype 01 Phase 2 Pathfinding and Movement System Spec v1.0](../../03-system-specs/prototype-01/phase-02-pathfinding-and-movement.md)  
Source main HEAD: `d942b4a662fd3b2ff4a0844ddcd6d66f04fecf99`  
Unity Repository: [undershot0704/Project-IYASAKA-Unity](https://github.com/undershot0704/Project-IYASAKA-Unity)  
Unity Repository HEAD: `548b3c8f8e87b4545e8f408f0955bc9e2fc15eaf`  
Implementation Use: Permitted  
Unity Implementation: Prohibited  
Last Updated: 2026-08-04

> この文書は承認済みのImplementation Handoffである。ただし、Unity Repository側の実装ゲートが解消され、Unity実装開始の明示的な許可が別途与えられるまで、Unity実装、テスト作成、Unity Repository変更を開始してはならない。

## 1. Purpose

本書は、承認済みのPrototype 01 Phase 2 Pathfinding and Movement System Spec v1.0を、Codexまたは実装担当AIへ渡す具体的な作業指示へ変換するためのImplementation Handoffである。

対象はPhase 2「住民移動」に限定する。本書はゲーム仕様やSystem Specを追加・変更せず、実装対象、ファイル計画、責務、入力、初期化、実装順序、検証方法、成果物、停止条件およびレビューゲートを明確にする。

本書の作成はUnity実装開始を意味しない。Phase 2のUnity実装には、承認済みの本Handoff、Unity Repository側の実装ゲート解消、およびユーザーによる明示的な実装開始許可が必要である。

## 2. Source of Truth

実装時は、次の順序で承認済み文書と実装ルールを参照する。

1. [Prototype 01 Phase 2 Pathfinding and Movement System Spec v1.0](../../03-system-specs/prototype-01/phase-02-pathfinding-and-movement.md)
2. [Prototype 01 PDD v1.0](../../02-prototypes/prototype-01/pdd.md)
3. [GDD v1.0](../../01-gdd/gdd.md)
4. 承認済みの本Implementation Handoff
5. Unity実装リポジトリの`AGENTS.md`
6. [Prototype 01 Roadmap](../../02-prototypes/prototype-01/roadmap.md)

本書は上位文書を上書きしない。文書間の矛盾、意味の分岐、必要情報の欠落を発見した場合は、推測で実装せず停止して報告する。

実装開始時には、承認済み文書のVersion、仕様リポジトリの`main` HEAD、Unity Repositoryの`main` HEAD、本Handoffの承認状態を再確認する。

### 2.1 System Spec Traceability

| System Spec | 本Handoffでの展開先 |
|---|---|
| §3 Scope／§4 Out of Scope | §3、§4、§22 |
| §6 System Boundary | §7 |
| §7 Traversability Model | §7.1、§8、§11 |
| §8 Pathfinding | §7.2、§8、§11、§13 |
| §9 Resident Movement | §7.3〜§7.5、§8、§10、§11 |
| §10 Simulation Time Integration | §7.4、§8、§10、§13、§14 |
| §11 Debug and Validation Display | §7.6、§9、§10、§14 |
| §12 State and Data | §7、§8 |
| §13 Error and Edge Cases | §11、§13、§14 |
| §14 Initialization Order | §10、§12 |
| §15 Runtime Flow | §10 |
| §16 Acceptance Criteria | §13、§14、§16 |
| §17 Verification Plan | §13〜§16、§23 |
| §18 Phase 3 Handoff Requirements | §22 |
| §19 Open Questions | §5.2、§8、§9、§18 |
| §20 Approval and Implementation Gate | §19、§21、§24 |

## 3. Implementation Scope

Phase 2で実装対象とする成果は次のとおり。

- 固定されたBlocked Cell設定とTraversability判定
- Blocked Cell設定全体の検証と利用可能状態の管理
- 8方向の隣接Cell取得と角抜け禁止
- Orthogonal Cost `1`、Diagonal Cost `√2`、Octile Distanceを用いるA*
- 定義済み移動コストに基づく最小総コスト経路
- 同一入力で再現可能なPathfinding結果
- Success、Invalid Start、Invalid Destination、Unreachable、Unavailableの結果
- 未配置または配置済みとして扱う住民1人のPlacement State
- 配置済み住民のCurrent CellとWorld Position
- Cell中心間の滑らかなWorld座標移動
- 基準移動速度`1 Cell/秒`と変更可能な正の有限設定
- Phase 1のPaused／Normal／Fastに従う移動
- Idle、Moving、Succeeded、FailedのMovement Status
- Phase 2検証用の配置、移動開始、リセット
- Blocked Cell、住民、Path、結果、失敗理由の検証表示
- Phase 2 Acceptance Criteriaを確認する自動テストとHuman Verification

Phase 1のGrid、Coordinate Conversion、Camera、Simulation Time、Start Cell、Destination Cellおよび常時表示／Overlay分離を再利用し、その責務をPhase 2へ移さない。

## 4. Explicit Out of Scope

次はPhase 2では実装しない。

- 複数住民、住民同士の競合、物理衝突、セル占有、経路回避
- Task System、Reservation System、Resident FSM
- Gather、Haul、Build、木、木材、倉庫、建設予定地、建物
- 道路、道路コスト、道路速度補正、地形別移動コスト
- 動的障害物、移動中の再経路探索、経路予約
- 高度な経路最適化、階層型経路探索、汎用Pathfinding Framework
- 移動取消、中断、目的地変更
- 住民の向き、歩行Animation、完成版Art
- 住民の仕事、欲求、優先順位、意思決定、Ambient Behavior
- 完成版の住民直接操作、完成版UI、Save／Load
- Phase 3以降の機能と先行実装
- 新規Package、外部Asset、Project Settings変更
- Phase 1のGrid、Camera、Simulation Timeを完成版Frameworkへ拡張すること
- 将来利用だけを理由とする抽象層、DI、Service Locator、Event Bus
- System Specの変更、Acceptance Criteriaの変更

## 5. Repository Findings

### 5.1 確認できた事実

仕様リポジトリは次へバインドする。

- Repository: `undershot0704/Project-IYASAKA`
- Branch: `main`
- 基準HEAD: `d942b4a662fd3b2ff4a0844ddcd6d66f04fecf99`
- Phase 2 System Spec: `Approved / Version 1.0 / Implementation Use: Permitted`
- Phase 2 Unity Implementation: `Prohibited`

Unity実装リポジトリは読み取り確認時点で次の状態である。

- Repository: `undershot0704/Project-IYASAKA-Unity`
- Visibility: Private
- Branch: `main`
- 基準HEAD: `548b3c8f8e87b4545e8f408f0955bc9e2fc15eaf`
- Phase 1実装: Merge済み
- Runtime asmdef: `Assets/IYASAKA/Scripts/IYASAKA.Runtime.asmdef`
- EditMode Tests asmdef: `Assets/IYASAKA/Tests/EditMode/IYASAKA.Tests.EditMode.asmdef`
- Scene: `Assets/IYASAKA/Scenes/Phase01Foundation.unity`
- Input Actions: `Assets/InputSystem_Actions.inputactions`の`Phase01` Action Map

Phase 1の再利用対象は次のとおり。

- `GridFoundation`: Grid設定、有効Cell判定、Cell／World変換
- `SimulationTimeController`: Paused／Normal／Fast、現在倍率、Simulation Elapsed Time
- `Phase01VerificationDisplay`: Start Cell、Destination Cell、常時Grid表示、F1 Overlay
- `Phase01Bootstrap`: Phase 1初期化と既存入力接続
- `Phase01CameraController`: Simulation Timeから独立したCamera操作

Unity Repositoryの`AGENTS.md`は、確認時点で「Prototype 01 Phase 1のみ実装可能」「Phase 2以降は禁止」と定めている。承認済みHandoffとユーザーの明示許可だけではこのRepository Gateを上書きできない。Phase 2実装開始前に、ユーザーが許可した別変更として`AGENTS.md`をPhase 2実装可能状態へ更新し、`main`へ反映する必要がある。

### 5.2 本Handoffで確定する検証設定

System Spec §19でImplementation Handoffへ委ねられた範囲を、次のとおり確定する。

- Start Cell指定: 既存のMouse Left Clickを維持
- Destination Cell指定: 既存のMouse Right Clickを維持
- 住民配置: Keyboard `P`
- 経路探索および移動開始: Keyboard `Enter`
- 再検証用リセット: Keyboard `R`
- Overlay切替: 既存の`F1`を維持
- 住民表示: Phase 2専用の単純な単色Marker
- Path表示: Cell中心を順序どおり結ぶPhase 2専用Line
- Blocked Cell表示: Traversable Cellと識別可能な単色塗りまたはMarker
- 失敗理由: Verification OverlayとConsoleの有限な1回単位の記録
- 基準移動速度: Scene上のSerialized設定、初期値`1 Cell/秒`

これらはPrototype検証操作であり、完成版の住民直接操作やUIを定義しない。

## 6. File Plan

次をPhase 2のFile Planとする。実装開始時にUnity Repository HEADと実在ファイルを再確認し、同名責務との競合がある場合は作成せず停止する。

| 種別 | パス／ファイル名 | 責務 | 依存・禁止 |
|---|---|---|---|
| 新規 | `Assets/IYASAKA/Scripts/Pathfinding/CellTraversability.cs` | Blocked Cell設定検証、利用可能状態、通行可否 | Gridを変更しない。動的障害物・占有を持たない |
| 新規 | `Assets/IYASAKA/Scripts/Pathfinding/PathfindingResult.cs` | Status、Cell Path、総コスト、Failure Reasonの値 | MovementやMonoBehaviour責務を持たない |
| 新規 | `Assets/IYASAKA/Scripts/Pathfinding/AStarPathfinder.cs` | 8方向A*、角抜け禁止、最小総コスト、再現性 | World移動・Simulation Timeを参照しない |
| 新規 | `Assets/IYASAKA/Scripts/Residents/ResidentPositionState.cs` | Placement State、Current Cell、World Position | 経路探索・入力を持たない |
| 新規 | `Assets/IYASAKA/Scripts/Residents/ResidentMovementController.cs` | Path検証、区間消費、位置更新、Movement Status | 再探索・目的選択を行わない |
| 新規 | `Assets/IYASAKA/Scripts/Phase02/Phase02ValidationController.cs` | 配置、移動開始、リセット要求とLast Result | 完成版Command／Task Systemへ拡張しない |
| 新規 | `Assets/IYASAKA/Scripts/Debug/Phase02VerificationDisplay.cs` | Blocked Cell、住民、Path、Phase 2状態の表示 | Phase 1常時表示／F1責務を壊さない |
| 新規 | `Assets/IYASAKA/Scripts/Phase02Bootstrap.cs` | Phase 1依存確認、Phase 2初期化、入力接続、更新順 | 汎用DI／Service Locatorを導入しない |
| 新規 | `Assets/IYASAKA/Scenes/Phase02PathfindingAndMovement.unity` | Phase 2主要確認Scene | Phase 1 Sceneを直接変更しない |
| 変更 | `Assets/InputSystem_Actions.inputactions` | `PlaceResident`、`StartMovement`、`ResetPhase02` Action追加 | 既存Action／Bindingを変更しない |
| 新規 | `Assets/IYASAKA/Tests/EditMode/CellTraversabilityTests.cs` | Traversabilityと設定異常 | Runtime asmdefを参照 |
| 新規 | `Assets/IYASAKA/Tests/EditMode/AStarPathfinderTests.cs` | Cost、最短、角抜け、失敗、再現性 | 実装内部順序に過剰依存しない |
| 新規 | `Assets/IYASAKA/Tests/EditMode/ResidentMovementControllerTests.cs` | Placement、Path消費、速度、Pause、失敗 | 表示方式を検証対象にしない |
| 新規 | `Assets/IYASAKA/Tests/EditMode/Phase02ValidationControllerTests.cs` | 要求順序、Moving中拒否、Reset | 完成版入力を前提にしない |
| 条件付き | `Assets/IYASAKA/Tests/PlayMode/Phase02RuntimeIntegrationTests.cs` | Scene接続と時間入力の統合 | EditModeで確認不能な場合だけ作成 |

対応する`.meta`をUnityに生成させ、コードと同じCommitへ含める。空フォルダ、将来用フォルダ、Phase 3用ファイルを先行作成しない。

### 6.1 asmdef方針

既存のRuntime／EditMode Tests asmdefを再利用する。Phase 2専用asmdef、Pathfinding専用asmdef、Residents専用asmdefは追加しない。

PlayModeテストが必要な場合は既存の`Assets/IYASAKA/Tests/PlayMode/IYASAKA.Tests.PlayMode.asmdef`が存在するか確認し、存在しない場合だけPhase 1 Handoffの規則に従う最小asmdefを追加する。PackageまたはAssembly Definition Referenceを不要に増やさない。

### 6.2 Scene方針

`Assets/IYASAKA/Scenes/Phase01Foundation.unity`を基礎として、`Assets/IYASAKA/Scenes/Phase02PathfindingAndMovement.unity`を新規作成する。Phase 1 Scene、SampleScene、URP2D Template Sceneを直接変更しない。

Phase 2 Sceneは既存Phase 1構成を保持し、Phase 2 Bootstrap、Validation Controller、Verification Displayおよび必要なSerialized参照だけを追加する。Scene Hierarchyの具体名は、責務と確認容易性を保つ最小構成とする。

### 6.3 Namespace方針

既存ルートnamespace `IYASAKA`を維持し、次を使用する。

- `IYASAKA.Pathfinding`
- `IYASAKA.Residents`
- `IYASAKA.Debugging`
- Phase 2 Bootstrap／Validationは`IYASAKA`
- Testsは`IYASAKA.Tests`

不要に深いnamespaceまたは汎用Domain層を作成しない。

## 7. Component and Class Responsibilities

### 7.1 Cell Traversability

- GridとBlocked Cell集合を初期化入力として受ける。
- Blocked Cellを重複のない固定集合として保持する。
- Blocked Cellに無効Cellが1件でもあれば初期化を失敗させ、`IsAvailable = false`とする。
- 無効項目だけを除去して続行せず、Clamp、Silent Recovery、自動修復を行わない。
- 有効CellかつBlocked集合に含まれないCellだけをTraversableとする。
- 範囲外CellをBlockedへ変換せず、無効として扱う。
- Resident、Movement Status、Simulation Timeに依存しない。
- Runtime中にBlocked集合を変更するAPIを持たない。

### 7.2 A* Pathfinding

- Start、Destination、Grid、Traversabilityを検証する。
- 上、下、左、右、左上、右上、左下、右下の8方向を評価する。
- Diagonalでは移動先に加え、隣接する2つのOrthogonal CellがTraversableである場合だけ許可する。
- Orthogonal Costを`1d`、Diagonal Costを`Math.Sqrt(2d)`とする。
- HeuristicにOctile Distanceを使用する。
- PathはStartとDestinationを含む順序付き`Vector2Int`列とする。
- Success時に総コストを返し、Failure時に部分Pathまたは前回Pathを返さない。
- Start／Destination同一時は1 Cell Path、Cost `0`、Successを返す。
- Unavailable、Invalid Start、Invalid Destination、Unreachableを区別する。
- 同一入力で再現可能にするため、同一評価値の順序を次で固定する。
  1. 推定総コスト`f`の昇順
  2. Heuristic`h`の昇順
  3. Cell Yの昇順
  4. Cell Xの昇順
- Neighbor評価順は、上、下、左、右、左上、右上、左下、右下で固定する。
- 同値処理規則は再現性のためだけに使用し、最小総コスト要件を変更しない。
- PathfindingはResident Position、World Position、Movement Status、Simulation Timeを変更または参照しない。

### 7.3 Resident Position State

- Placement Stateを`Unplaced`または`Placed`として保持する。
- Unplaced中はCurrent Cellを持たない。
- 有効かつTraversableなStart Cellへの配置だけを受理する。
- 配置時にCurrent CellをStart Cellへ、World PositionをCell中心へ設定する。
- Cell間移動中は最後に到達したCurrent Cellを維持する。
- Cell中心到達通知でCurrent Cellを更新する。
- Idle／Succeeded／Failedで移動していない場合、World PositionをCurrent Cell中心へ一致させる。
- Pathfinding、入力、目的地選択を行わない。

### 7.4 Resident Movement Controller

- Placement済みResident、Grid、Simulation Time依存が利用可能な場合だけ初期化する。
- 基準移動速度は正の有限値だけを受理し、初期値を`1 Cell/秒`とする。
- Movement StatusをIdle、Moving、Succeeded、Failedとして保持する。
- Movement開始前に、Path非空、Path先頭とCurrent Cellの一致、全区間の隣接性、Traversability、角抜け条件を検証する。
- Path先頭を重複移動せず、次Cell中心をCurrent Targetとする。
- World速度を`baseMovementSpeed * cellSize`とする。
- Phase 2 Bootstrapから渡された`simulationDeltaTime`を一度だけ適用する。
- 更新量が区間終点を越える場合、終点へ確定し、残距離を次区間へ繰り越す。
- Cell中心到達時だけCurrent Cellを更新する。
- Destination中心到達時に未処理区間がないことを確認しSucceededとする。
- Failure時は最後の正常なCurrent Cellを維持し、World Positionをその中心へ戻す。
- Pathを追加、短縮、再探索、最適化しない。
- Moving中の新要求、再配置、Resetを直接処理せずValidation Controllerへ拒否結果を返す。

### 7.5 Phase 2 Validation Controller

- Selected Start CellとSelected Destination Cellは既存Phase 1 Verification Displayから読み取る。
- `PlaceResident`要求時に、Movingでないこと、Start指定済み、有効Cell、Traversableを確認する。
- 配置成功時にResidentを配置し、MovementをIdleへ戻し、Pathと前回移動区間を消去する。
- `StartMovement`要求時に、ResidentがPlacedであることを最初に確認する。
- Resident Current CellをPathfinding Start、Selected Destination CellをDestinationとして探索する。
- Pathfinding成功時だけPathをMovementへ渡す。
- Same-Cell Successは即時Succeededとする。
- Failure時は住民を移動させず、Pathfinding／Movement Failure Reasonを公開する。
- `ResetPhase02`はMovingでない場合だけ、ResidentをUnplaced、MovementをIdle、Pathを未実行へ戻す。Phase 1のGrid、Camera、Time、選択Cellは変更しない。
- Moving中の配置、移動開始、Reset要求を拒否し、現在の移動を継続する。
- 完成版のCommand、Task、AI、直接移動操作へ拡張しない。

### 7.6 Phase 2 Verification Display

- Game ViewでBlocked Cell、Resident Marker、Current Path、Current／Target Cellを識別可能にする。
- Phase 1のGrid外周、Cell境界、Start、Destinationの常時表示を維持する。
- Resident Placement State、Current Cell、World Position、Current Target、Pathfinding Result、Path Cost、Movement Status、Base Speed、Time State、Multiplier、Last Result、Failure ReasonをOverlayで確認可能にする。
- F1 OFFでもBlocked Cell、Resident、Path、移動結果をGame Viewで観察可能にする。
- Camera移動とZoomへ追従する。
- 読み取りと表示だけを担当し、Pathfinding、Movement、Placement、Time状態を変更しない。
- 完成版UI、汎用Grid Renderer、完成版Resident Viewへ発展させない。

### 7.7 Phase 2 Bootstrap / Scene Composition

- Phase 1 Bootstrap、Grid、Camera、Simulation Time、Verification Displayの初期化完了を確認する。
- §10の順序でPhase 2構成を初期化する。
- 既存`Phase01` Action MapからPhase 1入力を維持し、Phase 2追加Actionを接続する。
- 毎Frame、`Time.unscaledDeltaTime * CurrentMultiplier`を`simulationDeltaTime`として一度だけMovementへ渡す。
- Camera入力はPhase 1側へ残し、Movement倍率を適用しない。
- 入力イベント、Pathfinding要求、Movement更新、表示更新を重複実行しない。
- 汎用Update Pipeline、DI、Service Locatorを追加しない。

## 8. Data and Configuration

| 項目 | Phase 2設定／型 | 所有者 |
|---|---|---|
| Blocked Cells | Serialized `Vector2Int`集合。初期化後固定 | Cell Traversability |
| Placement State | `Unplaced / Placed` | Resident Position |
| Current Cell | 配置済み時のみ有効な`Vector2Int` | Resident Position |
| World Position | `Vector3` | Resident Position／Movement |
| Base Movement Speed | 初期値`1f Cell/秒`、正の有限値 | Movement |
| Pathfinding Status | `NotRun / Success / Failed` | Pathfinding Result |
| Failure Reason | `None / InvalidStart / InvalidDestination / Unreachable / Unavailable` | Pathfinding |
| Current Path | Start／Destinationを含む読み取り専用Cell列 | Pathfinding Result |
| Path Cost | `double`、区間Cost合計 | Pathfinding Result |
| Path Index／Target Cell | 現在区間の進行 | Movement |
| Movement Status | `Idle / Moving / Succeeded / Failed` | Movement |
| Last Movement／Validation Result | 成否と理由 | Validation Controller |

Phase 2主要確認Sceneの既定Blocked Cellsは次とする。

- Detour Barrier: `(34,28)`〜`(34,35)`の縦8 Cell
- Unreachable Enclosure: `(43,31)`、`(44,31)`、`(45,31)`、`(43,32)`、`(45,32)`、`(43,33)`、`(44,33)`、`(45,33)`

既定Human Verification座標は次とする。

- 開けたOrthogonal: Start `(24,24)`、Destination `(28,24)`
- 開けたDiagonal: Start `(24,24)`、Destination `(28,28)`
- Detour: Start `(30,32)`、Destination `(38,32)`
- Corner Rule: Testまたは一時的な検証設定でStart `(30,30)`、Destination `(31,31)`、Blocked `(31,30)`
- Unreachable: Start `(40,32)`、Destination `(44,32)`
- Same Cell: Start／Destination `(24,24)`

Corner Rule用の設定変更はPlay開始前に行い、Pathfinding開始後にBlocked Cellsを動的変更しない。検証後に承認済み既定値へ戻す。Invalid Blocked設定はテストまたは専用の検証コピーで行い、正常設定へSilent Recoveryしない。

## 9. Input Mapping

既存`Assets/InputSystem_Actions.inputactions`の`Phase01` Action Mapを継続使用し、既存ActionとBindingを変更せず次の3 Actionだけを追加する。

| Action | Action Type | Control Type | Binding | 条件 |
|---|---|---|---|---|
| `PlaceResident` | Button | Button | Keyboard `P` | Moving中は拒否 |
| `StartMovement` | Button | Button | Keyboard `Enter` | PlacedかつDestination有効時 |
| `ResetPhase02` | Button | Button | Keyboard `R` | Moving中は拒否 |

Mouse Left ClickのStart指定、Mouse Right ClickのDestination指定、`F1`、Camera、Pause、Normal、Fastは既存挙動を維持する。

Input追加を完成版Action Map、Input Remapping UI、Gamepad、Touch、汎用Command Frameworkへ拡張しない。

## 10. Runtime Flow

### 10.1 Initialization

1. Phase 1 Grid、Coordinate Conversion、Camera、Simulation Time、Phase 1 Bootstrap／Displayが利用可能であることを確認する。
2. Blocked Cell設定全体を検証する。
3. 1件でも無効CellがあればTraversabilityを利用可能状態にせず、依存するPhase 2領域を開始しない。
4. 有効な場合だけTraversabilityを初期化する。
5. Base Movement Speedを検証する。
6. Resident PositionをUnplaced／Current Cellなしで初期化する。
7. Pathfindingを利用可能にする。
8. MovementをIdleで初期化する。
9. Validation Controllerを初期化する。
10. Phase 2 Verification Displayを各読み取り状態へ接続する。
11. Phase 2 Input Actionsを接続する。
12. Phase 2全体の利用可能状態を確認する。

Start Cellが選択済みでも、`P`による配置要求まではResidentを自動配置しない。これにより未配置状態を正式に検証可能にする。

### 10.2 Frame Flow

1. Phase 1がCamera、Zoom、Start／Destination指定、Time変更を処理する。
2. Phase 2がPlace、Start Movement、Reset要求を1回ずつ取得する。
3. Place要求時はMovingでないこととStart Cellの有効性／Traversabilityを確認し、ResidentをCell中心へ配置する。
4. Start Movement要求時はResidentが配置済みであることを最初に確認する。
5. Current CellとDestination Cellを検証し、Pathfindingを1回実行する。
6. Failure時は理由を公開し、Movementを開始しない。
7. SuccessかつSame Cellなら即時Succeededとする。
8. 移動区間があればPathをMovementへ渡しMovingとする。
9. `Time.unscaledDeltaTime * CurrentMultiplier`からsimulationDeltaTimeを求める。
10. Pausedなら移動量を増加させずPathと区間状態を維持する。
11. Normal／Fastなら未消費距離を区間へ順番に適用する。
12. Cell中心到達ごとにCurrent CellとPath Indexを更新する。
13. Destination中心到達時にWorld Positionを中心へ確定しSucceededとする。
14. 不正Pathまたは依存異常時はFailedとし、最後の正常なCurrent Cell中心を維持する。
15. Pathfinding、Movement、Resident、Time、Validationの読み取り値を表示へ反映する。

## 11. Error and Edge-Case Requirements

- Blocked Cell設定に無効Cellが1件でもあれば、設定全体を拒否しTraversabilityをUnavailableにする。
- 無効Blocked項目だけを無視、除去、Clamp、自動修復しない。
- 未配置ResidentにCurrent Cellを与えず、移動要求を開始しない。
- Start未指定／無効／Blockedでは配置と探索を開始せずInvalid Startを記録する。
- Destination未指定／無効／Blockedでは探索と移動を開始せずInvalid Destinationを記録する。
- Same CellではCost 0のSuccessとし、区間を生成せずSucceededとする。
- Unreachableでは部分Pathを返さず住民を移動させない。
- 角抜け条件を満たさないDiagonalを候補へ含めない。
- 空Path、先頭不一致、非隣接区間、Blocked Cell、角抜けを含むPathを拒否する。
- Movement失敗時は最後の正常Current Cellとその中心World Positionを維持する。
- Base Movement Speedが0以下または非有限ならMovement初期化を失敗させる。
- Moving中のPlace、Start Movement、Resetを拒否し現在の移動を継続する。
- Paused中は位置、Path、区間、Moving状態を維持する。
- 大きなsimulationDeltaTimeでも終点を通過せず、残距離を複数区間へ正しく適用する。
- 依存未初期化時は利用可能結果を返さず原因を確認可能にする。
- Failureのたびに無限ログまたは毎Frame警告を生成しない。
- NaN／Infinityでクラッシュ、無効状態遷移、非有限World Positionを発生させない。

## 12. Implementation Order and Checklist

### 12.1 Implementation Order

1. 仕様`main` HEADと承認済み文書を再確認する。
2. Unity Repository`main` HEADと`AGENTS.md`を再確認する。
3. 本HandoffがApprovedで、Repository Gateが解消され、ユーザーの明示許可があることを確認する。
4. Unity作業ブランチを作成する。
5. Traversabilityとテストを実装する。
6. Pathfinding結果、A*、Cost、再現性とテストを実装する。
7. Resident PositionとMovement、速度／区間消費テストを実装する。
8. Validation Controllerと要求拒否テストを実装する。
9. Input Actionsへ3 Actionだけ追加する。
10. Phase 2 BootstrapとVerification Displayを実装する。
11. Phase 2 Sceneを新規作成して接続する。
12. EditMode Testsを実行する。必要な場合だけPlayMode Testを追加・実行する。
13. §14のHuman Verificationを全手順実施する。
14. Console、変更ファイル、Scope、Completion Evidenceを確認する。
15. 実装Commitを作成し、許可された場合だけPushしてDraft PRを作成する。
16. Draft PR作成後に停止する。

### 12.2 Implementation Checklist

- [ ] Approved System Spec v1.0を使用している
- [ ] Approved Implementation Handoffを使用している
- [ ] Unity`AGENTS.md`のPhase 2禁止が解消されている
- [ ] ユーザーの明示的なUnity実装許可がある
- [ ] Unity Repository基準HEADを再確認した
- [ ] 変更が§6 File Plan内に限定されている
- [ ] Phase 1責務と既存Bindingを維持した
- [ ] Traversabilityが無効設定をSilent Recoveryしない
- [ ] A*が最小総コストと再現性を満たす
- [ ] Resident Placement Stateと未配置状態を実装した
- [ ] MovementがPathfindingを行わない
- [ ] Paused／Normal／Fastを一度だけ適用した
- [ ] Phase 3以降の機能を実装していない
- [ ] 自動テストとHuman Verificationを完了した
- [ ] Console基準を満たした
- [ ] Completion Evidenceを作成した
- [ ] Draft PR作成後に停止した

## 13. Automated Test Candidates

### EditMode: Traversability

- Valid CellのTraversable／Blocked判定
- 範囲外CellがTraversableにならないこと
- 無効Blocked Cellが1件でもあれば全体Unavailable
- 無効項目の無視・修復を行わないこと
- 同じ入力に対する安定した判定

### EditMode: A* Pathfinding

- 上下左右とDiagonalの8方向
- Orthogonal Cost `1`、Diagonal Cost `√2`
- Octile Distance
- Start／Destinationを含むPath
- Open GridのOrthogonal／Diagonal最短Cost
- Detour Barrierの最小総コストPath
- Corner Ruleの3条件
- Unreachableと部分Pathなし
- Invalid Start／Invalid Destination／Unavailable
- Same Cellの1 Cell Path／Cost 0
- Path全区間の隣接性、Traversability、角抜け禁止
- 同一入力100回で同一Path／Cost
- 同一最小Cost経路が複数ある配置で同一結果

### EditMode: Resident Position and Movement

- UnplacedでCurrent Cellなし
- 配置時のCurrent Cell／World中心一致
- Cell間でCurrent Cell維持、中心到達で更新
- Path先頭不一致、空Path、非隣接、Blocked、角抜けPath拒否
- Orthogonal 1 CellをNormal／1 Cell秒で1秒
- Diagonal 1 Cellを`√2`秒
- Cell Size変更時もCell単位速度を維持
- 複数区間を越える更新量の残距離消費
- Destination中心でSucceeded
- Failure後の最後の正常Cell維持
- 0以下／非有限Base Speed拒否
- Paused停止、Resume継続、Fast倍率

### EditMode: Validation Control

- UnplacedでStart Movement拒否
- Valid StartへのPlace成功
- Invalid／Blocked StartへのPlace拒否
- Pathfinding失敗でMovementを開始しない
- Same Cell即時成功
- Moving中のPlace／Start／Reset拒否と状態維持
- Idle／Succeeded／FailedからのReset

### PlayMode: Runtime Integration（必要な場合のみ）

- Scene参照と初期化順
- Input Actionが1回だけ発火すること
- Paused／Normal／FastとMovementの統合
- F1 OFFでもResident／Blocked／Pathが表示されること

表示の見た目、Camera追跡、操作感はHuman Verificationを中心とする。

## 14. Human Verification Procedure

Unity実装が明示許可された後、Phase 2 Sceneで次を順番に実施する。各手順のPass／Fail、実測値、Failure Reason、Console状態を記録する。

1. SceneとProject構成を確認する。Phase 1 Sceneが未変更で、Phase 2 Sceneと§6のファイルだけが追加・変更されていることを確認する。
2. Play開始時にGrid、Blocked Cells、Start／Destination、Resident Unplaced、Time Normal `1x`を確認する。
3. UnplacedのままEnterを押す。移動せず、Current Cellなしと拒否理由を確認する。
4. Start `(24,24)`をLeft ClickしPを押す。ResidentがCell中心へ配置され、Placed／Idleになることを確認する。
5. Destination `(28,24)`をRight ClickしEnterを押す。Orthogonal Path、Cost `4`、Moving、滑らかな移動、約4秒、目的中心でSucceededを確認する。
6. Reset後、Start `(24,24)`、Destination `(28,28)`でDiagonal移動を実行する。Cost `4√2`、約`4√2`秒、一定World速度を確認する。
7. Start `(30,32)`、Destination `(38,32)`でDetour Barrierを迂回し、Blocked CellをPathへ含めず最小総コストで到達することを確認する。
8. Corner Rule設定でStart `(30,30)`、Destination `(31,31)`、Blocked `(31,30)`を使用し、直接Diagonal角抜けが発生しないことを確認する。
9. Start `(40,32)`、Destination `(44,32)`でUnreachableを確認する。Residentが移動せず、部分Pathなし、理由表示を確認する。
10. Start／Destinationを`(24,24)`へ揃え、Cost 0、移動区間なし、即時Succeededを確認する。
11. InvalidまたはBlocked Startを指定してPを押し、既存の正常状態を破壊せずInvalid Startを確認する。
12. InvalidまたはBlocked DestinationでEnterを押し、Residentが移動せずInvalid Destinationを確認する。
13. Moving中にP、Enter、Rを押し、各要求が拒否され現在移動が継続することを確認する。
14. Moving中にSpaceでPausedへ移行し、World Position、Current Cell、Path Indexが停止することを確認する。Camera移動、Drag、Zoom、F1は機能することを確認する。
15. Resume後に同一区間から継続し、Normal／Fast切替で速度だけが変化し、Pathや位置がReset・逆行・重複しないことを確認する。
16. Fast中に複数区間相当の更新が発生してもCell中心を外れず、未消費距離が次区間へ適用されることを確認する。
17. Camera位置とZoom `4`／`24`でResident、Blocked、Path、Current／Target Cellを追跡できることを確認する。
18. F1 OFFでOverlayだけが消え、Grid、Start、Destination、Blocked、Resident、Path、移動結果がGame Viewに残ることを確認する。
19. 同一入力と同一Blocked設定で複数回実行し、PathとCostが再現されることを確認する。
20. Invalid Blocked設定と0以下／非有限Base Speedを検証用構成で与え、Unavailable／初期化失敗となりSilent Recoveryしないことを確認する。
21. Task、Reservation、Resident FSM、複数住民、道路効果、動的再探索がScene、Scripts、状態表示に存在しないことを確認する。
22. Play終了後、Console Error、未処理例外、無限ログ、新規Warning、著しい操作遅延または描画負荷を確認する。

## 15. Completion Evidence

### 15.1 必須

- Unity実装Commit SHA
- Unity作業ブランチ
- Draft PR URL
- 変更ファイル一覧
- 新規ファイル一覧
- `.meta`一覧
- 自動テスト名、件数、Pass／Fail
- §14の全Human Verification結果
- Inspector実測値と検証座標
- Console Error／Warning件数と内容
- Scope外変更なし
- Phase 3以降の機能なし
- 既知制限
- 未解決事項
- 基準Unity Repository HEADとの差分

Human Verificationは手順番号ごとに記録する。自動テスト、Console、Commit、PR履歴と合わせて追跡可能にする。

### 15.2 任意

- Screenshot
- Video
- GIF
- その他の視覚的証跡

視覚的証跡は補助であり、未取得だけを理由に完了判定またはMergeを拒否しない。

## 16. Acceptance Mapping

System Spec v1.0のAcceptance Criteriaを変更せず実装対象と証拠へ接続する。

| System Spec Acceptance対象 | 実装対象 | 自動テスト | Human Verification | Completion Evidence |
|---|---|---|---|---|
| Traversable／Blocked、範囲外 | Cell Traversability | Valid／Blocked／Out-of-range | 2、7、20 | Traversability結果、表示、テスト |
| 無効Blocked設定、Silent Recovery禁止 | Cell Traversability／Bootstrap | 設定全体拒否 | 20 | Unavailable理由、Console、テスト |
| A* 8方向、Cost 1／√2、Octile | AStar Pathfinder | Direction／Cost／Heuristic | 5、6 | Path／Cost、テスト |
| 最小総コスト経路 | AStar Pathfinder | Open／Detour最小Cost | 5〜7 | Path、Cost、テスト |
| 角抜け禁止 | Traversability／AStar／Movement | Orthogonal隣接条件 | 8 | Corner結果、テスト |
| Start／Destinationを含む有効Path | Pathfinding Result | 全Cell／区間検証 | 5〜8 | Path表示、テスト |
| Unreachable、部分Pathなし | AStar／Validation | Enclosure | 9 | Failure Reason、Pathなし |
| Same Cell Cost 0 | AStar／Movement | 1 Cell Path | 10 | 即時Succeeded |
| Invalid Start／Destination | Validation／AStar | 個別Failure | 11、12 | Failure Reason |
| 再現可能な同一最小Cost結果 | AStar tie-break | 反復／複数同Cost | 19 | Path比較、テスト |
| 道路効果・地形別Costなし | File Plan／Scope | 依存確認 | 21 | 変更一覧、Scope確認 |
| Resident Unplaced／Placed | Resident Position／Validation | Placement State | 2〜4 | Overlay、テスト |
| Current Cell／World Position | Resident Position／Movement | 配置・区間・到達 | 4〜6 | 状態記録 |
| Cell中心間の滑らかな移動 | Movement | 区間消費 | 5〜8、16 | Human結果、テスト |
| 目的中心でSucceeded | Movement | 最終位置／未処理区間 | 5〜7、10 | 成功結果 |
| 1 Cell/秒、Diagonal一定速度 | Movement | 所要時間 | 5、6 | 実測値、テスト |
| 大きな更新量の残距離適用 | Movement | 複数区間消費 | 16 | Fast結果、テスト |
| 不正PathでFailed、正常Cell維持 | Movement | 各不正Path | 20 | Failure、位置維持 |
| Moving中の新要求拒否 | Validation | Place／Start／Reset | 13 | 拒否結果、継続結果 |
| Paused停止／Resume継続 | Bootstrap／Movement | Delta 0／再開 | 14、15 | 位置・Index記録 |
| Fast倍率、Time切替連続性 | Bootstrap／Movement | 倍率別距離 | 15、16 | 実測値、テスト |
| CameraがTime倍率から独立 | Phase 1再利用 | 必要時統合Test | 14、15、17 | Camera確認 |
| Game View識別とOverlay分離 | Phase 2 Display | 必要時PlayMode | 2、17、18 | Human結果 |
| Failure Reason確認 | Display／Validation | Result Model | 3、9、11、12、20 | Overlay／Console |
| 住民1人、Phase 3機能なし | File Plan／Protection | 構成確認 | 21 | 変更一覧、Scope確認 |

## 17. Stop Conditions

### 17.1 Handoff Approval Gate

次のいずれかに該当した場合はHandoff承認を停止して報告する。

- Handoff本文内、またはApproved GDD、PDD、System Specとの間に矛盾がある。
- System SpecのScope、Out of Scope、責務、Acceptance CriteriaとのTraceabilityを確立できない。
- Acceptance MappingがSystem Spec Acceptance Criteriaを実装対象、自動テスト、Human Verification、Completion Evidenceへ接続していない。
- Verification PlanではSystem Spec Acceptance Criteriaを検証できない。
- Implementation Checklistが実装範囲、責務分離、禁止事項、検証および実装開始Gateを網羅していない。
- Handoff承認のためにPhase 2 Scope、System Spec Acceptance Criteria、責務を変更する、または新規仕様を追加する必要がある。

Unity Repositoryの`AGENTS.md`がPhase 2実装を禁止していること、本Handoffが未承認であること、およびユーザーのUnity実装開始許可がないことは、Handoff承認を停止する条件ではない。これらは§17.2のUnity Implementation Gateとして扱う。

### 17.2 Unity Implementation Gate

次の条件をすべて満たすまでUnity実装を開始してはならない。

- Approved Implementation Handoffが存在する。
- Unity Repositoryの`AGENTS.md`がPhase 2実装を許可している。
- ユーザーからUnity実装開始の明示許可がある。

上記を満たすまで、`Repository Gate: Blocked`および`Unity Implementation: Prohibited`を維持する。

Gate確認後も、次のいずれかに該当した場合はUnity実装を停止して報告する。

- Unity Repository HEADが基準値から変わりFile Planと競合する。
- Phase 1 Grid、Time、Input、Displayの公開状態ではPhase 2接続が成立しない。
- Existing Scene、Input Actions、asmdef、namespaceとFile Planが競合する。
- 新規Package、外部Asset、Project Settings変更が必要になる。
- Phase 1 Scene／Scriptsの責務変更が必要になる。
- 動的再探索、Task、Reservation、Resident FSM、道路効果が必要になる。
- 最小総コストまたは再現性を満たせない。
- Acceptance Criteriaを検証できる環境がない。
- 解消不能なConsole Error、新規Warning、著しい負荷が発生する。

## 18. Open Decisions

### 18.1 Resolved Decisions

| ID | 解決内容 | 状態 |
|---|---|---|
| OD-01 | Phase 1のGrid、Time、Start／Destination指定を再利用 | Resolved |
| OD-02 | §6の最小File Plan、Scene、namespace、asmdef方針 | Resolved |
| OD-03 | Place `P`、Start Movement `Enter`、Reset `R` | Resolved |
| OD-04 | Blocked、Resident、PathのPhase 2専用簡易表示 | Resolved |
| OD-05 | §8の既定Blocked配置と検証座標 | Resolved |
| OD-06 | A*のNeighbor順とTie-breakを固定して再現性を確保 | Resolved |
| OD-07 | Base SpeedをSerialized `1 Cell/秒`とする | Resolved |
| OD-08 | EditMode中心、必要時だけPlayMode Test | Resolved |
| OD-09 | Human Verificationを正式Completion Evidenceとする | Resolved |
| OD-10 | 実装許可後もDraft PR作成で停止 | Resolved |

### 18.2 Remaining Open Decisions

なし。

Unity`AGENTS.md`のPhase 2禁止は設計上のOpen Decisionではなく、実装開始前に解消必須のRepository Gateである。

## 19. Approval Readiness

- Blocking Open Decisions: なし
- Repository Gate: Blocked（Unity`AGENTS.md`がPhase 2を禁止）
- Handoff Review: Completed
- Handoff Approval: Approved
- Codex Prompt Preparation: Prohibited
- Unity Implementation: Prohibited

本HandoffはSystem Spec v1.0をImplementationレベルへ展開し、レビュー・承認済みである。Unity実装には、Repository Gate解消とユーザーの明示的なUnity実装許可が別途必要である。

## 20. Repository Rules

Unity実装時は次を必須参照順として扱う。

1. Approved Phase 2 System Spec
2. Approved PDD
3. Approved GDD
4. Approved Phase 2 Implementation Handoff
5. Unity Repositoryの`AGENTS.md`
6. Roadmap

- 作業開始前に基準HEADからfeature branchを作成する。
- `main`へ直接Commitしない。
- 指定File PlanだけをStageし、Unity生成物を含めない。
- `Assets`、`Packages`、`ProjectSettings`以外の生成フォルダをCommitしない。
- 既存`.meta`を削除・再生成せず、新規Assetの`.meta`を含める。
- Package追加・削除・Version変更を行わない。
- Draft PRを作成し、Ready化、Merge、Auto Mergeを行わない。

## 21. Approval Gate

- 本書は`Approved`、`Version: 1.0`、`Approved: 2026-08-04`、`Implementation Use: Permitted`である。ただし、`Unity Implementation: Prohibited`であり、Unity実装へは使用できない。
- Handoff ReviewでSystem SpecとのTraceability、責務分離、File Plan、入力、検証、Scopeを確認済みである。
- Version、Approved日、Implementation Useは正式な承認状態へ更新済みである。
- Approved後もUnity`AGENTS.md`のPhase 2禁止を解消する。
- Unity Repository HEADを再取得し、Repository FindingsとFile Planを再監査する。
- ユーザーの明示的なUnity実装開始指示を別途受ける。
- 上記がすべて満たされるまでUnity実装を開始しない。

## 22. Phase 3 Protection

Phase 2完了時に、System Spec §18の次だけを引き渡す。

- Resident Placement Stateと配置済みResidentのCurrent Cell／World Position
- Traversability、8方向Neighbor、角抜け禁止
- A*のSuccess／Failure、順序付きPath、Cost
- Base Speed、Cell中心Movement、滑らかなWorld表示
- Paused／Normal／Fast統合
- Idle／Moving／Succeeded／FailedとFailure Reason
- Phase 2 Acceptance確認結果

Task生成／取得／完了、Resident FSM、Reservation、複数住民、AIによるDestination選択、仕事失敗後の復帰、TaskとMovementの接続を先行実装しない。Phase 3のSystem SpecとHandoffが承認されるまで拡張点、Interface、仮Stateを追加しない。

## 23. Console Completion Standard

- Console Error: `0`
- 未処理例外: `0`
- Phase 2変更により新規発生したWarning: 原則`0`
- 無限ログまたは継続的な警告出力: `0`
- 既存Warningは件数と内容を実装前後で記録し、Phase 2で増加しておらず機能を阻害しない場合だけCompletion Evidenceへ明記する。
- 既存Warningの解消にScope外変更が必要な場合は修正せず停止する。

## 24. Codex Delivery and Draft PR Gate

明示的なUnity実装開始許可を受け、すべてのGateを満たしたCodexは次まで実施する。

1. Phase 2実装
2. 自動テスト
3. Human Verification
4. Completion Evidence整理
5. Unity実装Commit
6. 許可された場合のPush
7. Draft PR作成

Draft PR作成後に停止する。次を行わない。

- Draft解除
- Ready for Review化
- PR Merge
- `main`への直接反映
- Auto Merge設定
- Human Verificationを省略した完了宣言
- Phase 3以降の実装

