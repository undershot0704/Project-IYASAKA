# Project IYASAKA — Decision Log

Status: Draft  
Last Updated: 2026-08-02  
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

