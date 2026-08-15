# Project IYASAKA — Decision Log

Status: Draft  
Last Updated: 2026-08-15  
Owner: Project IYASAKA  
Implementation Use: Prohibited  

## Purpose

Project IYASAKAで承認された重要な判断を、GitHub上で追跡可能な形で記録する。

## Decisions

### D-001 — GitHubをSingle Source of Truthとする

- Date: 2026-07-25
- Status: Adopted
- Decision: Project IYASAKAの正式仕様と文書履歴のSingle Source of TruthをGitHubへ変更する。

### D-002 — Notionを補助用途とする

- Date: 2026-07-25
- Status: Adopted
- Decision: Notionは補助用途として使用し、正式仕様の正本にはしない。

### D-003 — PDDとSystem Specを分離する

- Date: 2026-07-25
- Status: Adopted
- Decision: PDDは「何を作るか」を管理し、内部挙動、アルゴリズム、状態遷移、データ構造、実装方法はSystem Specで管理する。

### D-004 — 旧統合PDDをArchiveとして保存する

- Date: 2026-07-25
- Status: Adopted
- Decision: 旧統合PDDは本文を変更せずArchiveへ保存し、Activeな実装要件として使用しない。

### D-005 — Prototype 01 PDDを再構成する

- Date: 2026-07-25
- Status: Adopted
- Decision: Prototype 01 PDDを、目的、検証仮説、Scope、Out of Scope、Gameplay Loop、Phase、完了条件、Playtest、Go / Revise / Pivotを中心とする文書へ再構成する。

### D-006 — Active Phaseを明示して運用する

- Date: 2026-07-25
- Status: Adopted
- Decision: PrototypeではActive Phaseを明示し、未承認または後続Phaseの実装を先行しない。

### D-007 — System Specは実装直前に作成する

- Date: 2026-07-25
- Status: Adopted
- Decision: System Specは対象Phaseの実装直前に、必要な範囲だけ作成する。DraftのSystem Specは実装判断に使用しない。

### D-008 — 統治を領主権限・課題・布令へ再構成する

- Date: 2026-07-26
- Status: Adopted
- Related PR: [#6 — docs: rework lord authority system](https://github.com/undershot0704/Project-IYASAKA/pull/6)
- Merge Commit: [`1d73ec77d0d53a6027b44841dcd6a677883f4291`](https://github.com/undershot0704/Project-IYASAKA/commit/1d73ec77d0d53a6027b44841dcd6a677883f4291)
- Source Head: `8f93aade325b79b76b5b0ba8c01fee2b1156e1dd`

#### Decision

- 「間接統治」を独立した主要ゲームシステムとして扱わない。
- プレイヤーは町の構造と発展へ直接介入し、住民は町の仕組みに従って自律行動する。
- 政治・政策を独立したゲームシステムとして扱わず、次の三概念へ再構成する。
  - 上位領主からの課題
  - 領主権限
  - 布令・施策
- 上位領主からの課題は町の作り方を指定するミッションではなく、発展段階を評価する節目とし、達成方法はプレイヤーへ委ねる。
- 町を成立させる基本行動は、上位領主からの課題によってロックしない。
- 個別の建物、産業、技術、交易先は、町の発展や研究によって解放される可能性を残す。
- 基本的な税率設定は、ゲーム開始時から持つ領主の基本権限とする。
- 課題達成によって解放されるのは、追加的・補助的な財政施策および領主施策とする。
- 布令・施策は一時的かつ状況依存とし、トレードオフを持たせる。
- 施策は町づくりを代替せず、第一の問題解決手段にしない。
- 施策の主な役割は、発展促進、危機対応、移行支援とする。
- 恒常的な農業方針、交易方針、産業方針などの政策システムは採用しない。
- 委任・自動化はEarly Access対象外のBacklog候補とする。

#### Rationale

- 町づくり、物流、生産、交易をゲームの主役として維持するため。
- 政策メニューによる問題解決が町の設計を代替することを防ぐため。
- プレイヤーの領主としての立場を残しつつ、統治ゲーム化を避けるため。
- ストーリーと段階的な権限解放を接続するため。
- Early Accessまでの開発スコープを抑えるため。
- 将来の委任や自動化の可能性を否定せず、現段階では保留するため。

#### Alternatives Not Adopted

- **独立した政治・政策システム:** 町づくり本体と問題解決経路が競合するため不採用。
- **恒常的な政策バフ:** 町の構造改善よりメニュー選択が優先される危険があるため不採用。
- **基本機能を父親の許可で解放する方式:** 序盤の町づくりを不自然に制限するため不採用。
- **Early Access段階からの委任・自動化:** 現時点では必要性を検証できず、開発範囲を拡大するため保留。

### D-009 — GDD v1.0をActiveとして正式承認する

- Date: 2026-07-26
- Status: Adopted
- Related PR: [#8 — docs: activate GDD v1.0](https://github.com/undershot0704/Project-IYASAKA/pull/8)
- Merge Commit: [`2c7800123c809819865fecfcf01a08932f2d2484`](https://github.com/undershot0704/Project-IYASAKA/commit/2c7800123c809819865fecfcf01a08932f2d2484)
- Source Head: `dcb85dc1f2b001570e0c501828b8cc9b51fabb53`

#### Decision

- GDD v1.0を正式承認する。
- GDDのStatusを`Active`とする。
- GDDのVersionを`1.0`とする。
- GDDのApprovedを`Approved`とする。
- GDDのImplementation Useを`Allowed`とする。
- GDDをゲームデザイン上のSingle Source of Truthとする。
- PDDは引き続きDraftとして扱う。
- PDD、System Spec、Implementation Handoffの承認前にUnity実装を開始しない。

#### Rationale

- GDD全文レビューおよび最終確認でBlocking Issueがなかったため。
- 主要仕様とゲームデザイン方針が確定したため。
- 今後のPDDおよび実装設計の基準を固定する必要があるため。

### D-010 — Prototype 01 PDD v1.0を正式承認しActive化する

- Date: 2026-07-27
- Status: Adopted

#### Decision

- Prototype 01 PDD v1.0を正式承認する。
- PDDのStatusを`Active`とする。
- PDDのVersionを`1.0`とする。
- PDDのApprovedを`Approved`とする。
- Phase Statusを`Awaiting Phase 1 System Spec`とする。
- Implementation Useを`Allowed for Specification`とする。
- PDDをPhase 1 System SpecおよびImplementation Handoff作成の正式な入力とする。

#### Context

- GDD v1.0はすでにActiveかつApprovedである。
- PDD v1.0-rc1の最終レビューでBlocking Issueがないことを確認した。
- PR #10で承認前の状態整理を完了した。
- PR #11でPDD v1.0を正式にActive化した。
- Phase 1 System SpecとImplementation Handoffは未作成である。
- Unity実装開始条件はまだ満たしていない。

#### Consequences

- Phase 1 System Specの作成、レビュー、承認へ進める。
- PDDを仕様設計の正式な基準として使用できる。
- PDDのActive化だけではUnity実装を開始できない。
- Unity実装には承認済みのPhase 1 System SpecとImplementation Handoffが必要である。
- 後続Phaseを先行実装しない。

#### Traceability

- PR #10: [docs: finalize Prototype 01 PDD v1.0](https://github.com/undershot0704/Project-IYASAKA/pull/10)
- PR #10 Merge Commit: [`d08d431dfe9f458905fd14fd8ac2d498d2aee23a`](https://github.com/undershot0704/Project-IYASAKA/commit/d08d431dfe9f458905fd14fd8ac2d498d2aee23a)
- PR #10 Source Head: `37664a9edd4c4a7db174019b13483a99e708e586`
- PR #11: [docs: activate Prototype 01 PDD v1.0](https://github.com/undershot0704/Project-IYASAKA/pull/11)
- PR #11 Merge Commit: [`5718da2aeff7639e0448bb15d61f34a2faa1eedb`](https://github.com/undershot0704/Project-IYASAKA/commit/5718da2aeff7639e0448bb15d61f34a2faa1eedb)
- PR #11 Source Head: `47bebbad0692e145355924dafc699f6ec5ffcb2f`

### D-011 — Prototype 01 Phase 1 Implementation Handoff v1.0を正式承認する

- Date: 2026-08-02
- Status: Adopted

#### Decision

- Phase 1 Implementation Handoff v1.0を正式承認する。
- Implementation Useを`Permitted`とする。
- Codex用実装プロンプト作成を許可する。
- Unity Implementationは`Prohibited`のまま維持する。
- Unity実装開始にはユーザーによる明示的な開始許可を必要とする。
- 実装開始前に基準Unity Repository HEADを確認する。

#### Context

- GDD v1.0は承認済みである。
- PDD v1.0は承認済みである。
- Phase 1 System Spec v1.0は承認済みである。
- Implementation HandoffレビューでBlocking Issueを解消済みである。
- Open Decisionsはない。
- Unity実装リポジトリは初期化済みである。
- Unity実装は未開始である。

#### Consequences

- Phase 1専用Codex実装プロンプトを作成できる。
- CodexはApproved GDD、PDD、System Spec、Implementation Handoff、およびUnity実装リポジトリの`AGENTS.md`に従う。
- 実装開始前にUnity Repository HEADを再確認する。
- 明示的な実装開始許可を受けるまでUnityファイルを変更しない。
- Phase 2以降を先行実装しない。

#### Traceability

- PR #14: [docs: draft Prototype 01 Phase 1 Implementation Handoff](https://github.com/undershot0704/Project-IYASAKA/pull/14)
- PR #14 Merge Commit: [`e1e36ae977f4bab59c3b14c0e369fac000d4659c`](https://github.com/undershot0704/Project-IYASAKA/commit/e1e36ae977f4bab59c3b14c0e369fac000d4659c)
- PR #14 Source Head: `b4632662864670932b44e454cb31d24415522b74`
- Approved Handoff: [Prototype 01 Phase 1 Implementation Handoff v1.0](../04-implementation-handoffs/prototype-01/phase-01-foundation.md)
- Unity Repository: [undershot0704/Project-IYASAKA-Unity](https://github.com/undershot0704/Project-IYASAKA-Unity)
- Handoff記載の基準Unity HEAD: `8adb90fe0c8ddf8cfcdb035d3e8a09a8b48a2058`


### D-012 — Prototype 01 Phase 1の実装運用事項を確定する

- Date: 2026-08-02
- Status: Adopted

#### Decision

- Phase 1 Implementation Handoffをv1.1へ更新し、Approved状態を維持する。
- Codexは、明示的な実装開始許可後、Phase 1実装、自動テスト、Human Verification、Completion Evidence整理、実装コミット、Draft PR作成まで行い、Draft PR作成後に停止する。
- Draft解除、Ready for Reviewへの変更、PRのMerge、`main`への直接反映、自動Merge設定はCodexが行わない。Ready化とMergeは人間の確認後にのみ行う。
- Camera初期XYはグリッド中心`(32, 32)`、Initial Orthographic Sizeは`10`とする。Zは既存Unity 2D Camera構成に適した値を使用する。
- GameObject名、Scene Hierarchy、Component接続、Game View／Scene Viewの検証表示方式は、Approved仕様を満たす範囲でCodexの最小実装裁量とする。
- 新規Package、外部Asset、完成版UI、汎用UI基盤、将来用フレームワークを追加しない。
- Console Error、未処理例外、無限ログまたは継続的な警告出力は`0`とし、Phase 1実装による新規Warningは原則`0`とする。
- 既存Warningは、実装前後を記録し、増加せず、Phase 1機能を阻害せず、Completion Evidenceへ記載する場合のみ許容する。
- Completion EvidenceへDraft PR URL、実装コミットSHA、Warning状況を含める。

#### Context

- ゲーム機能およびPhase 1 System Scopeは変更しない。
- Unity実装リポジトリの`main` HEADは`7c52d3e2089eb080577f7779c2f5d5e6c42eb95a`である。
- Unity実装リポジトリの`AGENTS.md`は、Draft PR停止、Human Verification、Completion Evidence、Package変更禁止をすでに要求しており、本決定と整合する。
- Unity実装の明示的な開始許可は、引き続き別途必要である。

#### Consequences

- Codex実装プロンプト最終版を作成できる。
- 実装開始時はUnity Repository HEADを再確認する。
- Unity実装はこの文書更新では開始しない。
- Scope外変更または既存WarningのScope外修正が必要な場合は停止して報告する。

#### Traceability

- Updated Handoff: [Prototype 01 Phase 1 Implementation Handoff v1.1](../04-implementation-handoffs/prototype-01/phase-01-foundation.md)
- Previous specification main HEAD: `2a462b60a10125ac2eca42957f771313ecc42314`
- Confirmed Unity main HEAD: `7c52d3e2089eb080577f7779c2f5d5e6c42eb95a`


### D-013 — Phase 1のGame View GridとPause／Resume Toggleを正式採用する

- Date: 2026-08-02
- Status: Adopted

#### Decision

- Phase 1ではGame Viewでもグリッド外周とセル境界を視認可能にする。
- Game View GridはCamera移動とZoomに追従し、Start CellとDestination Cellを別セル・同一セルのどちらでも識別可能にする。
- Game View Gridは完成版描画ではなく、Phase 1専用の最小検証表示とする。
- SpaceはPause専用ではなく、Pause直前の非Paused状態へ復帰するPause／Resume Toggleとする。
- 直前状態として保持するのはNormalまたはFastとし、新しいSimulation Time状態は追加しない。
- 復帰先が未定義の場合はNormalへ安全に復帰する。

#### Context

- Human VerificationでScene ViewのGrid、Camera操作、既存Time入力、Start Cell、Verification Display、Console状態は確認済みである。
- Game Viewが黒背景のみでは、検証者が空間とセル位置を直感的に把握しにくかった。
- SpaceによるPause後は、Pause前の速度へ戻る操作が検証効率と操作整合性を高める。

#### Consequences

- Phase 1 System Specをv1.1、Implementation Handoffをv1.2へ更新し、Approved状態を維持する。
- Unity Draft PR #2にはGame View Grid表示、Pause／Resume Toggle、自動テスト、Human Verification、Completion Evidenceの追加修正が必要になる。
- Prototype ScopeおよびPhase Scopeは変更しない。
- Unity実装は本決定の文書更新では開始しない。

#### Traceability

- Updated System Spec: [Prototype 01 Phase 1 System Spec v1.1](../03-system-specs/prototype-01/phase-01-foundation.md)
- Updated Handoff: [Prototype 01 Phase 1 Implementation Handoff v1.2](../04-implementation-handoffs/prototype-01/phase-01-foundation.md)
- Previous specification main HEAD: `5b21d98be1bf9b61b4e88492152893639cc4203b`


### D-014 — Phase 1のMouse Wheel ZoomとLeft Click／Drag Camera操作を正式採用する

- Date: 2026-08-02
- Status: Adopted

#### Decision

- Phase 1のMouse Wheel Zoomは、標準的な約3刻みでZoom Minimum `4`とZoom Maximum `24`の全範囲を双方向に移動可能とする。
- 1標準刻みあたりのZoom Units Per Notchは`20 / 3`（約`6.67` Orthographic Size Units）とし、Mouse Scroll Yを標準刻みへ正規化して最終値を`4`〜`24`へClampする。
- Left Mouse Buttonは、Drag Threshold未満のClickでStart Cellを指定し、Threshold超過後のDragでCamera Panする。
- Click／DragはPixel基準のDrag Thresholdで区別し、初期値を`8 pixels`、Human Verification調整目安を`5`〜`10 pixels`とする。
- Drag操作ではStart Cellを変更せず、既存のStart Cell／Destination Cellを維持する。
- WASD／Arrow Keys移動とRight ClickによるDestination Cell指定は維持する。
- Camera操作はSimulation Time倍率から独立し、Paused中も使用可能とする。

#### Context

- 最新のUnity Draft PR #2に対するHuman Verificationでは、Grid、Camera追従、Start／Destination表示、Pause／Resume、Elapsed Time、Paused中のCamera操作、Console、描画負荷に問題がないことを確認した。
- 現行のMouse Wheel Zoomは全範囲の移動に対して遅く、観察範囲を素早く切り替える操作感へ改善する必要がある。
- Keyboard移動を維持しつつ、マップを直接掴むLeft Drag Panを追加すると、固定俯瞰マップの観察性を高められる。
- Left ClickのStart Cell指定と競合するため、Pixel基準Thresholdによる一意なClick／Drag判定が必要である。

#### Consequences

- Phase 1 System Specをv1.2、Implementation Handoffをv1.3へ更新し、Approved状態を維持する。
- Unity Draft PR #2にはCamera Controller、既存Input Actions、Bootstrap、Verification Display、自動テスト、Human Verification、Completion Evidenceの修正が必要になる。
- 新規Package、Project Settings変更、Camera境界、Camera慣性、汎用入力／Camera Frameworkは追加しない。
- GDD、PDD、Prototype ScopeおよびPhase Scopeは変更しない。
- Unity実装は本決定の文書更新では開始しない。

#### Traceability

- Updated System Spec: [Prototype 01 Phase 1 System Spec v1.2](../03-system-specs/prototype-01/phase-01-foundation.md)
- Updated Handoff: [Prototype 01 Phase 1 Implementation Handoff v1.3](../04-implementation-handoffs/prototype-01/phase-01-foundation.md)
- Previous specification main HEAD: `6bb64c85afdab35bf225572aca812815b2824e31`

### D-015 — Phase 1のF1をVerification Overlay専用Toggleとする

- Date: 2026-08-02
- Status: Adopted

#### Decision

- Game Viewでは、Grid外周、セル境界、Start Cell、Destination Cell、同一Cell表示を常時表示する。
- `F1`はGame View左上のVerification OverlayだけをON／OFFする。
- Verification Overlayには、Grid設定・状態、Camera状態、Simulation Time状態・倍率・Elapsed Time、World／Cell Position、Start／Destination座標、Invalid操作結果、その他Debug情報を表示する。
- Verification OverlayをOFFにしても、Game View GridおよびStart／Destinationの表示状態を変更しない。
- ゲームループ、Simulation、Camera、Input Binding、Prototype Scope、Phase Scopeは変更しない。

#### Context

- Human Verificationにより、Grid外周、セル境界、Start Cell、Destination Cell、同一Cell表示はPhase 1の検証対象そのものであり、Debug情報の可視性とは独立して確認できる必要があると判断した。
- 現行Handoffの「Game View検証表示をF1で切替」という表現では、Game View Gridを含む表示全体が切替対象と解釈できるため、F1の責務境界を明確化する必要がある。

#### Consequences

- Phase 1 System Specをv1.3、Implementation Handoffをv1.4へ更新し、Approved状態を維持する。
- Unity Draft PR #2では原則`Phase01VerificationDisplay.cs`だけを修正し、F1の表示切替対象をVerification Overlayへ限定する。
- Camera、Grid、Simulation、Input Bindingの変更は不要とする。
- GDD、PDD、Prototype ScopeおよびPhase Scopeは変更しない。
- Unity実装は本決定の文書更新では開始しない。

#### Traceability

- Updated System Spec: [Prototype 01 Phase 1 System Spec v1.3](../03-system-specs/prototype-01/phase-01-foundation.md)
- Updated Handoff: [Prototype 01 Phase 1 Implementation Handoff v1.4](../04-implementation-handoffs/prototype-01/phase-01-foundation.md)
- Previous specification main HEAD: `7ea13a145bf52155984f123837adb61cb178ec0b`


### D-016 — Completion Evidenceの視覚的証跡を任意とする

- Date: 2026-08-02
- Status: Adopted

#### Decision

- Completion EvidenceではHuman Verificationの実施記録を正式証跡とする。
- スクリーンショット、動画、GIF、その他の視覚的証跡は、Human Verificationを補助する任意の証跡とし、必要に応じて取得する。
- スクリーンショット未取得のみを理由に、Draft解除、Ready for Reviewへの変更またはMergeを拒否しない。
- 自動テスト結果、Console結果、Commit履歴およびGitHub PR履歴は、従来どおり追跡可能な証跡として扱う。

#### Context

- Project IYASAKAはAI主体の個人開発であり、Human Verificationは人間がUnity Editor上で実施して結果を記録する。
- 通常開発では、自動テスト、Console、CommitおよびPRの履歴が残るため、全確認項目にスクリーンショットを必須化する管理コストが得られる利益を上回る。
- 視覚的な再確認や不具合調査に有用な場合は、引き続き任意で視覚的証跡を取得できる。

#### Consequences

- Phase 1 System Specをv1.4、Implementation Handoffをv1.5へ更新し、Approved状態を維持する。
- ゲーム仕様、Prototype Scope、Phase Scope、Acceptance Criteria、Human VerificationおよびAutomated Testは変更しない。
- 新しいBlocking Open QuestionまたはOpen Decisionは追加しない。
- Unity実装内容は変更しない。

#### Traceability

- Updated System Spec: [Prototype 01 Phase 1 System Spec v1.4](../03-system-specs/prototype-01/phase-01-foundation.md)
- Updated Handoff: [Prototype 01 Phase 1 Implementation Handoff v1.5](../04-implementation-handoffs/prototype-01/phase-01-foundation.md)
- Previous specification main HEAD: `4f11c9595b29bc8cfc3b9615c38589f323fa42f3`

### D-017 — Blocked Cell設定異常時にTraversabilityを利用不可とする

- Date: 2026-08-04
- Status: Adopted
- Decision: Blocked Cell設定異常ではSilent Recoveryを行わず、Traversabilityを利用可能状態にしない。

### D-018 — Pathfindingの最小総コストと再現性を保証する

- Date: 2026-08-04
- Status: Adopted
- Decision: Pathfindingは定義済み移動コストに基づく最小総コスト経路を返し、同一最小コスト経路が複数存在する場合も再現可能な結果を返す。

### D-019 — Residentの未配置状態を正式採用する

- Date: 2026-08-04
- Status: Adopted
- Decision: Residentは未配置状態（Placement State）を正式な状態として持つ。

### D-020 — Blenderベースの3D世界とAI主体の制作パイプラインを正式採用する

- Date: 2026-08-15
- Status: Adopted

#### Decision

- Project IYASAKAの世界表現はBlenderベースの3Dを正式採用する。
- AIをテクスチャ、質感、装飾生成へ使用し、Unityで統合する。
- 最終表現は和風絵巻風・手描きタッチとする。
- 標準Production Pipelineを`Concept Art → Blender → AI（質感・装飾） → Unity → Game`とする。

#### Context

- AI主体の一人開発におけるアセット制作コストを下げる必要がある。
- 3D形状は360°Cameraと親和性が高く、建物、住民、季節表現を将来追加しやすい。
- Blenderで形状、AIで質感と装飾を分担すると、再利用性と画風の展開を両立できる。

#### Consequences

- GDDをv1.1へ更新し、視点、Art Direction、Production Pipelineを正式化する。
- 2D固定俯瞰を完成版の基本視点とする旧方針を廃止する。
- 個別のAIモデル、生成設定、ファイル形式は本決定で固定しない。
- Unity Repository、Package、Project Settingsは変更しない。

### D-021 — Prototype 01 Phase 1 Camera v2へPivot Orbitを採用する

- Date: 2026-08-15
- Status: Adopted

#### Decision

- Prototype 01の標準Cameraを3D Orbit対応へ更新する。
- 標準操作はWASD移動、Left Drag Pan、Mouse Wheel Zoom、Right Drag Orbitとする。
- OrbitはPivot方式とし、Yawは360°、Pitchは真上・真下を含まない制限付きとする。
- Camera操作はPaused中も使用でき、Simulation Timeへ依存しない。

#### Context

- Timberbornでのプレイ検証により、町を複数方向から観察できるCameraが3Dの町づくりと相性がよいと判断した。
- Blenderベースの3D世界を正式採用するため、固定方向の2D Camera前提を更新する必要がある。

#### Consequences

- Phase 1 System Specをv2.0へ更新する。
- Camera Collision、Follow、Focus、Building Transparency、Minimap、CinemachineはPhase 1 Camera v2のScope外とする。
- Camera v2のUnity実装は、唯一の正式実装仕様であるPhase 1 System Specに従う。Implementation Handoff更新を要求しない。
- Phase 2／Phase 3仕様、Regression Checklist、Unity Repositoryは変更しない。

#### Traceability

- Updated GDD: [Game Design Document v1.1](../01-gdd/gdd.md)
- Updated System Spec: [Prototype 01 Phase 1 System Spec v2.0](../03-system-specs/prototype-01/phase-01-foundation.md)
- Previous specification main HEAD: `e2365a3835ccd2328ec55139eda1a1c8aa8bbd60`

### D-022 — Phase 1の正式実装仕様をSystem Specへ一本化する

- Date: 2026-08-15
- Status: Adopted

#### Decision

- Prototype 01 Phase 1では、Approved System Specを唯一の正式実装仕様とする。
- Approved System Specが`Implementation Use: Permitted`の場合、Implementation Handoff、追加仕様文書、追加承認Gateまたは形式的な開始許可を実装開始条件としない。
- Phase 1 Implementation Handoff v1.5はLegacy Documentとし、Camera v1までの移行記録としてのみ保持する。
- Camera v2以降はLegacy Handoffを正式仕様、実装判断、停止条件または承認条件として使用しない。
- Phase 1では今後Implementation Handoffを新規作成・更新しない。必要な仕様変更はSystem Specへ統合する。
- 本Decisionは、D-010〜D-016およびD-021に残るPhase 1のHandoff必須、追加許可、停止Gateに関する旧運用を置き換える。旧記述は履歴としてのみ保持する。

#### Context

- Camera v2のApproved System Specと、Camera v1を前提とするImplementation Handoffの間に運用上の競合が残っていた。
- Handoff依存により、System SpecがApprovedでも実装担当が追加Gateを要求して停止する状態を解消する必要がある。
- 仕様Authorityを一文書へ集約し、重複、更新漏れ、移行時の競合を防ぐ。

#### Consequences

- Phase 1 System Specをv2.1へ更新し、`Unity Implementation: Permitted`とする。
- Phase 1 Implementation Handoff v1.5をLegacy化し、本文はCamera v1の履歴として凍結する。
- Scope、Camera仕様、Acceptance Criteria、Verification PlanおよびCompletion Evidenceの内容は変更しない。
- GDD、PDD、Art Direction、Production Pipeline、Phase 2／Phase 3仕様およびUnity Repositoryは変更しない。

#### Traceability

- Authority: [Prototype 01 Phase 1 System Spec v2.1](../03-system-specs/prototype-01/phase-01-foundation.md)
- Legacy Record: [Prototype 01 Phase 1 Implementation Handoff v1.5](../04-implementation-handoffs/prototype-01/phase-01-foundation.md)
- Previous specification main HEAD: `4c8bf5a28bd3eed7af823b0c2cbd8ad7406c42ec`
