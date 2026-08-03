# Project IYASAKA — Prototype 01 Phase 2 Pathfinding and Movement System Spec

Status: Draft  
Version: 0.1  
Prototype: Prototype 01  
Phase: Phase 2  
Approved: Pending  
Implementation Use: Prohibited  
Unity Implementation: Prohibited  
Last Updated: 2026-08-03  
Owner: Project IYASAKA  
Single Source of Truth: GitHub  

## 1. Purpose

本書は、Prototype 01 Phase 2「住民移動」で必要となる内部挙動、責務、状態、概念データ、例外処理、検証条件を定義するDraft System Specである。

Phase 2の目的は、住民1人がグリッド上の目的地へ移動することにより、町に初めて生命感を与えることを検証することである。

本書は、その検証に必要な移動基盤を構築するための内部仕様を定義する。

- 住民1人の配置と現在セルの管理
- 開始セルおよび目的セルの管理
- 通行可能セルと固定地形として配置された通行不能セルの判定
- A*による8方向グリッド経路探索
- Cell中心を結ぶ経路の取得
- 経路に沿った住民移動
- World座標上での滑らかな表示移動
- 移動成功および到達不能の検出
- 経路探索とMovementの責務分離
- Phase 2の検証に必要な最低限の状態表示

本書は、承認済みPDDのPhase 2を内部仕様へ展開する。PDDのScope、Out of Scope、Phase構造、完了条件は変更しない。

本書で扱う開始セルと目的セルの指定は、Phase 2の移動基盤を検証するための操作である。プレイヤーが完成版で住民を直接移動させるゲーム仕様を定義するものではない。

本書はDraftであり、Unity実装開始を許可しない。Phase 2のUnity実装には、本書のレビューと承認、および承認済みImplementation Handoffが必要である。

## 2. Source of Truth and Traceability

本書は次の正式文書を参照する。

- [GDD v1.0](../../01-gdd/gdd.md)
- [Prototype 01 PDD v1.0](../../02-prototypes/prototype-01/pdd.md)
- [Prototype 01 README](../../02-prototypes/prototype-01/README.md)
- [Prototype 01 Roadmap](../../02-prototypes/prototype-01/roadmap.md)
- [Prototype 01 Open Questions](../../02-prototypes/prototype-01/open-questions.md)
- [System Specs README](../README.md)
- [Decision Log](../../04-records/decision-log.md)
- [Changelog](../../04-records/changelog.md)

Prototype 01 PDD v1.0のPhase 2が、本書の上位仕様である。

文書間で矛盾がある場合は、実装判断で補完せず、PDDまたは仕様監査へ戻して確認する。

## 3. Scope

本書が定義する対象は次のとおり。

- 住民1人の配置と現在セルの管理
- Phase 2検証用の開始セルと目的セルの管理
- 固定地形として配置する通行不能セル
- 通行可能セルと通行不能セルの判定
- A*による8方向グリッド経路探索
- 斜め移動における角抜けの禁止
- Cell中心を結ぶ経路の取得
- 経路探索成功および到達不能の検出
- 経路に沿った住民移動
- Cell中心への到達と現在セルの更新
- World座標上での滑らかな表示移動
- 初期値を1 Cell/秒とする変更可能な基準移動速度
- Phase 1のSimulation Time Controlに従う住民移動
- 移動成功および移動失敗の管理
- PathfindingとMovementの責務分離
- Phase 2の検証に必要な最低限の状態表示
- Phase 2の初期化順序
- Phase 2の概念的なRuntime Flow
- Phase 2の例外・境界条件
- Phase 2のAcceptance CriteriaとVerification Plan

## 4. Out of Scope

次はPhase 2および本書の対象外とする。

- 複数住民
- 住民同士の競合
- 住民同士の物理衝突
- セル占有
- 住民同士の経路回避
- 渋滞、順番待ち、押し出し
- Task System
- Reservation System
- Resident FSM
- Gather
- Haul
- Build
- 木、木材、倉庫、建設予定地、建物
- 道路
- 道路による経路コスト補正
- 道路による移動速度補正
- 動的障害物
- 移動中の動的再経路探索
- 経路予約
- 高度な経路最適化
- 階層型経路探索
- 地形ごとの複数の移動コスト
- 高低差
- 水上移動、飛行移動
- 住民の向き、歩行アニメーション、完成版アート
- 住民の生活行動
- 住民の仕事、欲求、優先順位、意思決定
- Ambient Behavior
- 談笑、犬の散歩、子供の遊び
- 完成版向けAI
- 完成版の住民直接操作
- 完成版UI
- セーブ・ロード
- 完成版向けの汎用Pathfinding Framework
- 完成版向けの汎用Movement Framework
- Phase 3以降の先行実装仕様
- Unityの具体的なクラス構成
- テストコードの作成

将来利用を想定した過剰な抽象化や、Phase 3以降の要求を追加しない。

## 5. Design Principles

- Prototype検証に必要な最小構成を優先する。
- 対象は住民1人に限定する。
- グリッド座標を経路判断と論理位置の基準とする。
- 住民はCell中心間を移動する。
- 論理的なCell移動とWorld座標上の表示移動を区別する。
- PathfindingとMovementの責務を分離する。
- 通行不能セルは、画面上で識別可能な固定地形として扱う。
- 8方向移動では斜め方向の角抜けを許可しない。
- 道路効果や地形別移動コストを先取りしない。
- 基準移動速度は変更可能な設定値として扱う。
- Simulation Timeと表示上のCamera操作を混同しない。
- 無効な入力や設定を黙って有効値へ変換しない。
- 到達不能や移動失敗を停止状態のまま隠さず、確認可能な結果として扱う。
- 検証可能性と原因追跡のしやすさを優先する。
- 開始セルと目的セルの指定を完成版の直接命令へ拡張しない。
- 後で置き換え可能な単純な構造を使用する。

## 6. System Boundary

Phase 2の責務は次の6領域へ分ける。名称は責務を示すものであり、Unityのクラス名を確定するものではない。

| 領域 | 責務 | 主な入力 | 主な出力・保持状態 | 依存関係 | Phase 2で扱わないこと |
|---|---|---|---|---|---|
| Traversability | 有効セルと固定地形に基づき、通行可能・通行不能を判定する | Grid、通行不能セル設定、対象セル | 通行可否 | Phase 1 Grid | 動的障害物、占有、道路効果、地形別コスト |
| Resident Position | 住民1人の現在セルと表示位置を管理する | 初期セル、Cell到達通知 | Current Cell、World Position | Grid、Coordinate Conversion | 複数住民、住民AI、仕事、欲求 |
| Pathfinding | 開始セルから目的セルまでの経路を探索する | 開始セル、目的セル、通行可否 | Pathfinding Result、経路、総コスト、失敗理由 | Grid、Traversability | World座標移動、時間更新、再経路探索 |
| Movement | 取得済み経路に沿って住民を移動させる | 経路、基準移動速度、Simulation Time | Movement Status、現在区間、表示位置、移動結果 | Resident Position、Coordinate Conversion、Simulation Time | 経路探索、目的選択、仕事判断 |
| Validation Control | Phase 2検証用の開始セル、目的セル、実行要求、リセットを管理する | 検証操作 | 指定セル、要求結果 | Traversability、Resident Position、Pathfinding、Movement | 完成版の直接移動命令、Task System |
| Debug / Validation Display | Phase 2の状態と結果を確認可能にする | 上記各領域の読み取り値 | 画面上または検証用表示 | 全領域 | 完成版UI、通知、住民情報画面 |

各領域は、自身の責務外の状態を直接変更しない。

- TraversabilityはGrid Configurationを変更しない。
- Resident Positionは経路を探索しない。
- Pathfindingは住民のWorld座標、現在セル、Movement Statusを変更しない。
- PathfindingはSimulation Timeを参照しない。
- Movementは通行可能セルの探索や代替経路の生成を行わない。
- Movementは開始セルまたは目的セルを選択しない。
- Validation Controlは住民の自律判断を定義しない。
- Debug / Validation Displayは、Phase 2検証用操作を除き、各領域の状態を変更しない。
- Simulation Time Control、Camera ControlおよびGridの既存責務をPhase 2側へ移さない。

## 7. Traversability Model

### 7.1 Cell Traversability

有効セルは、次のいずれかとして扱う。

| 状態 | 意味 |
|---|---|
| Traversable | 住民が経路として使用できる |
| Blocked | 固定地形により住民が進入できない |

範囲外セルはBlockedへ変換せず、無効セルとして扱う。

セルがTraversableである条件は次のとおり。

```text
Grid上の有効セルである
かつ
固定地形によるBlocked Cellではない
```

- 有効セルは、Blockedとして指定されていない限りTraversableとする。
- Blocked Cellへ住民を配置しない。
- Blocked Cellを経路へ含めない。
- 無効セルをTraversableとして扱わない。
- 通行可否判定は同じ入力に対して同じ結果を返す。
- 通行可否判定は住民の位置、Movement Status、Simulation Timeに依存しない。
- Blocked Cell設定に無効セルが1件でも含まれる場合は設定異常として扱い、Traversabilityを利用可能状態にしない。
- 無効な項目だけを無視して続行しない。
- 無効なBlocked Cell設定に対してSilent Recoveryまたは自動修復を行わない。

### 7.2 Fixed Terrain

Phase 2の通行不能セルは、意味を持つ固定地形として配置する。

- Blocked Cellは画面上でTraversable Cellと識別できなければならない。
- Blocked Cellは経路探索開始後に動的変化しない。
- 固定地形の具体的な外観と配置は、Phase 2の検証条件を満たす範囲でImplementation Handoffに委ねる。
- 固定地形には、迂回可能な配置と到達不能を発生させる配置の両方を用意できなければならない。
- 固定地形の種類ごとの異なる移動コストは設けない。
- 固定地形の破壊、建設、生成、消失は扱わない。

### 7.3 Start and Destination Validation

開始セルと目的セルは、次の条件をすべて満たす場合だけ有効とする。

- Grid上の有効セルである。
- Traversableである。
- Phase 2の検証操作によって確定されている。

開始セルまたは目的セルが無効、範囲外、Blocked、未指定の場合は、経路探索を開始しない。

### 7.4 Neighbor Directions

Pathfindingは、現在セルに隣接する次の8方向を候補とする。

```text
上
下
左
右
左上
右上
左下
右下
```

- 上下左右をOrthogonal Moveとする。
- 斜め4方向をDiagonal Moveとする。
- 候補セルが無効またはBlockedの場合、その方向へ移動できない。

### 7.5 Diagonal Corner Rule

Diagonal Moveでは角抜けを禁止する。

セル`(x, y)`から`(x + dx, y + dy)`へ斜め移動する場合、次のすべてを満たさなければならない。

```text
dxは-1または1
dyは-1または1
移動先セルがTraversable
セル(x + dx, y)がTraversable
セル(x, y + dy)がTraversable
```

斜め移動先がTraversableであっても、隣接する2つのOrthogonal Cellのどちらか一方でも無効またはBlockedの場合、そのDiagonal Moveを許可しない。

## 8. Pathfinding

### 8.1 Responsibilities

Pathfindingは次を担当する。

- 開始セルと目的セルの検証
- Traversabilityに基づく隣接セルの取得
- A*による経路探索
- 8方向移動
- 斜め移動の角抜け禁止
- 探索成功と到達不能の判定
- Cell中心を結ぶ順序付き経路の返却
- 経路総コストの返却
- 失敗理由の返却

Pathfindingは次を担当しない。

- 住民の移動
- World座標の補間
- Simulation Timeによる更新
- 住民の目的地選択
- 移動中の再経路探索
- 道路効果
- 住民または他オブジェクトによる動的回避

### 8.2 Movement Cost

移動コストは次のとおり。

| 移動 | コスト |
|---|---:|
| Orthogonal Move | 1 |
| Diagonal Move | √2 |

- すべてのTraversable Cellは同じ地形コストを持つ。
- 道路によるコスト補正を適用しない。
- Blocked Cellへ移動するコストは定義せず、探索候補に含めない。
- 経路総コストは各移動区間のコスト合計とする。

### 8.3 Heuristic

8方向移動と移動コストに整合するOctile DistanceをHeuristicとして使用する。

開始側の評価対象セルと目的セルのX差、Y差の絶対値を`dx`、`dy`とする。

```text
heuristic = max(dx, dy) + (√2 - 1) * min(dx, dy)
```

- HeuristicはBlocked Cellの配置を無視した推定値とする。
- Heuristicは実際の最小コストを過大評価しない。
- Heuristicへ道路効果、住民状態、World座標距離を混入しない。

### 8.4 Path Search

A*は概念上、次の情報を使用する。

- Open Set
- Closed Set
- 各セルまでの既知最小コスト
- 各セルの推定総コスト
- 経路復元用の直前セル

探索の必須挙動は次のとおり。

1. 開始セルをOpen Setへ追加する。
2. Open Setから推定総コストが最小のセルを選択する。
3. 選択セルが目的セルなら経路を復元して成功を返す。
4. 選択セルをClosed Setへ移す。
5. 移動可能な隣接セルを評価する。
6. より小さい既知コストが得られた場合は、コストと直前セルを更新する。
7. Open Setが空になるまで繰り返す。
8. 目的セルへ到達せずOpen Setが空になった場合は到達不能を返す。

到達可能な場合、A*は定義された移動コストに基づく最小総コスト経路の一つを成功結果として返す。

最小総コストが同一の経路が複数存在する場合も、同じ入力と同じTraversabilityから再現可能な結果を返す。

同じ入力と同じTraversabilityに対して、結果が非決定的に変化しないようにする。ただし、同一コストの複数経路が存在する場合の具体的な優先方向は、最短コストと再現性を維持する範囲で実装判断に委ねる。

### 8.5 Path Result

経路探索成功時は、次を返す。

- Success
- 開始セルから目的セルまでの順序付きCell Path
- 経路総コスト
- 失敗理由なし

Cell Pathは開始セルと目的セルを含む。

隣接するPath Cell間は、必ず次を満たす。

- X差とY差がそれぞれ-1、0、1の範囲である。
- 同一セルの重複区間ではない。
- Orthogonal Moveまたは許可されたDiagonal Moveである。
- 移動先がTraversableである。
- Diagonal Moveの場合は角抜け条件を満たす。

### 8.6 Path Failure

次の場合は経路探索失敗を返す。

| 結果 | 条件 |
|---|---|
| Invalid Start | 開始セルが未指定、無効、範囲外またはBlocked |
| Invalid Destination | 目的セルが未指定、無効、範囲外またはBlocked |
| Unreachable | 入力セルは有効だが、目的セルへ到達する経路が存在しない |
| Unavailable | 必要なGridまたはTraversabilityが利用可能でない |

失敗時は次を満たす。

- 部分経路を成功結果として返さない。
- 住民移動を開始しない。
- 住民の現在セルと表示位置を変更しない。
- 最後の有効な経路を今回の成功経路として再利用しない。
- 失敗理由を検証表示またはログで確認可能にする。

### 8.7 Same-Cell Path

開始セルと目的セルが同一のTraversable Cellである場合は、経路探索成功とする。

- Cell Pathはその1セルだけを含む。
- 経路総コストは0とする。
- Movementは移動区間を生成しない。
- 移動結果は即時にSucceededとする。
- 住民の表示位置を当該Cell中心に維持する。

## 9. Resident Movement

### 9.1 Resident Position

Phase 2では住民1人だけを管理する。

Phase 2では、住民の未配置状態を許容する。

- 未配置中の住民はCurrent Cellを持たない。
- 未配置中の住民は移動要求を開始できない。
- 住民は有効なStart Cellへ配置された時点で利用可能になる。

配置済みの住民の論理位置はCurrent Cellで表す。

- 初期配置時、住民を有効なStart Cellの中心へ配置する。
- Current Cellは、住民が最後に到達したCell中心を表す。
- Cell間を表示移動している間は、次のCell中心へ到達するまでCurrent Cellを変更しない。
- 次のCell中心へ到達した時点でCurrent Cellを更新する。
- World Positionは、Current Cellまたは移動中の補間位置を表す。
- 移動していない状態では、World PositionとCurrent Cellの中心を一致させる。

### 9.2 Movement Status

Movementは次の状態を持つ。

| 状態 | 意味 |
|---|---|
| Idle | 有効な移動要求を実行していない |
| Moving | 取得済み経路に沿って移動中 |
| Succeeded | 目的セルへ到達した |
| Failed | 移動要求または経路取得が失敗した |

これらはPhase 2のMovement状態であり、Resident FSMを定義するものではない。

### 9.3 Movement Request

移動要求は、Phase 2検証用の操作として次の順序で扱う。

1. 住民が有効なStart Cellへ配置済みであることを確認する。
2. Current Cellを経路探索の開始セルとする。
3. 指定されたDestination Cellを検証する。
4. Pathfindingへ経路探索を要求する。
5. 探索成功時は取得済みPathをMovementへ渡す。
6. 探索失敗時はMovement StatusをFailedとし、移動を開始しない。
7. 同一セル経路の場合はMovement StatusをSucceededとする。
8. 移動区間がある場合はMovement StatusをMovingとする。

Moving中に新しい移動要求、Start Cell再配置要求またはリセット要求を受けた場合は拒否し、現在の移動を継続する。Phase 2では移動の取消、中断、目的地変更、再経路探索を定義しない。

### 9.4 Start Cell Placement

Start Cell指定はPhase 2の検証用初期配置または再検証用配置に使用する。

- Start Cellが有効かつTraversableである場合だけ住民を配置できる。
- Start Cellへ配置した時点でCurrent CellをStart Cellへ更新する。
- World PositionをStart Cell中心へ設定する。
- Movement StatusをIdleとする。
- 現在のPathと移動区間状態を消去する。
- Moving中はStart Cellによる再配置を行わない。
- この操作は完成版の住民直接移動命令を定義しない。

### 9.5 Base Movement Speed

基準移動速度は、Normal状態で1秒あたりに移動するCell距離として扱う。

- 初期値は`1 Cell/秒`とする。
- 0より大きい有限値でなければならない。
- 検証目的で変更可能な設定値とする。
- 完成版の移動速度として固定しない。
- 道路、住民能力、地形種別による補正を適用しない。

World座標上の基準速度は概念上、次の関係とする。

```text
worldSpeed = baseMovementSpeed * Cell Size
```

これにより、基準移動速度が`1 Cell/秒`の場合は次のようになる。

- Orthogonal Move 1区間の所要時間は1秒。
- Diagonal Move 1区間の距離は`√2 Cell`であり、所要時間は`√2秒`。
- Orthogonal MoveとDiagonal MoveでWorld座標上の移動速度を一定に保つ。

### 9.6 Smooth World Movement

住民は、Cell Path上のCell中心間をWorld座標上で滑らかに移動する。

- 移動区間の始点と終点はCell中心とする。
- 区間内では一定速度で終点へ近づく。
- 表示位置をCell単位で瞬間移動させない。
- 終点を通り過ぎた場合は終点へ正確に確定する。
- 1回の更新量が複数区間分を超える場合は、未消費の移動量を次の区間へ適用する。
- フレームレート差によって経路から外れない。
- 目的セル到達時のWorld Positionを目的Cell中心へ一致させる。
- 表示上の滑らかさのために、Current Cell、Path、Traversabilityを変更しない。

### 9.7 Path Consumption

MovementはPathfindingから受け取った順序付きCell Pathを使用する。

- Path先頭がCurrent Cellと一致することを確認する。
- Path先頭のCurrent Cellは移動対象区間として重複処理しない。
- 次のPath Cell中心を現在の移動目標とする。
- Cell中心へ到達するたびにCurrent Cellを更新する。
- 次のPath Cellが存在する場合は次区間へ進む。
- Destination Cell中心へ到達した場合はSucceededとする。
- MovementはPathの途中へ独自のCellを追加しない。
- MovementはPathを短縮、再探索または最適化しない。

### 9.8 Movement Success and Failure

移動成功は、次の条件をすべて満たした時点で成立する。

- Current CellがDestination Cellと一致する。
- World PositionがDestination Cell中心と一致する。
- 未処理のPath区間が存在しない。

移動失敗は、次の場合に成立する。

- Pathfindingが失敗した。
- Movement開始時にPathが空または不正である。
- Path先頭がCurrent Cellと一致しない。
- Path内に無効な移動区間、Blocked Cellまたは角抜けが含まれる。
- 必要な依存領域が利用可能でない。

移動失敗時は次を満たす。

- Movement StatusをFailedとする。
- 不正な区間へ進入しない。
- 最後に正常到達したCurrent Cellを維持する。
- World Positionを最後に正常到達したCurrent Cell中心へ一致させる。
- 失敗理由を確認可能にする。
- 動的再経路探索を開始しない。

## 10. Simulation Time Integration

### 10.1 Time Dependency

住民移動は、Phase 1で定義されたSimulation Time Controlに従う。

| Simulation Time State | Movementの扱い |
|---|---|
| Paused | 移動距離を増加させない |
| Normal | 基準移動速度で進める |
| Fast | 基準移動速度へ現在のFast倍率を適用する |

概念上の移動量は次の関係とする。

```text
movementDistance =
baseMovementSpeed
* Cell Size
* simulationDeltaTime
```

simulationDeltaTimeは、現在のSimulation Time Stateと倍率が反映されたシミュレーション上の経過時間とする。

### 10.2 Paused Behavior

Paused中は次を維持する。

- Current Cellを変更しない。
- World Positionを変更しない。
- 現在のPathを維持する。
- 現在のPath区間を維持する。
- Movement StatusをMovingのまま維持できる。
- Resume後は停止前の区間から移動を継続する。

Paused中も次は使用可能とする。

- Camera移動
- Camera Zoom
- Debug / Validation Display
- 現在状態の確認

Paused中に住民移動だけを完了扱いにしない。

### 10.3 Normal and Fast Behavior

- Normalでは基準移動速度に従う。
- FastではPhase 1のFast倍率に従って移動を高速化する。
- FastからNormalへ変更してもPath、Current Cell、区間進行状態を失わない。
- NormalまたはFastからPausedへ変更してもPathを失わない。
- 時間状態変更によって住民を次のCell中心へ瞬間移動させない。
- 時間状態変更によって移動距離をリセット、逆行または重複加算しない。
- Camera操作速度は住民のSimulation Time倍率に連動させない。

### 10.4 Presentation Separation

- World座標上の住民表示はSimulation Timeに従う。
- Camera、Verification Overlayおよび検証表示の読み取りはPaused中も機能する。
- 表示補間を理由にSimulation Time Controlの状態や倍率を変更しない。
- MovementはSimulation Time Controlの状態を直接変更しない。

## 11. Debug and Validation Display

### 11.1 Purpose

Phase 2の内部状態を人間が確認し、通行可否、経路探索、住民移動、時間制御および失敗原因を追跡できるようにする。

完成版UIとして設計しない。

### 11.2 Required Information

最低限、次を確認できるようにする。

- Traversable CellとBlocked Cell
- Start Cell
- Destination Cell
- 住民の配置状態（未配置または配置済み）
- 配置済みの場合の住民のCurrent Cell
- 住民のWorld Position
- 住民の現在の移動目標Cell
- 取得済みPath
- Path上のCell順序または移動方向
- Pathfinding Result
- Path総コスト
- Movement Status
- 基準移動速度
- 現在のSimulation Time State
- 現在のTime Multiplier
- 最後の移動成功または失敗結果
- 到達不能を含む失敗理由
- Invalid操作結果

### 11.3 Game View Visibility

Phase 2のGame Viewでは、最低限次を互いに識別できるようにする。

- Traversable Cell
- 固定地形として配置されたBlocked Cell
- Start Cell
- Destination Cell
- 住民
- 取得済みPath
- 住民のCurrent Cellまたは現在の移動目標

住民のWorld座標上の移動をCamera移動およびZoomの異なる状態で追跡できなければならない。

Phase 1で定義された常時表示対象とVerification Overlayの責務分離を維持する。

- Grid外周、セル境界、Start Cell、Destination Cellは常時表示対象とする。
- Phase 2の検証に必要な固定地形、住民および取得済みPathもGame Viewで確認可能にする。
- Verification Overlayの表示切替によって住民移動またはPathfindingの状態を変更しない。
- Verification Overlayを非表示にしても、Phase 2の移動結果をGame View上で観察できる状態を維持する。

### 11.4 Validation Interaction

Phase 2では検証用として次の操作を可能にする。

- 有効なStart Cellの指定
- 有効なDestination Cellの指定
- 住民のStart Cellへの配置
- 経路探索および移動の開始
- 移動していない状態での再検証用リセット
- Verification Overlayの表示切替

- 無効セルまたはBlocked CellをStart Cell、Destination Cellとして確定しない。
- Moving中に再配置、リセットまたは新しい移動を開始しない。
- 操作の具体的な入力割り当てはImplementation Handoffで決定する。
- 検証操作を完成版の住民直接操作として再利用しない。

## 12. State and Data

次は概念データであり、Unityのクラス、コンポーネント、アセット形式、シリアライズ方式を確定しない。

| データ | 内容 | 更新主体 |
|---|---|---|
| Blocked Cells | 固定地形により通行不能なCellの集合 | 初期化時の設定 |
| Resident Placement State | 住民が未配置または配置済みであることを示す状態 | Resident Position |
| Resident Current Cell | 配置済みの住民が最後に到達したCell中心。未配置中は値を持たない | Resident Position |
| Resident World Position | 住民の現在のWorld座標 | Movement |
| Base Movement Speed | Normalでの基準移動速度。初期値1 Cell/秒 | 初期化時の設定 |
| Selected Start Cell | Phase 2検証用の開始セル。未指定を許容 | Validation Control |
| Selected Destination Cell | Phase 2検証用の目的セル。未指定を許容 | Validation Control |
| Pathfinding Status | 未実行、成功、失敗 | Pathfinding |
| Pathfinding Failure Reason | Invalid Start、Invalid Destination、Unreachable、Unavailable | Pathfinding |
| Current Path | 開始セルから目的セルまでの順序付きCell Path | Pathfinding |
| Current Path Cost | Current Pathの移動コスト合計 | Pathfinding |
| Current Path Index | 現在到達済みまたは次に処理するPath位置 | Movement |
| Current Target Cell | 現在移動中の区間終点Cell | Movement |
| Movement Status | Idle、Moving、Succeeded、Failed | Movement |
| Movement Failure Reason | 最後の移動失敗理由 | Movement |
| Last Movement Result | 最後の移動要求の成功または失敗結果 | Validation Control |
| Current Time State | Phase 1のPaused、Normal、Fast | Simulation Time Control |
| Current Time Multiplier | 現在状態に対応する倍率 | Simulation Time Control |
| Last Validation Result | 最後の検証操作と成功・失敗 | Debug / Validation Display |

Phase 2で次のデータは持たない。

- 複数住民の一覧
- セル占有
- 住民間Reservation
- Task
- Resident FSM状態
- 住民の欲求、仕事、優先順位
- 道路状態
- 道路コスト
- 道路速度補正
- 動的障害物
- 経路予約
- Gather、Haul、Buildの状態
- Ambient Behaviorの状態

## 13. Error and Edge Cases

| ケース | 必須の扱い |
|---|---|
| Blocked Cell設定に無効セルが1件でも含まれている | 設定異常として確認可能にし、Traversabilityを利用可能状態にしない。無効な項目だけを無視して続行せず、Silent Recoveryまたは自動修復を行わない |
| 住民が未配置 | Current Cellを持たず、移動要求を開始しない |
| Start Cellが未指定 | 経路探索と移動を開始せず、Invalid Startを返す |
| Start Cellが範囲外またはBlocked | 配置と経路探索を行わず、Invalid Startを返す |
| Destination Cellが未指定 | 経路探索と移動を開始せず、Invalid Destinationを返す |
| Destination Cellが範囲外またはBlocked | 経路探索と移動を行わず、Invalid Destinationを返す |
| Start CellとDestination Cellが同一 | コスト0の成功経路とし、移動せずSucceededとする |
| 目的セルまでの経路がない | Unreachableを返し、住民を移動させない |
| 斜め移動先だけがTraversableで隣接Orthogonal CellがBlocked | Diagonal Moveを禁止する |
| PathにBlocked Cellが含まれる | Movementを開始または継続せずFailedとする |
| Pathに隣接していないCellが含まれる | Movementを開始または継続せずFailedとする |
| Path先頭がCurrent Cellと一致しない | Movementを開始せずFailedとする |
| Pathが空 | Movementを開始せずFailedとする |
| 基準移動速度が0以下、NaN、Positive Infinity、Negative Infinity | Movement初期化を失敗とする |
| Movement中に新しい移動要求を受ける | 要求を拒否し、現在の移動を継続する |
| Movement中にStart Cell再配置またはリセット要求を受ける | 要求を拒否し、現在の移動を継続する |
| Paused中 | Pathと区間状態を維持し、移動距離を増加させない |
| 1回の更新量が区間終点を超える | 終点へ確定し、残りの移動量を次区間へ適用する |
| 必要な依存領域が未初期化 | 依存する領域を利用可能にせず、原因を確認可能にする |
| 初期化前のPathfindingまたはMovement参照 | 利用可能な結果として返さず、原因を確認可能にする |

例外時にプロセス全体を無条件で停止させない。ただし、無効なPath、MovementまたはTraversabilityを正常として継続利用しない。

エラー表示の具体的な方式は固定しない。最低限、開発中に原因を特定できるログまたは状態表示を残す。

NaN、Positive Infinity、Negative Infinityによって未定義動作、クラッシュまたは無効な内部状態への遷移を発生させない。

## 14. Initialization Order

Phase 2は次の順序で初期化する。

1. Phase 1 Grid、Coordinate Conversion、Camera Control、Simulation Time Controlが利用可能であることを確認する。
2. Blocked Cell設定を検証する。
3. Blocked Cell設定が有効な場合にTraversabilityを初期化する。設定異常がある場合はTraversabilityを利用可能状態にしない。
4. 基準移動速度を検証する。
5. Resident Positionを初期化する。
6. Pathfindingを利用可能にする。
7. Movementを初期化する。
8. Validation Controlを初期化する。
9. Debug / Validation DisplayをPhase 2状態へ接続する。
10. 有効なStart Cellが指定されている場合は住民を配置する。有効なStart Cellが指定されていない場合、住民はCurrent Cellを持たない未配置状態を維持する。
11. Phase 2全体の利用可能状態を確認する。

依存関係のない内部処理を過度に結合しない。

- TraversabilityはGrid初期化完了後に利用可能とする。
- Resident PositionはGridとCoordinate Conversionの初期化完了後に利用可能とする。
- PathfindingはGridとTraversabilityの初期化完了後に利用可能とする。
- MovementはResident Position、Coordinate ConversionおよびSimulation Time Controlの初期化完了後に利用可能とする。
- Validation ControlはPathfindingとMovementの初期化完了後に利用可能とする。
- Debug / Validation Displayは、参照対象となる領域の初期化後に利用可能とする。
- 一つの領域が失敗した場合、依存領域を正常として開始しない。

## 15. Runtime Flow

通常フレームにおける概念的な流れは次のとおり。

1. Phase 1の入力要求とSimulation Time状態を取得する。
2. Camera移動、ZoomおよびSimulation Time状態変更をPhase 1の責務で処理する。
3. Phase 2検証用のStart Cell、Destination Cell、配置、移動開始またはリセット要求を取得する。
4. Start CellまたはDestination Cellの指定要求があれば、Valid CellとTraversabilityを確認する。
5. Start Cellへの配置要求があれば、Movement中でないことを確認して住民をCell中心へ配置する。
6. 移動開始要求があれば、住民が配置済みであることを確認し、Current CellとDestination Cellを検証する。
7. PathfindingがA*による経路探索を実行する。
8. 経路探索失敗時は失敗理由を公開し、Movementを開始しない。
9. 経路探索成功時はCell Pathと総コストをMovementへ渡す。
10. 同一セル経路の場合は移動せずSucceededとする。
11. 移動区間がある場合はMovement StatusをMovingとする。
12. 現在のSimulation Time状態と倍率に従って移動量を求める。
13. 取得済みPathの次のCell中心へWorld Positionを進める。
14. Cell中心へ到達した場合はCurrent CellとPath進行位置を更新する。
15. Destination Cell中心へ到達した場合はMovement StatusをSucceededとする。
16. Pathまたは依存状態が不正な場合はMovement StatusをFailedとし、最後の正常なCell中心を維持する。
17. Pathfinding Result、Movement Status、Current Cell、World Positionおよび検証表示を更新する。

この順序は責務と依存関係を示す。Unityのイベント関数や具体的な実行順序を固定するものではない。

## 16. Acceptance Criteria

Phase 2 System Specの完了条件は次のとおり。

### Traversability and Pathfinding

- 有効セルをTraversableまたはBlockedとして安定して判定できる。
- 範囲外セルをTraversableとして扱わない。
- Blocked Cellを経路へ含めない。
- 固定地形として配置されたBlocked CellをGame Viewで識別できる。
- Blocked Cell設定に無効セルが1件でも含まれる場合、設定異常を検出し、Traversabilityを利用可能状態にしない。
- 無効な項目だけを無視して続行しない。
- 無効なBlocked Cell設定をSilent Recoveryまたは自動修復しない。
- A*により8方向のCell Pathを取得できる。
- 到達可能な場合、定義された移動コストに基づく最小総コスト経路の一つを取得できる。
- Orthogonal Moveのコストを1として扱う。
- Diagonal Moveのコストを√2として扱う。
- Diagonal Moveで隣接するOrthogonal CellのどちらかがBlockedの場合、角抜けを発生させない。
- Pathは開始セルと目的セルを含む。
- Path内の隣接Cellが有効なOrthogonal MoveまたはDiagonal Moveになっている。
- 迂回可能な障害物配置で目的セルまでの経路を取得できる。
- 到達不能な障害物配置でUnreachableを検出できる。
- 到達不能時に部分経路を成功結果として返さない。
- 同一セルを開始セルと目的セルにした場合、コスト0の成功結果を返す。
- Invalid StartとInvalid Destinationを区別して検出できる。
- 同じ入力と同じTraversabilityから再現可能な結果を取得できる。
- 最小総コストが同一の経路が複数存在する場合も、同じ入力と同じTraversabilityから再現可能な結果を取得できる。
- 道路効果または地形別移動コストを適用しない。

### Resident Movement

- 有効なStart Cellへ配置されていない住民を未配置状態として扱える。
- 未配置中の住民がCurrent Cellを持たない。
- 未配置中の住民が移動要求を開始できない。
- 有効なStart Cellへ配置した時点で住民を利用可能にできる。
- Debug / Validation Displayで住民の未配置状態を確認できる。
- 住民1人を有効なStart Cell中心へ配置できる。
- 住民のCurrent CellとWorld Positionを確認できる。
- 取得済みPathに沿って住民が移動できる。
- 住民がCell中心間を移動する。
- World座標上で住民が滑らかに表示移動する。
- Cell間移動中はCurrent Cellを最後に到達したCellとして維持する。
- 次のCell中心へ到達した時点でCurrent Cellを更新する。
- 目的セル到達時にCurrent CellとWorld Positionが目的Cell中心へ一致する。
- 基準移動速度の初期値を1 Cell/秒として扱う。
- Normalかつ1 Cell/秒の場合、Orthogonal Move 1区間を1秒で移動する。
- Normalかつ1 Cell/秒の場合、Diagonal Move 1区間を√2秒で移動する。
- Orthogonal MoveとDiagonal MoveでWorld座標上の移動速度が一定になる。
- 更新量が区間終点を超えてもPathから外れず、未消費の移動量を次区間へ適用できる。
- 移動成功をSucceededとして確認できる。
- 経路探索失敗または不正PathをFailedとして確認できる。
- 失敗時にBlocked Cellまたは不正区間へ進入しない。
- Moving中の新しい移動、再配置またはリセット要求を拒否できる。
- PathfindingとMovementの責務が混在していない。

### Simulation Time

- Paused中は住民のCurrent CellとWorld Positionが変化しない。
- Paused中も現在のPathと移動区間状態を維持する。
- Resume後は停止前の区間から移動を継続する。
- Normal中は基準移動速度で移動する。
- Fast中はPhase 1のFast倍率に従って移動する。
- 時間状態変更によって移動距離をリセット、逆行または重複加算しない。
- Paused中もCamera移動、Zoomおよび検証表示が機能する。
- Simulation Time倍率によってCamera操作速度が変化しない。

### Debug and Phase 3 Preparation

- Start Cell、Destination Cell、Blocked Cell、住民およびPathをGame Viewで識別できる。
- 住民のCurrent CellとWorld Positionを確認できる。
- Pathfinding Result、Path総コスト、Movement Statusを確認できる。
- Invalid Start、Invalid Destination、UnreachableおよびMovement Failureの理由を確認できる。
- Verification Overlayを非表示にしても住民移動、固定地形およびPathをGame View上で観察できる。
- Camera移動とZoomの異なる状態で住民移動を追跡できる。
- 到達可能、迂回、角抜け禁止、到達不能および同一セルの各条件を再現できる。
- 住民1人以外を生成または管理しない。
- Task System、Reservation System、Resident FSMが混入していない。
- Gather、Haul、Build、道路効果、動的再経路探索が混入していない。
- 開始セルと目的セルの指定が完成版の直接移動命令として扱われていない。

各項目は、後続のUnity実装後に人間が確認可能な表現とする。本書のDraft作成時点ではUnity実装および動作確認を行わない。

## 17. Verification Plan

本節は、後続のImplementation HandoffおよびUnity実装時に行う確認方法を定義する。今回はテストコードを作成しない。

### 17.1 Automated Verification Candidates

後続工程で自動確認の候補とする項目は次のとおり。

- 有効セル、範囲外セル、Traversable Cell、Blocked Cellの判定
- 無効なBlocked Cell設定を1件でも含む場合にTraversabilityが利用可能状態にならないこと
- 無効なBlocked Cell設定のうち無効な項目だけを無視して続行しないこと
- 無効なBlocked Cell設定がSilent Recoveryまたは自動修復されないこと
- Start CellおよびDestination Cellの有効性判定
- Blocked Cellを含まないPathの生成
- 上下左右4方向の隣接Cell取得
- 斜め4方向の隣接Cell取得
- Orthogonal Moveのコスト1
- Diagonal Moveのコスト√2
- Octile Distanceの計算
- 開けたGridでの直線経路
- 開けたGridでのDiagonal Path
- 固定地形を迂回する経路
- 両側のOrthogonal CellがTraversableな場合のDiagonal Move
- 一方のOrthogonal CellがBlockedな場合の角抜け禁止
- 両方のOrthogonal CellがBlockedな場合の角抜け禁止
- 無効セルまたはGrid外周を越えるDiagonal Moveの禁止
- 到達不能時のUnreachable
- 到達不能時に部分経路を成功として返さないこと
- Invalid Start
- Invalid Destination
- Start CellとDestination Cellが同一の場合のコスト0経路
- Pathが開始セルと目的セルを含むこと
- Path内の全区間が隣接し、Traversableであること
- Path総コストと区間コスト合計の一致
- 到達可能な場合にPath総コストが最小総コストとなること
- 同じ入力に対する再現可能なPathfinding Result
- 最小総コストが同一の経路が複数存在する場合の再現可能なPathfinding Result
- 住民未配置中にCurrent Cellを持たず、移動要求を開始できないこと
- Resident Current Cellと初期World Positionの一致
- Path先頭とCurrent Cellの一致確認
- Cell中心到達時のCurrent Cell更新
- Cell間移動中のCurrent Cell維持
- 目的セル到達時のSucceeded
- Pathfinding失敗時のFailed
- 空Path、非隣接Cell、Blocked Cell、角抜けを含む不正Pathの拒否
- 不正Path拒否後の最後の正常なCurrent Cell維持
- 基準移動速度1 Cell/秒でのOrthogonal Move所要時間
- 基準移動速度1 Cell/秒でのDiagonal Move所要時間
- 異なるCell SizeでのCell単位移動速度の一致
- 1回の更新量が複数区間を超える場合の未消費移動量処理
- Paused中の位置停止とPath状態維持
- Normal中の基準速度
- Fast中の倍率適用
- 時間状態変更後のPathと区間進行状態の維持
- Moving中の新しい移動、再配置およびリセット要求の拒否
- 0以下、NaN、Positive Infinity、Negative Infinityの基準移動速度の拒否
- 道路効果、Task、Reservation、Resident FSMへの依存がないこと

### 17.2 Manual Verification

後続工程で人間が確認する項目は次のとおり。

- Traversable Cellと固定地形としてのBlocked Cellの識別
- Start CellとDestination Cellの指定・識別
- 住民の未配置状態をDebug / Validation Displayで確認できること
- 有効なStart Cellへ配置した時点で住民が利用可能になること
- 住民1人のStart Cell中心への配置
- 住民のCurrent Cellと表示位置の一致
- 開けたGridでのOrthogonal Move
- 開けたGridでのDiagonal Move
- 固定地形を迂回する移動
- 斜め方向の角抜けが発生しないこと
- 到達不能時に住民が移動を開始しないこと
- 到達不能の理由表示
- Start CellとDestination Cellが同一の場合の即時成功
- Cell中心間の滑らかなWorld座標移動
- Cell中心到達時のCurrent Cell更新
- 目的セル中心での正確な停止
- Normalかつ1 Cell/秒でのOrthogonal Move所要時間
- Diagonal Moveで表示速度が不自然に速くならないこと
- Paused中の住民停止
- Paused中のCamera移動とZoom
- Resume後の移動継続
- NormalとFastの移動速度差
- Camera移動とZoomの異なる状態での住民追跡
- Pathfinding Result、Path、Path Cost、Movement Statusの表示
- Invalid Start、Invalid Destination、UnreachableおよびMovement Failureの表示
- Verification Overlay OFFでも住民、固定地形、Pathおよび移動結果を観察できること
- Moving中の新しい移動、再配置およびリセット要求が現在の移動へ影響しないこと
- 移動中または失敗時のConsole Error／Warningの有無
- 住民移動中に実用上問題となる著しい操作遅延または描画負荷がないこと

### 17.3 Completion Evidence

後続工程では、Human Verificationの実施記録を正式なCompletion Evidenceとして残す。自動テスト結果、Console結果、Commit履歴およびGitHub PR履歴と組み合わせ、実装完了を追跡可能にする。

#### Traversability and Pathfinding

- 固定地形をGame Viewで識別したHuman Verification結果
- 開けたGridで目的セルへ到達できた結果
- 固定地形を迂回して到達できた結果
- 角抜けを行わずに移動した結果
- 到達不能を検出した結果
- Invalid StartとInvalid Destinationを検出した結果
- 同一セル経路をコスト0の成功として扱った結果
- Path内にBlocked Cellまたは不正区間が含まれていない確認結果
- Pathfinding関連の自動テスト結果
- PathfindingによるConsole Error／Warningの有無

#### Resident Movement

- 住民1人をStart Cell中心へ配置した結果
- Orthogonal MoveのHuman Verification結果
- Diagonal MoveのHuman Verification結果
- 固定地形を迂回するWorld座標上の滑らかな移動結果
- Cell中心到達時のCurrent Cell更新結果
- 目的セル中心で停止しSucceededとなった結果
- 基準移動速度1 Cell/秒の確認結果
- Paused中に住民が停止した結果
- Resume後に移動を継続した結果
- NormalとFastの移動速度差の確認結果
- Moving中の新しい要求を拒否した結果
- Movement関連の自動テスト結果
- MovementによるConsole Error／Warning、著しい操作遅延または負荷の有無

#### Failure and Display

- 到達不能時に住民が移動しない確認結果
- 失敗後に最後の正常なCurrent Cellを維持した結果
- Pathfinding Result、Path Cost、Movement Statusおよび失敗理由の確認結果
- Verification Overlay OFFで住民、固定地形、Pathおよび移動結果を観察した結果
- 異なるCamera位置とZoom値で住民移動を追跡した結果
- Task System、Reservation System、Resident FSM、道路効果および動的再経路探索が混入していない確認結果

スクリーンショットはHuman Verificationを補助する任意の証跡であり、必要に応じて取得する。動画、GIF、その他の視覚的証跡も同様に任意とする。視覚的証跡が未取得であることだけを理由に、完了判定またはMergeを拒否しない。

具体的な記録形式、ファイル名、収集手順はImplementation Handoffで定義する。

### 17.4 Acceptance Mapping

今回追加するAcceptance Criteriaと検証方法の要件レベルの対応を次に示す。具体的なテストファイル名、Human Verification手順番号、証拠収集手順はImplementation Handoffを正とする。

#### Traversability and Pathfinding

| Acceptance Criterion | Automated Test | Human Verification | Completion Evidence |
|---|---|---|---|
| Blocked Cellを経路へ含めない | Path内の全Cellを検証 | 固定地形を迂回する移動を確認 | 迂回結果、テスト結果 |
| A*で8方向経路を取得できる | Orthogonal／Diagonal Pathを確認 | 両方向の移動を確認 | Pathfindingテスト、移動確認結果 |
| Orthogonal Costを1とする | 区間コストを確認 | 必須としない | テスト結果 |
| Diagonal Costを√2とする | 区間コストを確認 | Diagonal速度を確認 | テスト結果、Diagonal移動結果 |
| 角抜けを禁止する | Orthogonal隣接Cellの各Blocked条件を確認 | 角抜けが発生しないことを確認 | 角抜け禁止結果、テスト結果 |
| 障害物を迂回できる | 迂回PathとCostを確認 | 固定地形を迂回することを確認 | 迂回結果 |
| 到達不能を検出できる | Unreachableを確認 | 住民が移動しないことを確認 | 到達不能結果 |
| 同一セルを成功扱いする | 1Cell PathとCost 0を確認 | 即時成功を確認 | 同一セル確認結果 |
| Invalid Start／Destinationを拒否する | 各失敗理由を確認 | 表示結果を確認 | 失敗結果、テスト結果 |
| 道路効果を適用しない | Path Costへ道路補正がないことを確認 | 必須としない | テスト結果、Scope確認結果 |

#### Resident Movement

| Acceptance Criterion | Automated Test | Human Verification | Completion Evidence |
|---|---|---|---|
| Start Cell中心へ配置できる | Current CellとWorld Positionを確認 | 配置位置を確認 | 初期配置結果 |
| Pathに沿って移動できる | Path IndexとTarget Cell更新を確認 | Game Viewで移動を追跡 | 移動確認結果 |
| Cell中心間を滑らかに移動する | 区間終点と補間位置を確認 | 表示移動を確認 | Orthogonal／Diagonal移動結果 |
| Cell到達時にCurrent Cellを更新する | 到達前後のCurrent Cellを確認 | 状態表示を確認 | Current Cell更新結果 |
| 目的セル中心でSucceededとなる | 最終位置と状態を確認 | 目的セルでの停止を確認 | 成功結果 |
| 初期速度を1 Cell/秒とする | Orthogonal所要時間を確認 | Normalで所要時間を確認 | 速度確認結果 |
| DiagonalでもWorld速度を一定にする | Diagonal所要時間√2を確認 | 不自然な加速がないことを確認 | Diagonal結果 |
| 更新量超過を次区間へ適用する | 複数区間消費を確認 | Fast時の経路追従を確認 | テスト結果、Fast確認結果 |
| 不正Pathへ進入しない | 各不正Pathの拒否を確認 | 失敗表示を確認 | Failure結果 |
| Moving中の新要求を拒否する | 要求拒否と状態維持を確認 | 現在移動が継続することを確認 | 要求拒否結果 |

#### Simulation Time and Validation

| Acceptance Criterion | Automated Test | Human Verification | Completion Evidence |
|---|---|---|---|
| Paused中は移動しない | 位置と区間状態の維持を確認 | Paused中の停止を確認 | Pause確認結果 |
| Resume後に移動を継続する | 区間状態の継続を確認 | 再開後の移動を確認 | Resume確認結果 |
| Normalで基準速度を使用する | 基準速度の適用を確認 | Normal移動を確認 | 速度確認結果 |
| Fast倍率を適用する | 倍率別移動量を確認 | Normal／Fast差を確認 | Fast確認結果 |
| Camera操作は時間倍率から独立する | 必要なRuntime統合を確認 | Paused／Normal／Fastで操作を確認 | Camera確認結果 |
| 到達不能理由を確認できる | Failure Reasonを確認 | Overlayまたは状態表示を確認 | 到達不能結果 |
| Overlay OFFでも移動を観察できる | 表示状態とSimulation状態の分離を確認 | Overlay OFFで移動を確認 | Overlay OFF確認結果 |
| Phase 3以降の機能が混入しない | 依存関係と構成を確認 | 必須としない | Scope確認結果 |

### 17.5 Verification Records

確認結果はImplementation Handoffで指定する形式に記録する。

本書がDraftまたは承認済みであってもUnity実装を開始しない。承認済みImplementation Handoffが別途必要である。

## 18. Phase 3 Handoff Requirements

Phase 3開始前に、Phase 2から次を利用可能な状態で引き渡す。

- Resident Placement State
- 配置済み住民1人の有効なCurrent Cell
- 住民のCell中心に対応するWorld Position
- Traversable CellとBlocked Cellの判定
- 8方向の隣接Cell取得
- 角抜けを禁止するDiagonal Move判定
- A*による経路探索
- Pathfinding Success
- Invalid Start
- Invalid Destination
- Unreachable
- 開始セルと目的セルを含む順序付きCell Path
- Path総コスト
- 基準移動速度
- Cell中心間のMovement
- World座標上の滑らかな表示移動
- Paused、Normal、Fastに従う住民移動
- Movement Idle
- Movement Moving
- Movement Succeeded
- Movement Failed
- 移動成功および失敗理由の確認
- Phase 2 Acceptance Criteriaの確認結果

Phase 3では、本書のPathfindingとMovementを利用できる。ただし、本書は次を定義しない。

- Taskの生成、取得、実行、完了、失敗、取消、中断
- Resident FSM
- Reservation System
- 複数住民
- 仕事またはAIによる目的セル選択
- 仕事失敗後の活動復帰
- TaskとMovementの接続方法

これらはPhase 3の承認済みSystem Specで初めて定義する。

## 19. Open Questions

PDD、GDD、Phase構造またはScopeへ戻す必要があるBlocking Open Questionはない。

次はPhase 2 Implementation Handoffで指定可能な検証設定として扱い、本書のBlocking Open Questionにはしない。

- 固定地形の具体的な外観
- 固定地形の具体的な配置
- 到達可能、迂回、角抜け禁止、到達不能を再現する検証マップ
- 住民のPhase 2検証用表示
- PathのPhase 2検証用表示
- Start Cell、Destination Cell、配置、移動開始、リセットの具体的な入力割り当て
- Phase 2検証表示の具体的な配置
- PathfindingとMovementの失敗理由を表示する具体的な方式
- 基準移動速度を変更する具体的な設定方式

これらは、本書の挙動、責務、Acceptance CriteriaおよびPhase境界を変更してはならない。

## 20. Approval and Implementation Gate

現在の状態は次のとおり。

- Status: Draft
- Version: 0.1
- Approved: Pending
- Implementation Use: Prohibited
- Unity Implementation: Prohibited

本書はDraftであり、Phase 2 Implementation Handoff作成の正式な入力またはUnity実装判断として使用できない。

System SpecのDraft完成または承認だけではUnity実装開始を許可しない。

Phase 2のUnity実装開始には、次のすべてが必要である。

1. 本書がレビュー・承認済みのSystem Specであること
2. Phase 2 Implementation Handoffが作成・承認済みであること
3. ユーザーからPhase 2 Unity実装開始の明示的な許可があること

条件が揃うまでUnity実装を開始しない。
```

## 2. 反映箇所一覧

- 第7章「Traversability Model」
  - 無効セルを1件でも含むBlocked Cell設定を設定異常として扱うこと
  - Traversabilityを利用可能状態にしないこと
  - 無効項目だけを無視して続行しないこと
  - Silent Recoveryおよび自動修復を行わないこと

- 第8章「Pathfinding」
  - A*が定義された移動コストに基づく最小総コスト経路の一つを返すこと
  - 同一最小総コスト経路が複数存在する場合も再現可能な結果を返すこと

- 第9章「Resident Movement」
  - 住民の未配置状態を許容すること
  - 未配置中はCurrent Cellを持たないこと
  - 未配置中は移動要求を開始できないこと
  - 有効なStart Cellへの配置時点で利用可能になること

- 第11章「Debug and Validation Display」
  - 住民の配置状態を確認可能にすること
  - Current Cell表示を配置済みの場合に限定すること

- 第12章「State and Data」
  - Resident Placement Stateの追加
  - 未配置中のResident Current Cellが値を持たないこと

- 第13章「Error and Edge Cases」
  - Blocked Cell設定異常時の非継続・利用不可・非自動修復
  - 住民未配置時の扱い

- 第14章「Initialization Order」
  - Blocked Cell設定が有効な場合だけTraversabilityを初期化すること
  - Start Cell未指定時に住民を未配置状態で維持すること

- 第16章「Acceptance Criteria」
  - Blocked Cell設定異常に関する完了条件
  - 最小総コスト経路と同一コスト時の再現性
  - 住民未配置状態に関する完了条件

- 第17章「Verification Plan」
  - Blocked Cell設定異常の自動確認候補
  - 最小総コストと同一コスト時の再現性の自動確認候補
  - 住民未配置状態の自動確認およびHuman Verification

## 3. 指定3項目以外を変更していないことの確認

指定された次の3項目に必要な箇所のみを変更しています。

1. Blocked Cell設定異常
2. 最小総コスト経路
3. 住民未配置状態

以下は追加・変更していません。

- 新しい仕様
- 章構成および章順
- Phase 2 Scope
- Phase 3以降の除外範囲
- GDD／PDD／Decisionとの整合方針
- Implementation Handoffが別途必要であること
- Unity Implementationの禁止
- I-01 Acceptance Mappingの追加行
- I-02 Pathfinding失敗時のMovement Status遷移の追加明文化
- I-03 浮動小数点比較の許容誤差
- MetadataのStatus、Version、Approved、Implementation Use、Unity Implementation
