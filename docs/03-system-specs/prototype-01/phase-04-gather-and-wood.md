# Project IYASAKA — Prototype 01 Phase 4 Gather and Wood System Spec

Status: Draft  
Version: 0.1  
Prototype: Prototype 01  
Phase: Phase 4 — 生活ループ  
Approved: Pending  
Implementation Use: Prohibited  
Unity Implementation Status: Not Started  
Verification Status: Not Run  
Last Updated: 2026-09-26  
Owner: Project IYASAKA  
Single Source of Truth: GitHub  
Specification Base main HEAD: c62e072e5a696a8987057b74792a573fb550fdc3  
Unity Baseline main HEAD: 31ed1932cff5070c0fb69fa46f27cd3896f069a8

## 1. Purpose and Authority

住民2人が自律的に仕事を取得し、木の隣へ移動し、伐採して木材を発生させ、次の仕事へ進む最小の連続した町の活動を検証する。「生活ループ」は空腹・睡眠・住居を意味しない。利用可能な仕事がある間の連続活動を意味し、木の再生による永久運転は要求しない。

目的・Scope・Phase構成・完了条件の正本は[PDD v1.1 §15](../../02-prototypes/prototype-01/pdd.md)。本書へPhase 4固有の挙動、Verification、Acceptance、Evidenceを集約する。[D-024](../../04-records/decision-log.md#d-024--phase-3以降のsystem-spec-authorityとphase-3仕様承認)に従い、承認後は本System SpecをPhase 4の唯一のNormative Implementation Authorityとする。新規Implementation Handoff、追加仕様文書、形式的なImplementation Start PermissionをGateとして要求しない。Legacy Handoffを実装判断へ使用しない。

本仕様はユーザー提示のPhase 4確定事項を詳細化する。文書の承認、Draft PRの提出、PRのMerge、Unity実装開始、実装Verificationは別の状態である。今回の作業は仕様文書作成のみで、Unity実装・Unity PR変更・実装テスト実行を含まない。

Approval status: 今回の「Approved System Spec作成」指示を仕様承認として扱うか確認中。既存の新規Spec Draft運用に従い、回答前のApproved／Permitted変更は保留する。本文の整合確認は正式なSpecification Review PASSを代行しない。

## 2. Dependencies and Verified Baseline

| Source | 継承・境界 |
|---|---|
| [GDD v1.1](../../01-gdd/gdd.md) | 自律的な住民と町の活動。完成版ゲーム体験を変更しない |
| [PDD v1.1](../../02-prototypes/prototype-01/pdd.md) | §15のGather、§16の将来Haulとの境界、§19の再現可能なPrototype検証 |
| [Phase 1 Spec v2.2](./phase-01-foundation.md) | logical Grid／Cell、Camera／Input、Simulation Time |
| [Phase 2 Spec v1.1](./phase-02-pathfinding-and-movement.md) | A*、8方向、直交1／斜め√2の経路コスト、Corner Cutting禁止、Movement |
| [Phase 3 Spec v1.0](./phase-03-task-system.md) | Creation Order、Assignment、Target Reservation、FSM、Lifecycle／Cleanup |
| [System Specs運用](../README.md) | Phase別Authorityと承認状態 |
| [Roadmap](../../02-prototypes/prototype-01/roadmap.md)・[Open Questions](../../02-prototypes/prototype-01/open-questions.md) | 進捗と未決定事項。詳細仕様の重複管理をしない |
| [Decision Log](../../04-records/decision-log.md)・[Changelog](../../04-records/changelog.md) | 横断判断と変更履歴 |
| [Regression Checklist](../../04-records/regression-checklist.md) | Camera／Cell Selection／表示回帰。§16へMapping |

2026-09-26の再開時にもGitHub current mainを再取得し、2026-09-22の基準から変更がないことを確認した。仕様PR [#29](https://github.com/undershot0704/Project-IYASAKA/pull/29)はMerge済みで、仕様mainは上記Base。Unity [PR #6](https://github.com/undershot0704/Project-IYASAKA-Unity/pull/6)もMerge済み、検証済みHEAD `ce147e768c05ec86bb0f6a19303e1abdaaa2cbc7`、Merge commit／Unity mainは上記Baseline。Phase 3の既存Human Verification PASSと提出Automated EvidenceはPhase 3 Spec §18.1を参照し、今回の再実行結果とは扱わない。

Unity mainのPhase03TaskSystem／ResidentTaskExecutorは、Verification Target自身のCellを移動先とし、Target Cellの通行可否を有効性に含め、Interruptで作業進捗を保持している。Gatherは木セルと作業セルを分離し、仕事種別固有の有効性・作業・成功処理を最小限接続する。Phase 3 Verification Taskの既存挙動を一律に置き換えてはならない。

## 3. Scope and Non-Targets

対象は固定Fixture、円形対象範囲、木、Gather、隣接作業セル、Wood Stack、最小検証表示・操作、Phase 1〜3との統合である。

以下は追加しない。

- Haul、Warehouse、resident inventory、Stack merge／split、最大Stack量、重量、複雑な資源管理
- 木こり小屋、動的範囲編集、個別伐採指令UI、再成長、切り株Gameplay、木の種類、道具、職業、作業能力差
- 空腹、睡眠、住居、食料、石材、複数資源、経済、生産UI
- 住民衝突、セル占有、作業セルReservation、経路Reservation、渋滞、回避、道路効果
- 完成版アート／UI／Outline Shader品質、Save/Load、Priority／距離優先／Utility、汎用AI・資源・再評価Framework
- Phase 5以降の先行実装、既存Camera仕様変更、Phase 2 A*／Movementの再実装、無関係なリファクタリング

## 4. Responsibility and Minimal Runtime Data

| 領域 | 責務・最低限のデータ |
|---|---|
| Tree registry | Unique Target ID、固定Cell、有効性、Uncut／Cut。状態の正本はRuntime DataでありGameObjectの有無ではない |
| Gather eligibility | 現在の有効な伐採理由を判定する。Phase 4では固定円形範囲の理由だけ。中心・半径と同一判定を表示にも供給 |
| Gather job generation | Treeごとの未終了Task対応と再生成抑止を管理。新規Taskを既存Task Systemへ登録 |
| Task System | Unique Task ID、Creation Order、Target ID、Task Status、Assignment、終了理由。Lifecycleの唯一の管理者 |
| Reservation System | Tree Target ID → (Resident ID, Task ID)。既存の排他と所有者一致Releaseを利用 |
| Resident FSM／Gather execution | 既存FSM、Current Task、選択Work Cell／Path、Gather Work Elapsed。移動・作業・結果をTask Systemへ通知 |
| Traversability integration | Phase 2の固定地形と立木の通行禁止を合成し、以後の既存経路探索へ供給 |
| Ground resources | Unique Resource ID、Resource Type = Wood、Quantity = 3、Cell。成功元Tree／Taskと対応して一回だけ生成 |
| Verification integration | Fixture、検証操作、読み取り表示、Reset。所有状態をOverlayから直接書き換えない |

IDはシナリオ内で一意とし、Unity Instance ID、表示名、壁時計を識別・順序の正本にしない。Reset前の遅延通知が新Fixtureへ作用しないよう世代または同等の識別を使用する。具体的クラス名・継承構造は固定しない。

## 5. Fixed Verification Fixture and Eligibility

### 5.1 Fixture

- 住民2人を固定セルへ配置済み。通常開始時から直接命令なしで自律取得できる。
- 木8本を重複しない固定セルへ配置し、Uncut／Validで開始する。範囲内6本、範囲外2本。木材0、初期Gather Taskは6個。
- 固定中心とRadius = 8 cells。全木はGrid内、固定地形上は通行可能なセルへ置く。住民開始セルは木・固定障害物と重ねない。
- 通常6対象は両住民から少なくとも1つの8近傍セルへ到達可能な配置。範囲外2本にも到達可能な周辺を設け、「範囲外だから取得しない」を到達不能と区別できる。
- 具体的セル座標・Tree ID順・住民順は実装側が決定し、固定Fixture表として提出する。乱数やHierarchy列挙順に依存させない。Tie-break観察用の同コスト候補と、木セル開通を確認できる経路条件をFixtureに含める。
- 初期構築直後・最初のSimulation更新前の値を記録／表示可能にし、初期6 Taskと自律取得後の状態を混同しない。通常起動の自律開始を手動Start必須へ変更しない。

### 5.2 Circle and eligibility

論理Gridのセル単位平面で、木セル中心Tと固定円中心Cについて `(Tx-Cx)^2 + (Ty-Cy)^2 <= 8^2` を範囲内とする。境界を含む。World上の表示半径は既存Cell Sizeで換算し、Camera／見た目の高さや画面座標を判定に使わない。中心のFixture値を一箇所で保持する。

Phase 4のEligibilityは `Tree.Valid && Tree.State == Uncut && InFixedCircle(Tree.Cell)`。範囲外の木が存在するだけでは仕事を作らない。CutまたはInvalidは非対象。対象木数と輪郭強調はこの同じ結果を使う。

将来は範囲由来、個別指令由来、両方を識別可能にする方向とする。Eligibilityを永続的な一個のboolだけで表し、理由を復元できなくする設計を避ける。Phase 4では固定範囲の理由を識別する最小データ／判定で十分であり、汎用理由Frameworkや未使用の個別指令処理を実装しない。個別指令は将来、範囲外にも成立可能とする。

### 5.3 Exactly one active Gather job and reevaluation

ここで「1 Tree = 1 Gather Job」の数に含むのはAvailable／Assigned／InProgress／Interruptedの未終了Taskである。Completed／Failed／Cancelledの履歴は含まない。初期化では6対象へ固定Tree順で正確に1つずつ登録し、Phase 3 Creation Orderを付ける。同一Treeに未終了Taskがある間、再評価・Interrupt・毎フレーム更新から2個目を作らない。

失敗した古いTaskは終端のまま保持し、自動復活させない。木が残るFailedや検証取消／DisabledによるCancelledの後は、そのTreeの新規生成を抑止する。住民は他のAvailable Taskへ進む。Eligibility自体は変えず、表示には「対象／新規生成は再評価待ち」を区別する。

Phase 4の再評価条件は、検証専用の明示的な「そのTreeを再評価」操作と完全Resetとする。再評価操作一回で、Valid／Uncut／有効理由あり／未終了Taskなしを再検査し、成立すれば新ID・新Creation Orderで1 Taskだけ作る。失敗注入は一回のコマンドであり持続フラグにしない。毎フレーム、失敗直後、他の木の開通だけで抑止解除しない。条件不成立なら生成せず理由を表示する。これで正常対象の再利用を検証し、生成→即失敗の無限ループを防ぐ。

## 6. Acquisition, Reservation and Work Cell

住民の処理順とAvailable Taskの選択はPhase 3の固定住民順／Creation Orderを継承する。距離が近い木へ優先順位を変更しない。Task AssignmentとTree Target Reservationの取得を一体で成功させ、途中失敗時に片方だけ残さない。他Owner確保中のTargetを奪わず、既存規則に従い次候補へ進む。

取得した木に対して、既存Movementが扱う現在位置／Current Cellから、木の8近傍にあるGrid内・通行可能セルへの経路を既存A*で求める。到達可能な候補の**経路総コスト**が最小のWork Cellを選ぶ。直線距離やマンハッタン距離のみで代用しない。同コストではlogical Cellのx昇順、次にy昇順をTie-breakとする。同じ入力・通行状態なら同じ結果にする。すでに候補セルにいる場合のコストは0。候補間の経路コスト比較には既存経路計算と一貫した数値比較を使用する。

候補がない／全候補到達不能ならTaskをFailed（到達不能理由）としCleanup、木材なし、木はUncutのまま、§5.3の再評価待ちへ進める。検証Fixtureの通常6対象では発生させず、自動テスト用条件で検証する。

Tree Target ID／Tree CellとWork Cellを混同しない。Reservation対象は木であり、隣接セルではない。住民は木セルへ入らず、Work Cell到着後にWorkingへ進む。同じWork Cellを別の木の住民が使うことは許容し、占有・衝突処理を追加しない。Debug Cell SelectionはTask Target／Assignment／Reservationを書き換えない。

## 7. Traversability Integration

Phase 2の固定地形設定と妥当性検査は維持する。Phase 4では `既存地形が通行可能 && 有効な立木がない` を経路探索へ反映する最小拡張を行う。Phase 2の「固定」前提はPhase 2 Fixtureの仕様として維持し、全体へ汎用動的地形編集を導入しない。

Uncut／Validの木セルは、範囲内外を問わず通行不可。成功後は当該木の通行禁止だけを除去する。Fixtureでは元の固定地形が通行可能なので木セルが通行可能になる。固定地形の障害物まで解除しない。変更後の全ての新規探索が更新状態を読み、古い通行可否Cacheを使わないこと。既存A*のCorner Cutting規則も同じ更新状態に従う。

他住民が有効な経路を移動中なら、通行可能セルが増えたことだけを理由に強制再探索しない。Target自体の無効化は別であり§10を適用する。Resetでは移動を止めてから立木を復元する。Wood Stackは通行禁止に寄与しない。このGround Woodの規則はPhase 4の出力契約であり、Phase 5の倉庫・運搬先・建物の通行規則を先行確定しない。

## 8. State, Time and Successful Gather

### 8.1 Lifecycle and time

Task StatusとResident FSMはPhase 3を継承する。Gatherの表示名「伐採中」は既存Workingに対応し、新しい共通FSM状態を増やさない。

`Idle → Acquiring → Moving → Working → Complete → Idle → 次Task取得` を基本とする。失敗はFailed状態／Cleanupを経てIdleへ戻る。検証コマンドをSimulation更新前に適用する順序、到着フレームの移動deltaを作業へ再使用しないこと、終端後Idleを経由して次回取得することもPhase 3に従う。

Gather Duration = **3 Simulation seconds**。正のSimulation deltaだけをWork Elapsedへ一回加算し、3秒到達前に成功させない。Normal = 1x、現行PrototypeのFast = 4x。倍率をGather側で再乗算しない。Fast時の作業部分は概ね0.75実秒であり、移動・フレーム境界時間は含めない。数値はPrototype仮値であり完成版Balanceではない。

PauseはSimulation delta = 0とし、移動・伐採進捗・通常取得を停止する。Assignment／Reservation／進捗を保持し、Task Interruptへ変換しない。2秒でPauseした場合、解除後の残りは1 Simulation秒。Camera／検証表示は既存どおり使用でき、明示的検証コマンドは通常Simulationの停止と区別して処理する。

### 8.2 Success and resource integrity

Working中、同じTaskが未終了で正しいAssignment／Reservationを持ち、有効なUncut Treeと伐採理由、正しいWork Cellを確認でき、3秒に達した場合だけ成功する。無効化・取消・Interruptが同じ更新で先に受理されていれば成功処理を実行しない。

一回の成功処理として以下を整合させる。

1. Treeの論理状態をCutへ変更する。
2. 木の立木表示・対象輪郭を消す。
3. Treeセルの通行禁止を除去し、以後の探索へ反映する。
4. 元Tree CellにWood Stackを**1個、Quantity = 3**生成する。
5. TaskをCompletedとし、Assignment／Reservation／実行ContextをCleanupする。
6. 住民はIdle経由で次のAvailable Taskを探す。

成功コールバック重複、同フレームの複数通知、終端Taskへの再通知から資源を増殖させない。成功元Tree／TaskとStackの対応を一意にし、一連の更新の途中状態を次の取得・表示に確定済みとして公開しない。3個の個別資源Objectを生成しない。

Stackは地面に残る。Resource ID、Type、Quantity、Cellを読み取れるようにし、住民所持やHaul Taskを作らない。正常に6本完了するとStack 6個／総Wood 18、対象木0、未終了Gather Task 0、住民2人Idle、範囲外2本Uncutで安定する。終端Task履歴を「残り仕事」と表示しない。

## 9. Interrupt and Resume

Moving／Workingの真のInterruptでは、Phase 3どおり**Assignmentと元Taskを保持し、Tree Target Reservationを解放**する。Gather固有の規則としてWork Elapsedを0にする。木はUncut、木材なし。Movingの位置・未完了移動区間・再開Contextは既存規則を維持する。Pauseには適用しない。

Resumeは同じTask／Treeの有効性・理由を再検査してReservationを再取得する。別Ownerが確保していればInterruptedのまま保持し、強奪・自動連続Retryをせず、既存の明示Resumeを使う。Movingなら保存した移動から、Workingなら元の有効な隣接作業位置から再開し、伐採は0から3秒を必要とする。到達条件やTargetが無効なら対応するFailed／Cancelledへ進みCleanupする。

Interrupted Taskは未終了なので、Reservationが空でも同じ木へ新規Gather Taskを生成しない。Phase 4での「中断後の対象再利用」はこの同じTaskをResumeして完了できることで確認する。他住民へのAssignment移譲・途中進捗引継ぎを追加しない。Phase 3 Verification TaskのInterrupt進捗保持は変更せず、仕事種別の作業進捗規則だけを分ける。

## 10. Failure, Cancellation and Disabled

| 契機 | Task結果 | 木・資源 | Cleanup／次の活動 |
|---|---|---|---|
| Moving中に木が消失／無効 | Failed（Target Invalid） | Cut扱いにせずWoodなし | 移動停止、全所有解放、Idle後別Taskへ。無効対象は再利用しない |
| Working中に木が消失／無効 | Failed（Target Invalid） | 成功と同じフレームでも無効化優先、Woodなし | 作業停止、全所有解放、Idle後別Taskへ |
| 木を残す強制失敗／到達不能 | Failed | Uncut、Woodなし | 全所有解放、進捗0、生成抑止、別Taskへ。§5.3で新Taskとして再利用 |
| Interrupt | Interrupted | Uncut、Woodなし | Assignment保持、Reservation解放、Gather進捗0。§9でResume |
| 検証取消／将来すべての理由喪失 | Cancelled | Uncut、Woodなし | 移動／作業停止、全所有解放、進捗0、Idle後別Taskへ |
| Resident Disabled | Cancelled（ResidentDisabled） | Uncut、Woodなし | Phase 3どおり全所有・移動・進捗を解放。Disabled中は取得しない |

消失をGameObject参照nullだけで成功判定しない。検証無効化はTree registryへ明示的に反映し、未終了Taskは有効性検査でFailedへ進む。Interrupted／Availableも無効Targetを再利用しない。消失した木の表示・対象輪郭・木由来の通行禁止は除去するが、Cut／Wood生成へ変換しない。Reset以外で無効木を復活させない。

終端処理は冪等とし、Task ID／Owner一致を確認して解放する。古いTaskからの遅延Releaseが新TaskのReservationを解除してはならない。Disabled解除でCancelled Taskを復活させず、木の再生成は§5.3に従う。

将来、範囲変更等で全ての伐採理由を失えばCancelled、別の有効理由が残れば継続する。木の消失Failedと区別する。Phase 4では範囲を動かす機能や個別指令を作らず、取消経路は検証専用コマンド／最小テスト入力で確認する。

## 11. Presentation and Verification Controls

### 11.1 Readable scene and overlay

円の境界と中心を地面上で視認可能にし、Radius 8と表示する。Eligibilityと同じ中心・半径・判定結果から、現在対象の木の外周を黄色系等の輪郭で強調する。木全体の色置換を基本表現にしない。範囲外2本、Cut、Invalidに対象輪郭を付けない。再評価待ちのUncut対象木はEligibilityを維持するため輪郭を維持し、別途待ち状態を示す。

仮3Dの木とStackでよいが、セル位置、立木／消失、内外、Wood存在／Quantityを判別可能にする。木材は「Wood ×3」等で表示する。Cameraの角度・Zoom変更でも論理位置と表示を一致させる。

| 表示対象 | 最低限の観察値 |
|---|---|
| Resident | ID、FSM／Enabled、Task ID、Tree ID、Moving／Working／Failed／Interrupted、Work Elapsed／3、Work Cell |
| Tree | ID、Cell、Valid、Uncut／Cut、Eligibility／理由、Reservation Owner、再評価待ち |
| Task | ID、Creation Order、Status、Assigned Resident、終了理由。終端結果を次Task取得後も追跡可能 |
| Wood | Unique ID、Type、Quantity、Cell、成功元Treeとの対応 |
| 全体 | 対象木数、未終了Gather Task数、Stack数、地面Wood総量、Time mode／倍率／Simulation時間 |
| 経路検証 | 選択Work Cell、候補ごとの到達可否・経路コスト／同率時順序、Tree Cellの通行可否 |

### 11.2 Debug controls

少なくとも対象木の無効化／消失、木を残す現在Taskの一回限り強制Failed、完全Resetを用意する。加えて既存のInterrupt／Resume、Cancel／Disabledの検証経路を利用可能にし、§5.3のTree再評価を明示操作として用意する。住民やTreeの検証選択は本編の伐採命令UIではない。

具体的キー／簡易ボタン、検証対象の選び方は実装側が既存Camera・Cell Selection・時間・Phase 1〜3キーと衝突しない形で確定し、操作一覧に記録する。Human Verificationは以下の操作名と提出操作一覧で再現する。選択対象がない／既に終端等の不正コマンドは状態を破壊せず拒否理由を表示する。Debug操作を除けば住民への直接命令は不要とする。

## 12. Full Scenario Reset

ResetはSimulation更新より先に受理し、旧世代の移動・作業・成功通知を停止／無効化する。Task／Assignment／Reservation、進捗、失敗注入・生成抑止を消去し、全Stackを削除する。住民2人を初期位置・Enabled／Idle、木8本をValid／Uncut・元Cellへ戻し、立木セルの通行禁止を復元する。同じ円・内6外2・固定順の初期Task 6個を再構築する。旧世代の通知から木材が再出現しないこと。

Reset対象はPhase 4のシナリオ状態であり、既存のグローバルSimulation時計を巻き戻さない。時間モード／Cameraは既存操作で管理し、Pause中のResetは初期状態で停止する。Normal／Fastへ戻せば通常開始と同じく自律取得が動く。検証記録にはReset時の時間モードを記載する。旧Taskの終端履歴と新Fixtureの現在数を混算しない。

## 13. Codex Implementation Constraints

- Phase 3のTask／Reservation／FSMとPhase 2のA*／Movementを利用する。Gatherの対象有効性、Work Cell、作業進捗方針、成功出力だけを必要最小限に統合する。
- Phase 3の「Target Cellが通行可能」「Target Cellへ移動」をGatherへそのまま適用せず、立木セルを一時的に通行可能へ偽装して回避しない。Phase 3 Verification Taskの規則は維持する。
- Phase 3の共通Interrupt処理を一律進捗Resetへ変えない。Assignment保持／Reservation解放と作業固有進捗を分離する。
- 新Package・Project Settings変更、汎用Framework、将来UI／Haulを追加しない。既存Scene／FixtureはRegression可能なまま保持する。
- 実装後の操作一覧、固定Fixture、実測結果、HEADをCompletion Evidenceへ対応付ける。未実施の検証をPASSとしない。

## 14. Automated Test Requirements

テスト数・クラス名は固定しない。境界・競合・終端・統合を観察可能な振る舞いで検証し、通常Fixtureと失敗注入用条件を分ける。

| ID | 必須検証と期待結果 |
|---|---|
| AT-01 | 円の内／外／境界ちょうどを判定。半径8、境界含む、表示用判定との同一性、無効／Cut除外 |
| AT-02 | 固定Fixtureの住民2・木8・内6外2・木材0・初期6 Task。Reset／再構築で同じ順序 |
| AT-03 | 1 Treeに未終了Taskは最大1、通常初期は正確に1。繰返し評価・Interruptedで重複なし、終端履歴は別計数 |
| AT-04 | 固定住民順／Creation Order、同一Task二重Assignmentなし、Tree Target二重Reservationなし、取得片側失敗の巻戻し |
| AT-05 | 8近傍候補、木セル除外、範囲外／障害／到達不能候補除外。経路コスト最小、0コスト、固定Tie-break |
| AT-06 | 全候補到達不能でFailed／Cleanup／Woodなし。無限再生成なし |
| AT-07 | 3 Simulation秒未満で未完了、到達で一度完了、到着delta二重消費なし、Normal／Fast 4xの同一Simulation挙動 |
| AT-08 | Pauseで進捗・移動・通常取得停止、所有保持、同進捗から再開。倍率二重適用なし |
| AT-09 | Moving／Working InterruptでAssignment保持・Reservation解放・Gather進捗0・Woodなし。Resume再確保、0から3秒、競合時Interrupted維持 |
| AT-10 | 成功だけUncut→Cut、見た目・輪郭除去、木セルの以後の探索へ通行可反映。既存固定障害物不変、他の有効Pathの不要な強制再探索なし |
| AT-11 | 元Tree CellにStack 1個、Wood／Quantity 3／Unique ID、Stackセル通行可。重複成功通知・終端再通知で増殖なし |
| AT-12 | Moving中のTarget消失／無効でFailed、Woodなし、移動停止、全所有解放、他Task取得 |
| AT-13 | Working中のTarget消失／無効でFailed、Woodなし。完了境界と同フレームでも無効化優先、Invalid対象再利用なし |
| AT-14 | 木を残すForced FailedでUncut／Woodなし／Cleanup、他Taskへ。自動再生成なし、明示再評価で新Task、新Creation Order、旧Task終端維持 |
| AT-15 | Cancelled／ResidentDisabledでWoodなし、移動・作業停止、全所有解放。Disabled中取得なし。古いTask再通知が新Ownerを解放しない |
| AT-16 | 検証Cancelの経路で未完了Gatherの取消・No Wood・木のUncut維持を確認。将来の全理由喪失／他理由存続は§10の設計契約として維持し、そのための動的範囲・個別指令・複数理由処理をPhase 4テスト用に先行実装しない |
| AT-17 | Interrupt／Failed／Cancelledに成功出力なし。明示再評価の連打でも重複なし、無効／Cut／外の再評価は仕事を作らない |
| AT-18 | Moving／Working／Interrupted／成功後／失敗後のResetで§12へ復元。旧世代コールバックから状態・木材汚染なし |
| AT-19 | 正常6本完了でStack6／総Wood18／対象0／未終了Task0／両住民Idle／外2本残存。以後の更新で再生成なし |
| AT-20 | Phase 3 Verification Taskの進捗保持、Assignment／Reservation、Cancelled／Disabled／FSM、Phase 2経路・MovementとPhase 1時間／選択Regression |

## 15. Human Verification Procedure

提出されたPhase 4 Scene名・Fixture表・操作一覧・検証HEADを記録する。各独立ケースは完全Reset後に開始し、指定がなければNormal。途中操作には表示Work ElapsedとPauseを使って条件を再現し、Pauseしたまま検証コマンドを適用した場合は明記する。スクリーンショットだけで時間・所有・到達判定を推測しない。

| ID | 再現手順 | 期待結果 |
|---|---|---|
| HV-01 初期 | Scene起動時の初期スナップショットとFixture表を照合。Pauseして全体を観察 | 住民2、Valid Uncut木8、内6外2、木材0、初期Task6。初期記録と取得後を区別 |
| HV-02 自律取得 | Reset後Normalで直接命令せず進め、両住民のTask／Tree／Ownerを観察 | 別の木へ自律取得、同じ木の二重処理なし。Creation Orderと一致 |
| HV-03 隣接移動 | 選択Work Cell・各候補経路コストを確認し到着を観察。同率条件をResetして再確認 | 木セルへ入らず到達可能な最小コスト隣接セル、同率時x→y順で同一結果。目測距離で代用しない |
| HV-04 正常成功 | Working開始からSimulation時刻と進捗を記録し、成功前後を観察 | 3秒前に出力なし、3秒でCut／木消失／Completed、元セルStack1・Quantity3、所有解放 |
| HV-05 連続活動 | 正常Fixtureを外部操作せず6本完了まで進める | 次Taskへ自律移行。最終両住民Idle、対象0／未終了Task0、外2本残存、Stack6／総Wood18。追加更新でも増殖なし |
| HV-06 Pause | 約2秒伐採した時点でPause、実時間を待ち解除 | 同進捗・所有を保持。残り約1 Simulation秒で成功。中断扱いなし |
| HV-07 Fast | ResetしてNormalとFast 4xの作業Simulation時間を比較 | どちらも3 Simulation秒。Fast作業は概ね0.75実秒、結果・数量同一、Overlay 4x |
| HV-08 Interrupt | 約2秒WorkingでInterrupt。所有・Assignment・進捗を確認してResume。Movingでも繰返す | 木残存、Woodなし、Reservation解放／Assignment保持、0から3秒で成功。同Taskを再利用、Moving位置継続 |
| HV-09 木を残す失敗 | Working中にForced Failed、数更新待って他Taskへの移行を確認。そのTreeを明示再評価 | Uncut・Woodなし・所有解放・勝手な再生成なし。新Taskで再利用し成功できる。旧TaskはFailedのまま |
| HV-10 対象消失 | Moving中に対象木を無効化。ResetしWorking中でも実施 | 両方Failed、Completedでない、対応Woodなし、Cleanup後別Taskへ。無効木再利用なし |
| HV-11 範囲表示 | 円中心／半径表示とFixtureを照合、内6／外2の輪郭を確認。Cameraを動かして再確認 | 円・Eligibility・輪郭が一致。外2本に輪郭なし。再評価待ちと非対象を区別 |
| HV-12 木材と通行 | 成功後に元木セルの通行可否を表示で確認し、既存経路探索の検証操作／統合テストで同セルを通る経路を確認。複数成功後にStack一覧照合 | 立木時は通行不可、成功後・Wood存在時は通行可。1本1Stack×3、ID／Cell／総量整合。新Haulは発生しない |
| HV-13 Reset | Moving／Working／Interrupted／失敗・資源生成後にResetし初期スナップショットを比較 | §12の完全復元、残留所有・資源・進捗・失敗状態なし。同じ固定順で再開。Pause中は解除後自律取得 |
| HV-14 Console／負荷 | 以上の連続操作と全対象完了後を通してConsoleと応答を観察 | Error 0／Warning 0、入力遅延・処理停止・著しい負荷問題なし |
| HV-15 Regression | §16に従いPhase 1〜3 SceneとChecklistの影響範囲を実施し項目別結果を記録 | Camera／Cell Selection／Pathfinding／Pause・Fast／Movement／取得・Reservation・FSMが既存仕様どおり |
| HV-16 Cancel／Disabled | 取得中Taskを検証Cancelし、Reset後はMoving／Working中ResidentをDisabledにする | 木残存・Woodなし・所有解放、Cancelled理由を区別。Disabled中再取得なし、他住民は活動継続 |

HV-12の経路確認は既存検証機能または提出された再現可能な最小診断を使い、本編の住民直接命令や新しい経路編集UIを追加しない。Humanによる目視とAutomatedの論理確認を別Evidenceとして対応付ける。

## 16. Regression Mapping

| 既存正本 | Phase 4での確認 |
|---|---|
| Regression Checklist §3〜5 | 全Camera可動域、Grid／Path／Marker／Resident表示、visible Cellと選択logical Cell一致、Pitch下限×Zoom上限、代表Yaw、Pan／WASD後、範囲外拒否、Click／Drag分離 |
| Phase 1 SpecのCamera／Input／時間 | Pause中Camera操作、SelectionがTaskを変えない、Normal／Pause／Fast、単一Simulation倍率 |
| Phase 2 SpecのPathfinding／Movement | 8方向・Corner Cutting禁止・到達不能・Same Cell・区間移動。固定地形Fixtureを維持し、Phase 4木状態更新のみ追加 |
| Phase 3 Spec §14〜18 | 一人Lifecycleと二人競合、Creation Order、Assignment、Target Reservation、Interrupt進捗保持、Cancel／Disabled解放、終端後の次取得 |

既存Checklistの過去PASSをPhase 4実施済みとして転記しない。影響項目・実行Scene・HEAD・実結果・未実施理由を記録する。Gatherで進捗Resetとなる新要件と、Phase 3 Verification Taskで保持される既存要件を両方検証する。

## 17. Acceptance Mapping

PDD §15の6完了条件を変更せず、以下へ対応付ける。

| PDD完了条件 | 本書 | Verification |
|---|---|---|
| 木からGatherが発生する | §5・§6 | AT-01〜04、HV-01・02・11 |
| 住民が木まで移動して採取できる | §6〜8 | AT-05〜08、HV-03・04・06・07 |
| 同じ木を二人が重複処理しない | §5.3・§6・§9 | AT-03・04・09、HV-02・08 |
| 採取後に木材アイテムが生成される | §7・§8.2 | AT-10・11・19、HV-04・05・12 |
| 失敗または中断後に対象を再利用できる | §5.3・§9・§10 | AT-09・14・17、HV-08・09。消失対象は再利用対象外 |
| 失敗後も別の仕事を継続できる | §8・§10 | AT-12〜15、HV-09・10・16 |

## 18. Completion Evidence

実装完了時には検証対象Unity HEAD、Scene、固定Fixture表、操作一覧、実行環境、Automated Testの実行数／Passed／Failed／Skipped、Compiler Error／Warning、Human Verificationの各IDの条件・期待値・実結果・判定、Console Error 0／Warning 0、Regression結果を記録する。失敗・未実施・既知の問題を隠さない。

少なくとも以下を追跡可能にする。

| Evidence | 対応 |
|---|---|
| 初期状態、円形範囲、範囲内輪郭／範囲外非強調 | HV-01・11、AT-01・02 |
| 住民2人が別の木へ向かう、伐採中、正常伐採後 | HV-02〜04 |
| Stack Quantity 3、元木セル通行可、Woodセル通行可 | HV-04・12、AT-10・11 |
| Interruptと0からResume、木を残すFailedと新Task再利用 | HV-08・09、AT-09・14 |
| Moving／WorkingのTarget消失Failed、全非成功経路のNo Wood | HV-10・16、AT-12〜17 |
| Reset、全6本終了、外2本残存、Stack6／総Wood18 | HV-05・13、AT-18・19 |
| Console／Compiler、負荷観察、Phase 1〜3 Regression | HV-14・15、AT-20、§16 |

[D-016](../../04-records/decision-log.md#d-016--completion-evidenceの視覚的証跡を任意とする)を維持する。ユーザーの今回の確認回答により、初期状態を含めスクリーンショット・動画・GIFは任意。画像の未提出だけをBlockerにせず、上記の状態・操作・結果はHuman Verification記録で追跡する。画像は補助証跡として対応IDとHEADを付けられる。現在の実装・Automated／Human VerificationはNot Started／Not Runであり、本仕様の作成をPASS証跡として扱わない。

## 19. Consistency and Remaining Questions

| 確認対象 | 整合結果 |
|---|---|
| Phase 3 Interrupt AssignmentとGather進捗Reset | §9でAssignment保持・Reservation解放を継承し、作業進捗だけGather固有化 |
| 失敗／中断後の再利用 | 有効失敗は明示再評価から新Task、中断は同TaskのResume。消失木は除外 |
| Failed再生成ループ | §5.3で生成抑止と一回の明示再評価。古い終端Taskを復活させない |
| Phase 2固定Traversability | §7で固定地形を維持し立木層のみ反映。既存Phase 2仕様を変更しない |
| Wood通行と将来Logistics | Ground Woodは通行可。倉庫／Haul／所持／将来搬入規則は未実装 |
| OutlineとEligibility | §5・§11の同一中心・半径・判定結果。別ロジックにしない |
| Phase 5境界 | 出力は地面Wood Stackのみ。運搬仕事を生成しない |
| 固定範囲と将来複数理由 | §5.2・§10で方向のみ保持し、将来機能・汎用Frameworkを作らない |

木数・内外・半径・作業時間・木材量は今回決定済みのPrototype仮値であり、未決定として扱わない。具体的Fixture座標、非衝突キー、仮表示方法は上記を満たす実装裁量であり、新しいゲームデザインOpen Questionではない。新たに機械的解消ができないScope／Gameplay判断が発生した場合は「弥栄企画壁打ちチャットで判断すべき事項」として該当変更を保留する。
