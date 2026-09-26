# Project IYASAKA — Prototype 01 Phase 3 Task System Spec

Status: Approved  
Version: 1.0  
Prototype: Prototype 01  
Phase: Phase 3 — 仕事システム  
Approved: Approved  
Approved Date: 2026-09-16  
Implementation Use: Permitted  
Unity Implementation Status: Completed / Human Verification Passed / PR #6 Merged  
Verification Status: A01–A07 / B01–B06 PASS; B-1 focused Human Verification PASS (see §18.1)  
Last Updated: 2026-09-26  
Owner: Project IYASAKA  
Single Source of Truth: GitHub  
Specification Approval Base main HEAD (historical): 265b0cdcf1999078fb930e5fb84377361eeaecba

## 1. Purpose and Authority

住民が仕事を取得・実行し、競合を防げる基盤を、実装開始前の内部仕様へ詳細化する。
目的・Scope・Phase構成・完了条件の正本は[Prototype 01 PDD v1.1 §14・§23](../../02-prototypes/prototype-01/pdd.md)である。本書はそれらを変更しない。

本書にTask System、Reservation System、Resident FSMをまとめ、各責務を分離する。Phase 3の新規Implementation Handoffは作成せず、実装・Verification・Acceptance Mapping・Completion Evidenceを本書で管理する。Legacy Handoffは参照・更新・実装判断に使用しない。

[D-024](../../04-records/decision-log.md#d-024--phase-3以降のsystem-spec-authorityとphase-3仕様承認)により、本Approved System SpecをPhase 3の唯一のNormative Implementation Authorityとする。Implementation Use: PermittedはCodex／Unity実装の正式な入力仕様として使用可能という意味であり、実装開始済みを意味しない。別途のImplementation Handoff承認やユーザーによるImplementation Start Permissionを正式Gateとして要求しない。実装・検証の現在状態は§18.1に記録する。承認済み要件とVersion 1.0、Approved Dateは維持し、今回の更新はEvidenceと進捗の同期に限定する。

## 2. Dependencies and Current Baseline

| Source | 用途・確認結果 |
|---|---|
| [GDD v1.1](../../01-gdd/gdd.md) | ゲーム体験・Design Pillarsの上位方針。変更しない |
| [PDD v1.1](../../02-prototypes/prototype-01/pdd.md) | Phase 3前半／後半と完了条件の正本 |
| [Phase 1 System Spec v2.2](./phase-01-foundation.md) | Grid、座標、Camera/Input、Simulation Time。Camera v2実装・Human Verification Passedを同期済み |
| [Phase 2 System Spec v1.1](./phase-02-pathfinding-and-movement.md) | Traversability、A*、Movement、§18のPhase 3への出力 |
| [System Specs README](../README.md) | Draft運用とPhase別Authority |
| [Roadmap](../../02-prototypes/prototype-01/roadmap.md)・[Open Questions](../../02-prototypes/prototype-01/open-questions.md) | 進捗・未確認の実装前条件 |
| [Decision Log](../../04-records/decision-log.md)・[Changelog](../../04-records/changelog.md) | 設計前提と文書変更履歴 |
| [Regression Checklist](../../04-records/regression-checklist.md) | §16で既存回帰項目をMapping |

2026-09-15の追加Human Verification正式記録とGitHub反映確認：

- 仕様mainは上記Base SHA。Phase 1完了とCamera v2最終Human Verification PassedはPhase 1 Spec §17.6に記録済み。
- Phase 2 [Unity PR #4](https://github.com/undershot0704/Project-IYASAKA-Unity/pull/4)はMerge済み。今回ユーザーがUnity Editorで追加実施したHuman Verification PASSを[Phase 2 Spec v1.1 §17.6](./phase-02-pathfinding-and-movement.md)へ記録し、Phase 2をHuman Verification Passed / Completion Completedとした。PR #4本文に残る当時のPendingを今回の正式証跡で補完する。
- 検証対象はUnity PR #5 HEAD `50510897fec4c55874701ff75abe0ff30856fa13`。今回の実施結果と既存自動テスト・Camera v2回帰記録を区別して対応付けている。
- Camera v2 [Unity PR #5](https://github.com/undershot0704/Project-IYASAKA-Unity/pull/5)を通常Merge CommitでMergeした。Unity mainは `0333868e69eb0b7e84ce6f52067dd468babb315f`。Verified HEADを親に含み、mainのtreeはVerified HEADのtreeと完全一致し、Camera／表示修正の反映を確認した。
- D-022の既存Authority移行はPhase 1限定という履歴を維持する。2026-09-16のD-024によりPhase 3以降の新規PhaseへSystem Spec単独Authorityを適用する。Phase 2のAuthorityは今回移行しない。

OQ-P3-01／02はResolved。2026-09-16にユーザーから提示された弥栄企画壁打ちチャットのSpecification Review結果はPassed / Blocker None。本書はv1.0 Approved / Implementation Use Permitted。現在の実装・Human Verification結果は§18.1を参照。

## 3. Scope and Verification Order

| 順序 | PDD上の対象 | 本書での検証 |
|---|---|---|
| Phase 3-A相当（前半） | 住民1人、検証用の単純な仕事、Task System、Resident FSM、完了・失敗・取消・中断後の復帰 | 単独Residentの取得→移動→作業→終了、元TaskへのResume、終了後の次Task取得 |
| Phase 3-B相当（後半） | 住民2人、Reservation System、仕事と対象の競合、住民無効化時の解放 | 二重Assignment防止、Target排他、Interrupt／各終了経路のRelease、Disabled後の対象再利用 |

A／Bは既存Phase 3内の検証順序を示すラベルであり、新Phase、新Scope、追加の承認単位ではない。AのLifecycleを先に確認し、Bで実Reservationを統合してAを再実施する。AだけではPhase 3完了にならない。

## 4. Out of Scope

- Gather／Haul／Buildと固有FSM状態、資源生成・所持・消費、倉庫、建設予定地、木の消失
- Phase 4以降、道路配置・経路コスト補正・速度補正
- Priority、Utility AI、Score、距離優先、職業・能力・欲求、別TaskへのInterrupt切替
- 住民同士の衝突、セル占有、経路Reservation、回避、渋滞、順番待ちQueue、共同作業
- 動的障害物、動的再経路探索、Target移動、複数Targetを一Taskで確保する仕組み
- Task再配布、他Residentへの途中進捗引継ぎ、高度なRecovery／Retry機構
- 完成版UI／アート、Save/Load、Task依存グラフ、汎用AI／Pointer／Task Framework
- Phase 2のPathfinding／Movement再実装、既存Camera仕様変更、新Package、Project Settings変更、無関係なリファクタリング

## 5. Responsibility Boundaries

| 領域 | 唯一の書き込み責務 | 入出力 | 担当しないこと |
|---|---|---|---|
| Task System | Task登録・作成順・Lifecycle・Resident Assignment | 取得要求、Task結果、Assigned Resident ID | Target排他、座標補間、Resident FSMの内部状態 |
| Reservation System | Targetごとの排他所有とRelease | Target ID、Resident ID、Task ID、Reserve結果 | Task選択・Assignment・仕事の成功判定 |
| Resident FSM | 取得済みTaskの実行段階と再開位置、作業進捗 | Task取得、Move要求、作業結果、Interrupt／Resume | Task一覧の直接変更、独自Pathfinding |
| Phase 2 Pathfinding & Movement | Path、経路結果、位置と移動区間 | Current Cell、Target Cell、既存時間入力 | 仕事選択、Target所有、作業時間 |
| Verification integration | シナリオ初期化、検証コマンド転送、状態の読み取り表示 | 固定条件、操作、観察記録 | 所有状態の直接上書き、完成版の住民直接命令 |

AssignmentはTaskとResidentの関係、ReservationはTargetと所有者の関係であり別概念。FSMはTask Systemへ遷移を要求し、受理結果を反映する。共有状態を各Componentで独立に書き換えない。

## 6. Minimal Data and Verification Task

| データ | 最小項目 |
|---|---|
| Task | 一意Task ID、単調増加Creation Order、Target ID、Task Status、Assigned Resident ID（未割当は空）、終了理由 |
| Verification Target | 一意Target ID、有効性、固定Target Cell |
| Resident | 一意Resident ID、Enabled／Placement状態、Current Task ID、FSM状態、Phase 2 Movement参照 |
| Execution context | Interrupted前のMoving／Working、保存Movement区間、Work Duration、Work Elapsed |
| Reservation | Target ID → (Resident ID, Task ID)。未確保は空 |

同じシナリオ内でID／Creation Orderを再利用しない。壁時計、Unity Instance ID、辞書列挙順で選択順序を作らない。名前や色は表示専用とする。

Verification Taskは「固定Target Cellまで移動し、一定Simulation時間だけ作業してCompletedになる」一種類のみ。

- Phase 3 Verification用固定値はWork Duration = 3 Simulation秒。0以下・NaN・Infinityを拒否する。完成版の仕事時間ではない。
- Task作成時にTargetが登録済み・有効・Traversableであることを検証する。到達可能性は取得後のPhase 2探索で判定する。
- 到着前に作業を加算しない。同一CellならPhase 2の即時Succeededを受け、次の更新からWorkingを開始する。
- Workingでは有効なSimulation deltaだけを加算し、DurationへClampする。完了は一度だけ。終了で資源、建物、スコア等を生成しない。
- Targetは完了で消費・破壊せず、Release後に別Taskから利用可能。再検証Taskは明示的なシナリオ作成で追加し、Completed TaskをAvailableへ戻さない。
- Fixtureの各Targetは別Cellに置く。同一対象競合には必ず同じTarget IDを使用する。Target Reservationはセル占有ではなく、住民は同じCell／経路を通過できる。
- Failed検証は到達不能Target、または専用の「現在Taskを失敗させる」検証要求で再現する。資源消失など後続Phaseのゲーム処理を追加しない。
- Task作成は固定シナリオ／デバッグ操作。ResidentへのTask直接指名は行わず、Idle Residentが自動取得する。

## 7. Task Lifecycle

| Task Status | 意味 | 許可される次状態 |
|---|---|---|
| Available | 取得可能、Assignmentなし | Assigned、Cancelled、Failed（取得前Target無効のみ） |
| Assigned | Residentへ一意Assignment済み、実行開始前 | InProgress、Failed、Cancelled |
| InProgress | MovingまたはWorking実行中 | Interrupted、Completed、Failed、Cancelled |
| Interrupted | 元Assignmentと実行情報を保持し停止 | InProgress、Failed、Cancelled |
| Completed | 正常完了、終端 | なし |
| Failed | 実行失敗、終端 | なし |
| Cancelled | 明示取消またはResident Disabled、終端 | なし |

Completed／Failed／CancelledではAssignmentなし、Reservationなし、ResidentのCurrent Taskなしとする。終了理由・直前所有者は検証履歴に記録できるが、活動中の所有として残さない。
終端Taskを再取得・再開・再完了しない。Failed Taskの自動再試行は行わず、次のAvailable Taskへ進む。必要なら検証操作で新Taskを作成する。
Available取消はResidentへ影響しない。Assigned／InProgress／Interrupted取消は§11のCleanupを行う。

## 8. Task Acquisition and Assignment

1. Enabledかつ配置済み、FSM Idle、Current TaskなしのResidentだけが取得要求できる。
2. Task SystemがAvailableをCreation Order昇順で走査する。
3. Targetが現在無効なら、そのTaskをFailedへ確定し理由を残す（取得前検証失敗の例外遷移）。以後の更新で再走査しない。
4. Bでは候補TaskのTargetへReserveを要求する。他者確保中ならTaskはAvailable・未Assignmentのまま、その走査で次候補を見る。
5. Reserve成功とAssignmentを、他Residentの取得処理が割り込まない一連の処理として確定する。Task SystemがAvailable再確認を行い、Assigned ResidentとResident Current Taskを一致させる。途中失敗なら今回のReserveのみRollbackし、半割当を公開しない。
6. FSMは取得成功後に経路探索を要求する。失敗ならFailed、成功ならInProgressとMoving／Workingへ進む。
7. 取得候補がなければIdleを維持する。次の有効Simulation更新で再取得可能とし、Busy Loopや毎フレーム同一警告を出さない。

Phase 3-AはResident 1人のTask Assignmentを検証し、Bで上記Reserveを含む全手順を検証する。最終実装でReservationを迂回できる通常経路を残さない。

同一更新に複数Residentが要求する場合は固定のResident登録順で処理する。距離を比較しない。確保中の古いTaskを飛ばすことは実行可能性の判定であり、距離／Score優先ではない。公平性保証や高度な待機機構は導入しない。

Creation OrderはPhase 3 Verification／Prototype内部の単純・決定的な暫定選択ルールであり、完成版のTask Priority方式を固定しない。将来のPhaseまたは正式設計でPriority／Distance／Utility／Score方式へ変更・拡張することを妨げない。Phase 3ではそれらを導入しない。

## 9. Resident FSM and Transitions

| FSM状態 | 意味・入力 | 遷移 |
|---|---|---|
| Idle | 現在Taskなし。Enabled・配置済みなら取得要求 | Acquiring |
| Acquiring | Task Systemへ取得を要求する短い処理段階 | 無Task→Idle、成功→MovingまたはWorking、探索失敗→Failed |
| Moving | Phase 2 Movementへ進行を委譲 | 到着→Working、失敗→Failed、Interrupt→Interrupted |
| Working | Target所有を確認し作業時間を加算 | Duration到達→Complete、失敗→Failed、Interrupt→Interrupted |
| Interrupted | 元Task・復帰先と進捗を保持 | Resume成功→元Moving／Working、Target無効→Failed |
| Complete | Task Completedを確定・Cleanupする結果段階 | 次の有効Simulation更新でIdle |
| Failed | Task Failedを確定・Cleanupする結果段階 | 次の有効Simulation更新でIdle |

Complete／Failedは永続的活動ではなく、結果を履歴表示で観察可能にしてから次活動へ戻る。Acquiringも持続時間を要求しない概念上の状態である。
取消時はTaskをCancelledにし、結果Cancelledを記録してIdleへ戻す。Resident FSMにTaskの終端状態をすべて複製しない。
DisabledはResidentの利用可否であり仕事実行状態ではない。無効化時はCleanup後IdleかつEnabled=falseとし、再取得しない。

Task InProgressの間にResident Moving→WorkingとなってもTaskを別仕事として作り直さない。Phase 2 Movement Succeededは「到着」でありTask Completedではない。

## 10. Interrupt / Resume and Simulation Pause

- InterruptはMoving／Workingだけで受理する。TaskをInterruptedとし、Assigned Resident、Current Task ID、Work Elapsed、元FSM状態、Movement区間と補間位置を保持する。
- Interrupt時は進行を止めてTarget ReservationをReleaseする。PDDの「中断後に対象を再利用できる」を満たす。同じTaskはAssignedのままで他Residentへ渡さない。
- Interrupted中は新Taskを取得せず、Movement／Workを進めない。他Residentは同Targetの別Available Taskを取得できる。
- Resumeは元Resident・元TaskについてTarget有効性を検証し、Targetを再ReserveしてからInProgressへ戻す。
- Resume時にTargetが他者確保中ならInterruptedを維持し、TargetBusyを表示する。Assignment／進捗を消さず、横取りせず、別Taskへ切り替えない。解放後の明示的Resumeで再試行する。自動Resume Queueは不要。
- 再Reserve後は保存されたMoving区間／Working進捗から再開する。固定経路を使用し、再探索や位置リセットを行わない。
- Target無効、保存状態不正はFailedとしてCleanupする。検証要求によるInterrupt／Resumeの連打は二重Release、二重進捗、二重Reserveを起こさない。

Interrupt時ReleaseとResume時再Reserveは、PDD Phase 3の中断後の対象再利用を検証するPrototype内部仕様である。完成版の全Interrupt理由・全仕事に共通する恒久的なReservation解放規則を固定しない。

Simulation PauseはPhase 1時間倍率0による停止であり、Task／FSMをInterruptedへ変更しない。PauseだけでAssignment／Reservationを解放しない。
Paused中は自動取得・移動・作業・自動の次活動遷移を停止する。検証用Cancel／Disable／Interrupt／Resumeは受理可能とするが、Resume成功時も時間進行は0のまま。Pause解除は明示的Task Interruptを解除しない。
Normal／Fastでは既存Simulation倍率を一度だけ適用する。Cameraと検証操作は時間倍率非依存。

## 11. Reservation and Cleanup

ReserveはTarget IDに対する不可分な「空き確認と所有記録」とする。同一(Resident ID, Task ID)の再Reserveは同一所有として成功し、別所有者を上書きしない。Releaseは所有者とTask IDが一致した場合だけ消す。既に未確保なら安全なNo-op、他者所有なら拒否し理由を残す。

| イベント | Task | Assignment | Reservation | Resident実行 |
|---|---|---|---|---|
| Completed | 終端Completed | 解放 | Release | Complete→Idle |
| Failed／探索失敗 | 終端Failed | 解放 | Release | Failed→Idle |
| Cancel | 終端Cancelled | 解放 | Release | 移動／作業停止、Idle |
| Interrupt | Interrupted | 保持 | Release | 状態と進捗保存 |
| Resume成功 | InProgress | 保持 | 再Reserve | 保存段階へ復帰 |
| Resident Disabled | 活動中TaskをCancelled、理由ResidentDisabled | 解放 | 全所有をRelease | 停止、無効化 |

CleanupはTask終了・Assignment解放・Reservation解放・Resident参照解消を単一処理順で完遂する。終了要求が重複しても結果・進捗を二重適用しない。途中で次Residentの取得を許可しない。古い終了通知が新TaskのReservationを解放しないようTask IDを照合する。

Resident DisabledはIdle／Moving／Working／Interruptedのいずれでも有効。所有Reservationを残さず、同Targetの別Available Taskを残るResidentが取得可能になる。元TaskはCancelledのため再配布しない。再有効化は検証用の有効Cell配置からIdleへ戻し、Cancelled Taskを復活させない。

## 12. Phase 2 Movement Integration

- 同じPhase 2 Pathfinding／Movement基盤を使用する。Residentごとに移動状態を独立保持し、Pathfinderと固定Traversabilityは共有可能。二人分のアルゴリズムを複製しない。
- Task Target Cellを移動先として渡す。開始はResident Current Cell。Debug Destination変更を実行中Taskへ混入させない。
- A*、8方向、Orthogonal Cost 1／Diagonal Cost √2、角抜け禁止、最小コストと決定性、初期速度1 Cell/秒、同一Cell成功、Current Cell更新規則を維持する。
- Interruptは当該ResidentのMovement更新を停止し、保存済みPath／区間／World Positionを保つ。グローバルPauseを使用して他Residentまで止めない。再開で中断時間分を追い付き加算しない。
- Cancel／Failed／Disabledによる終端では当該Movementを停止・破棄し、最後に到達済みのCurrent Cell中心へ表示位置を整合させる。これはPhase 3の終端Cleanupだけに限定した検証用処理であり、Interrupt／Resumeには適用しない。
- Phase 2単独検証ではMoving中の移動・再配置・Reset拒否を維持する。Phase 3の内部停止／終了接続に必要な最小追加は可能だが、一般の操作拒否規則を解除して代用しない。
- Phase 3でTask保持中の手動移動・再配置要求は拒否する。シナリオ全Resetは全Residentを無効化してCleanup後に検証初期状態を再構築し、動作中状態へ直接上書きしない。
- 探索失敗理由をTaskへ伝える。Movementの不正Path拒否や最後の正常Cell維持を緩和しない。

## 13. Determinism and Failure Behavior

同じシナリオ、Task作成順、Resident登録順、順序付き検証コマンド、Simulation delta列から、Assignment・Reservation・結果が再現すること。異なる描画FPSでビット単位の履歴一致を要求せず、同じSimulation時間に対する移動量・作業量の整合を確認する。

各更新は、検証コマンドの受付順処理→有効Simulation更新（Resident登録順）とする。各Residentは終了段階からIdleへ戻る更新で新Taskまで連続実行せず、次更新に取得する。到着した更新の残りdeltaをWorkingへ二重使用せず、作業は次更新から進める。汎用Schedulerは作らない。
同一更新のCancel／Disable等が処理されたTaskへ、後段のMovement／Work完了を適用しない。終端が既に確定したTaskへの遅延通知は無視して元結果を保持する。

| 条件 | 結果 |
|---|---|
| 未配置／Disabled Residentの取得 | 拒否、他状態を変えない |
| 無効ID、重複登録、不正Duration | 登録拒否、半作成TaskやReservationなし |
| TargetBusy（取得） | 未AssignmentのAvailableを維持、次候補へ |
| TargetBusy（Resume） | Interrupted維持、理由表示 |
| Invalid Start／Invalid Destination／Unreachable／Unavailable | Failed、Phase 2理由保持、Cleanup |
| Movement Failed／検証用強制失敗 | Failed、理由保持、Cleanup |
| 実行中のTarget無効／所有不一致 | Failed、他者Reservationを解放せず自分の状態をCleanup |
| 適用不能なInterrupt／Resume | 拒否、既存状態保持 |

通常の競合・意図した失敗は検証結果として表示し、未処理例外や継続的Console警告にしない。必要な基盤が初期化不能の場合はPhase 3 Runtimeを利用不可として理由を示し、無効設定を黙って修復しない。

## 14. Verification / Debug Requirements

Resident ID・Enabled・配置状態・FSM・Current Task ID、Task ID・作成順・Status・Assigned Resident、Target ID／Cell・Reservation所有者、Work進捗、Interrupt前状態、取得拒否／失敗／終了理由を識別できること。
既存のCurrent Cell／World Position、Path／Path Cost、Movement Status、Simulation Time、Camera情報も観察可能にする。

固定シナリオ開始、Task追加、Task取消、Resident Interrupt／Resume、強制失敗、Resident Disable／再配置、全Resetを最低限の検証操作で再現できること。既存Camera／Cell選択／Pause／F1と入力を競合させない。具体的な検証ボタン配置・色・クラス名・テスト名は実装時の最小裁量であり、Completion Evidenceへ記録する。

F1はOverlayだけを切り替える。Overlay OFFでもGrid・Resident・Path・Targetを観察できる。二人のPath／Current／Targetと所有情報を混同しない。Camera向きによる表示Depth補正は表示に限定し、Cell座標やTarget座標へ戻さない。

## 15. Automated Tests

以下は実装の必須検証。提出済みAutomated Evidenceは§18.1を参照。今回のSpecification SyncではUnityテストを再実行していない。

| ID | 検証内容 |
|---|---|
| AT-01 | 作成順選択（距離が逆順でも同じ）、同更新のResident順、無Task、未配置／Disabled拒否、不正ID／Duration拒否 |
| AT-02 | 正常Lifecycle、同一Cell到着とWork区別、作業時間Clamp、終端一回、終了後に別Task取得、Failed自動再取得なし |
| AT-03 | Invalid Start／Destination／Unreachable／Unavailable・不正Path・強制失敗、理由保持とCleanup |
| AT-04 | Available／Assigned／Moving／Working／Interrupted取消、連打・遅延通知、各終了経路のAssignment／Reservation残存なし |
| AT-05 | Moving区間途中／Working途中のInterrupt、元Task・位置・進捗保持、Release、Resume再Reserve、進捗継続 |
| AT-06 | 二人が同Taskを取得要求、別Task同Target、別Target並行作業、Reserve失敗時Rollback、所有者違いRelease、古い通知拒否 |
| AT-07 | Interrupted中の他者Target利用、Resume競合拒否、他者Release後の元Task再開、Target無効時Failed |
| AT-08 | Idle／Moving／Working／InterruptedでDisable、重複Disable、全所有Release、残るResidentが対象利用、再有効化でTask復活なし |
| AT-09 | PauseでTask／FSM／所有保持、停止・再開、Normal／Fast倍率一回適用、Task InterruptとPauseの交差、Camera時間非依存 |
| AT-10 | 既存Phase 1／2回帰テスト、8方向・最小コスト・角抜け・同一Cell・大delta・位置規則・既存Moving中要求拒否、二人のMovement独立 |
| AT-11 | Overlay ON/OFFとSimulation分離、表示参照接続、全ResetのCleanup、順序固定で履歴再現、終了要求と同更新完了競合 |

## 16. Human Verification and Regression Mapping

各シナリオを固定条件から開始し、操作・期待結果・実結果・対象Commitを記録する。Aを先に実施し、B統合後にA全項目を再実施する。

| ID | 構成・手順 | 期待結果 |
|---|---|---|
| HV-A01 | 一人を配置、遠いTaskを先・近いTaskを後に作成 | 遠いTaskから自動取得、移動→3 Simulation秒のWorking→完了→次Task |
| HV-A02 | Current Cellと同じTargetのTaskを作成 | 移動即時成功だけではTask完了にならず、Workingを経て完了 |
| HV-A03 | 到達不能Taskの後に到達可能Taskを用意、別試行でMoving／Working強制失敗 | 理由表示、終了Cleanup、次Taskで活動継続 |
| HV-A04 | Available、Moving、Working、Interruptedの各時点でCancel | 終端Cancelled、動作停止、所有残存なし、次Task取得 |
| HV-A05 | Cell間Moving途中でInterrupt→待機→Resume | 同じTask・Cell・補間位置を保持し元区間から再開、位置跳躍なし |
| HV-A06 | Working途中でInterrupt→待機→Resume | Work進捗を保持、再開後に残り時間だけ作業、完了一回 |
| HV-A07 | Moving／WorkingでNormal→Pause→Resume、Fast→Pause→Resume。Interrupted中にもPause切替 | Pauseで状態・所有不変、位置／時間停止、Camera操作可。Task Interruptは独立 |
| HV-B01 | 二人を配置しAvailable Taskを一つだけ用意 | 一人だけAssignment、他者Idle、重複実行なし |
| HV-B02 | 同Targetの別Task二つと別TargetのTaskを用意 | 同Target同時利用なし、Busy候補を飛ばして別Targetは並行実行 |
| HV-B03 | 所有者をMoving／WorkingでInterrupt、他者に同Target別Taskを取得させる | Assignment保持・Target解放、Resume競合ではInterrupted維持、他者完了後Resume成功 |
| HV-B04 | Complete／Failed／Cancelledごとに同Targetの別Taskを用意 | 各終了後Release、他者が再利用可能 |
| HV-B05 | Idle／Moving／Working／Interruptedの各状態でResident Disable | 所有を全解放、元Task復活なし、残るResidentが対象利用。再有効化も確認 |
| HV-B06 | B構成でA01〜A07を再実施、シナリオを同条件で再開始 | 単独Lifecycle回帰なし、取得・競合結果再現 |
| HV-R01 | 下記Camera／表示Regression Matrixを全組合せで確認 | 表示欠落、操作競合なし |
| HV-R02 | 下記Functional RegressionをPhase 1／2単独SceneとPhase 3で確認 | 既存入力・移動・時間・位置規則を維持 |
| HV-R03 | F1 ON/OFF、二人動作中、失敗後、全Reset後の表示・Consoleを確認 | 状態追跡可能、Error 0／新規Warning 0、著しい遅延・負荷なし |

### Existing Regression Checklist Mapping

以下の節・項目名を既存Baselineへの参照とする。2026-09-22のSpecification Syncで、B-1の再発防止としてRegression Checklist §3・§5に表示とlogical Cell選択の一体確認を追加した。既存の検証要件は変更しない。

| 既存正本の項目 | Phase 3での再確認 | 対応 |
|---|---|---|
| Regression Checklist §3：Orbit全域、Pitch下限／上限、Zoom下限／上限、Pan後、WASD後 | 各Camera状態で§4の全表示（Grid、Path Line、Target、Resident、Current、Blocked）を確認。A／B両構成、Overlay ON/OFF | HV-R01、AT-10/11 |
| §5：Start／Destination維持、Cell選択とCamera競合なし | Left Click／Drag、Right Click／Orbit、別Cell／同Cell、Task Targetと検証用選択の独立性 | HV-R02、AT-10 |
| §5：Pathfinding結果、Resident Placement／Movement | 迂回・到達不能・8方向・角抜け禁止・同一Cell・Current Cellと表示、既存手動要求拒否 | HV-R02、AT-03/10 |
| §5：Pause／Resume中Camera操作と表示 | Normal／Fastへの復帰、Elapsed Time連続性、位置・区間保持、Paused Camera操作 | HV-A07、HV-R02、AT-09 |
| §5：Console Error 0／Warning 0 | 通常・失敗・競合・Disable・Reset後のConsole。既存Warningがあれば由来と非増加を別記 | HV-R03、全自動テスト結果 |
| Phase 2 Spec §17：Overlay OFF、移動速度・大delta・失敗理由 | World表示を残す、Orthogonal 1秒／Diagonal √2秒（1 Cell/秒）、Fast、失敗後位置保持 | HV-R02/R03、AT-03/10/11 |

## 17. Acceptance Mapping

| PDD Phase 3完了条件 | 本書要件 | Automated | Human | Completion Evidence |
|---|---|---|---|---|
| 住民が仕事を取得して実行できる | §6–9、§12 | AT-01/02/10 | A01/A02、B06 | E-02/E-03 |
| 完了・失敗・取消・中断後に次の活動へ移れる | §7、§9–11 | AT-02–05/09 | A01–A07、B06 | E-02/E-03/E-04 |
| 二人が同じ仕事を重複取得しない | §8、§13 | AT-01/06/11 | B01/B06 | E-02/E-04 |
| 二人が同じ対象を重複利用しない | §8、§10–11 | AT-06/07 | B02/B03 | E-02/E-04 |
| 失敗または中断後に対象を再利用できる | §10–11 | AT-03–08 | B03–B05 | E-02/E-04 |
| 失敗後も別の仕事を継続できる | §7、§9、§13 | AT-02/03/08 | A03/B04/B05 | E-02/E-03/E-04 |
| 後半：住民無効化時の解放確認 | §11–12 | AT-08 | B05 | E-02/E-04 |
| 依存機能の維持・Scope逸脱なし | §4、§12、§16 | AT-09–11、差分監査 | A07、R01–R03 | E-01/E-05/E-06 |

Human列のA/B/R番号は§16のHV接頭辞を省略している。全行が検証され、未解決不具合が次Phase検証を妨げないことを確認してからPhase 3完了を判断する。実施結果は§18.1を参照。Specification Review、Automated Tests、Human Verification、Merge状態を別々に記録し、未提示項目を一括PASSへ読み替えない。

## 18. Completion Evidence and Implementation Constraints

| ID | 必須記録 |
|---|---|
| E-01 | 参照Spec Version／SHA、Unity Base／実装Commit SHA、PR、変更／新規ファイル一覧、実装環境 |
| E-02 | AT ID→実テスト名、EditMode／PlayMode件数・成功／失敗／Skip、Compiler／Console結果。未実施をPASSとしない |
| E-03 | A全手順のHuman実施日・条件・期待／実結果、元Taskと中断前後進捗、終了後の次活動 |
| E-04 | B全手順の二人のTask Assignment／Target所有履歴、各Release、Resume競合と復帰、Disable後再利用、B統合後A再実施 |
| E-05 | R全手順と既存Checklistの項目別結果、Camera可動域×表示Matrix、Pause／Input／Pathfinding／Movement回帰 |
| E-06 | 検証Duration・Resident順・Task作成順・Target配置・入力操作、Scope外変更なし、既知制限・未解決事項、著しい負荷の有無 |

Human Verification実施記録が正式Evidence。スクリーンショット、動画、GIFは任意の補助証跡であり未取得だけで完了を拒否しない。具体的な記録ファイル名は実装PRで示し、上記要件は省略しない。

実装時も本書の最小責務に留め、既存Input資産・Grid・Camera・Simulation Time・Pathfinding／Movementを利用する。検証操作に必要な最小接続以外を変更しない。Package、Project Settings、完成版向けFramework、後続Phaseを追加しない。


### 18.1 Phase 3 Implementation Verification Record — 2026-09-22 Sync

本節はユーザー提示の確定Human Verification／Specification Diff Re-Review結果と、GitHub上の実装・Automated Evidenceを同期する。2026-09-22は同期・受領記録日であり、提示されていない各Human試行の実施日時を補完しない。新しいDesign Decision、Scope変更、Normative requirement変更はない。

#### E-01 / E-06 — Traceability at verification sync (historical)

| 項目 | 確認結果 |
|---|---|
| Specification main / Authority | `f9b13bfc966db354f6cd1e6dae687cbf0b801e73` / 本Spec v1.0 Approved（D-024）。PDD v1.1、Phase 1 v2.2、Phase 2 v1.1を維持 |
| Unity main / PR base | `0333868e69eb0b7e84ce6f52067dd468babb315f` / `main` |
| Unity PR / branch | [#6](https://github.com/undershot0704/Project-IYASAKA-Unity/pull/6) / `feat/prototype01-phase3-task-system` / Open / Draft / 未Merge |
| Fix / focused Human verified HEAD | `ce147e768c05ec86bb0f6a19303e1abdaaa2cbc7` |
| Re-Review range | `600662e9d594c64adedbd0ae14a4294992f25cff` → `ce147e768c05ec86bb0f6a19303e1abdaaa2cbc7` |
| Implementation environment / fixture record | Windows / Unity 6000.3.20f1（PR提出情報）。[固定HEADの検証記録](https://github.com/undershot0704/Project-IYASAKA-Unity/blob/ce147e768c05ec86bb0f6a19303e1abdaaa2cbc7/docs/phase03-verification.md)にAT対応、変更ファイル・役割、3 Simulation秒、Resident順、Target配置、操作を記録 |

Unity PR本文および実装側文書に残るHuman Verification Pending / Not Runは、今回のユーザー提示結果の同期前の記録。本節が今回受領した結果を記録する。Unity側の文書・PRは変更していない。このVerification同期時点では検証済みHEADはUnity mainへ未反映だった。以後のMerge確認は§18.2に記録し、当時のSHA／PR状態を履歴として保持する。

#### E-03 / E-04 — Phase 3 Human Verification

ユーザー確定結果：**A01〜A07 PASS、B01〜B06 PASS / Phase 3 implementation Human Verification completed**。

§16のIDへ対応する結果として、Task creation／acquisition、Creation Order、Assignment、Target Reservationとone owner exclusivity、Moving／Working／Completion／Failed／Cancel、Interrupt／Resume、Reservation release／reacquire、Target busy時Resume拒否、Pause／Fast、複数Resident、terminal cleanup、Resident Disable、Phase 2 Movement integrationの確認完了を記録する。

A01〜B06の既存PASSを保持する。共通変換修正後の全項目再実施は行っておらず、ユーザー提示のSpecification Diff Re-Review判定に従い不要とされた。修正後HEADで全A/Bを新規実施したとは記録しない。

Fastの追加実機確認：Phase03TaskSystemで**キー2 → Fast切替**、Overlay **`Time: Fast (4x)`**。Fast倍率は**4x**。固定HEADのScene `fastMultiplier: 4`、Phase01Bootstrapの `timeController.Initialize(4f)`、Phase03Bootstrapの現在倍率適用と整合する。Phase 1 §10.1の設定値制約および本書§10の倍率一回適用に適合し、倍率を新たなNormative固定値にはしない。

#### E-05 — Camera / Cell Selection B-1 and focused Human Verification

Phase 3固有確認後のRegressionでB-1を検出。Phase02PathfindingAndMovement、Pitch 20°／Zoom 24付近で中央・左右・手前のvisibleな有効Cellの選択が失敗し、`Screen position does not intersect the Grid plane.` を表示した。B-2なし。台形状の見え方は本不具合の対象外。

原因はdisplay-only depth compensationとlogical Gridの分離下で、near planeを始点とするRayの負の交差距離を旧処理が拒否していたこと。fixは `Phase01CameraController.TryScreenToGridPlane` の符号付き交点計算への最小修正。[固定HEADの修正・テスト記録](https://github.com/undershot0704/Project-IYASAKA-Unity/blob/ce147e768c05ec86bb0f6a19303e1abdaaa2cbc7/docs/camera-cell-selection-regression.md)を参照。

Re-Review結果（ユーザー提示）：**Specification Diff PASS / Previous B-1 Resolved（code / automated tests）/ New Blockers None / Human Verification Proceed**。runtime差分は同変換のみ。logical Grid、display depth compensation、Pitch／Zoom範囲、Orbit／Pan／WASD、Click／Drag、Grid bounds、Pathfinding／Movement、Task／Assignment／Reservation／FSM、取得順序、Interrupt／Resume、Pause／Fast semantics、Scene、Package、Project Settings、Approved Specificationを維持。

次は `ce147e768c05ec86bb0f6a19303e1abdaaa2cbc7` での修正後実機確認結果（ユーザー提示）。A〜Kは本focused確認のラベルであり、HV-A01等とは別である。

| 項目 | 条件・実結果 | 結果 |
|---|---|---|
| A | Phase 2、Pitch 20°／Zoom 24。中央・左右・手前のvisible Cell選択成功、Start Marker正常、intersection error再発なし | PASS |
| B | Pitch 80°／Zoom 4でCell選択 | PASS |
| C | 一周Orbit、複数Yaw方向でCell選択 | PASS |
| D | Pan／WASD後のCell選択 | PASS |
| E | LMB short clickでStart、LMB dragでPan・Start不変、RMB clickでDestination、Grid外click拒否・既存selection保持 | PASS |
| F | Phase 2：Start→P配置→Destination→Enter→Path→Movement→Arrival | PASS |
| G | Phase01Foundation：Start／Destination選択、LMB Drag Pan、DragでStart不変 | PASS |
| H | Phase03TaskSystem Scenario B：Task取得・移動中の別Cell選択が正常。Task継続、Target／Assignment／Reservation不変 | PASS |
| I | Paused中のWASD／Zoom／Orbit／Pan／Cell Selection | PASS |
| J | キー2でFast、Overlay `Time: Fast (4x)`。Camera／Cell Selection／Task／Resident進行が正常 | PASS |
| K | 実機Console Error 0 / Warning 0 | PASS |

**Camera / Cell Selection B-1はHuman VerificationでもResolved。** 上記focused確認と既存A/B結果を記録し、全Camera表示Matrixや未提示の個別測定結果を新規PASSと推測しない。Phase全体の完了判断には§17・§18の既存要件を引き続き使用する。

#### E-02 — Automated Evidence (separate from Human Verification)

GitHub PR #6／修正記録の2026-09-22提出結果：**EditMode 146/146 PASS、PlayMode 99/99 PASS、双方Failed 0 / Skipped 0、Compiler Error 0 / Warning 0**。修正に伴いPlayMode 31ケース追加。今回のSpecification Syncでテストを再実行した結果ではない。実機Console結果は上記Kに別記する。

修正差分の5ファイルはCamera controller、PlayModeのPhase01CameraControllerTests／Phase02RuntimeIntegrationTests／Phase03RuntimeTests、および `docs/camera-cell-selection-regression.md`。PR全体の変更は22ファイルであり、この5ファイルは上記Re-Review範囲だけの一覧である。

### 18.2 Phase 3 Merge Status Sync — 2026-09-22

GitHub current main再取得時に、仕様[PR #29](https://github.com/undershot0704/Project-IYASAKA/pull/29)とUnity [PR #6](https://github.com/undershot0704/Project-IYASAKA-Unity/pull/6)のMerge完了を確認した。

- Specification main／PR #29 Merge commit: `c62e072e5a696a8987057b74792a573fb550fdc3`。
- Unity PR #6 verified HEAD: `ce147e768c05ec86bb0f6a19303e1abdaaa2cbc7`。
- Unity PR #6 Merge commit／current main: `31ed1932cff5070c0fb69fa46f27cd3896f069a8`。
- Phase 3: Completed / Human Verification Passed / Unity main反映済み。

本追記は状態同期のみ。§18.1のHuman／Automated Evidence、v1.0の要件・Approved Dateは維持し、今回テストを再実行していない。

## 19. Open Questions and Implementation Readiness

- **OQ-P3-01: Resolved。** ユーザーの追加Human Verification PASSをPhase 2 Spec v1.1 §17.6へ正式記録し、既存自動テスト・回帰Evidenceと合わせてPhase 2 Human Verification Passed / Completion Completedを確認した。
- **OQ-P3-02: Resolved。** Unity PR #5をMergeし、Unity main `0333868e69eb0b7e84ce6f52067dd468babb315f` がVerified HEAD `50510897fec4c55874701ff75abe0ff30856fa13`と同一treeであることを確認した。

両項目の解消は進捗・実装基準の同期のみ。2026-09-16のSpecification Review結果は **Passed / Blocker None**。PDD v1.1 Phase 3と、本書のTask／Lifecycle／Assignment、Reservation、Resident FSM、Interrupt／Resume、失敗・取消・Disabled／Cleanup、Phase 2統合、Automated Tests、Human Verification、Regression／Acceptance Mapping、Completion Evidenceの整合を確認し承認した（ユーザー提示の正式Review結果）。仕様PR #28はMerge済み。現在は§18.1のHuman Verification結果と§18.2のMerge完了を同期済み。次工程はPhase 4の仕様策定であり、本書の承認済み挙動は変更しない。

検証用数値・表示配置を越えて、ゲーム体験、Prototype Scope、Phase構成または仕事固有挙動の判断が必要になった場合は、**「弥栄企画壁打ちチャットで判断すべき事項」**として報告する。現時点で新たなゲーム体験上のBlocking Open Questionは確認していない。
