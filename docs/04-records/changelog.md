# Project IYASAKA — Changelog

Status: Draft  
Last Updated: 2026-08-02  
Owner: Project IYASAKA  
Implementation Use: Prohibited  

## 2026-08-02

### Prototype 01 Phase 1 Completion Evidence運用更新

- AI主体・個人開発・完成優先の方針に合わせ、Phase 1 System Specをv1.3からv1.4、Implementation Handoffをv1.4からv1.5へ更新した。
- Human Verificationの実施記録を正式なCompletion Evidenceとした。
- スクリーンショット、動画、GIF、その他の視覚的証跡を任意の補助証跡とした。
- スクリーンショット未取得のみを理由にDraft解除、Ready化またはMergeを拒否しない運用へ変更した。
- 必須EvidenceをCommit SHA、変更／新規ファイル一覧、自動テスト結果、Console結果、Human Verification結果、必要なInspector実測値、Scope外変更なし、既知制限、未解決事項へ整理した。
- Decision LogへD-016を追加した。
- ゲーム仕様、GDD、PDD、Prototype Scope、Phase Scope、Acceptance Criteria、Human VerificationおよびAutomated Testは変更していない。
- Unity RepositoryおよびUnity Draft PR #2は変更していない。

### Specification impact

- GDD変更：なし
- PDD変更：なし
- Prototype Scope変更：なし
- Phase Scope変更：なし
- Acceptance Criteria変更：なし
- Human Verification変更：なし
- Automated Test変更：なし
- System Spec：v1.4へ更新
- Implementation Handoff：v1.5へ更新
- Unity変更：なし

### Prototype 01 Phase 1 Verification Display仕様更新

- Human Verification結果に基づき、Phase 1 System Specをv1.2からv1.3、Implementation Handoffをv1.3からv1.4へ更新した。
- 両文書のStatusを`Approved`、Approvedを`2026-08-02`として維持した。
- Game ViewのGrid外周、セル境界、Start Cell、Destination Cell、同一Cell表示を常時表示とした。
- `F1`の切替対象をGame View左上のVerification Overlayだけへ限定した。
- Automated Test候補、Human Verification手順19、Completion Evidence、Acceptance Mappingを同期した。
- Decision LogへD-015を追加した。
- ゲームループ、Simulation、Camera、Input Binding、GDD、PDD、Prototype ScopeおよびPhase Scopeは変更していない。
- Unity RepositoryおよびUnity Draft PR #2は変更していない。

### Specification impact

- GDD変更：なし
- PDD変更：なし
- Prototype Scope変更：なし
- Phase Scope変更：なし
- System Spec：v1.3へ更新
- Implementation Handoff：v1.4へ更新
- Unity変更：なし

### Prototype 01 Phase 1 Camera操作仕様更新

- Human Verification結果に基づき、Phase 1 System Specをv1.1からv1.2、Implementation Handoffをv1.2からv1.3へ更新した。
- 両文書のStatusを`Approved`、Approvedを`2026-08-02`として維持した。
- Mouse Wheel Zoomを、約3標準刻みでZoom `4`〜`24`の全範囲を双方向に移動できるZoom Units Per Notch方式へ変更した。
- Left Mouse Buttonを、Pixel基準Threshold未満のClickではStart Cell指定、Threshold超過後のDragではCamera Panとして正式採用した。
- WASD／Arrow Keys移動とRight Click Destination指定を維持し、DragではStart Cell／Destination Cellを変更しない。
- Camera操作の自動テスト、Human Verification、Completion Evidence、Acceptance Mappingを更新した。
- Decision LogへD-014を追加した。
- Prototype ScopeおよびPhase Scopeは変更していない。
- PDDは影響確認のみで本文変更していない。
- Unity RepositoryおよびUnity Draft PR #2は変更していない。

### Specification impact

- GDD変更：なし
- PDD変更：なし
- Prototype Scope変更：なし
- Phase Scope変更：なし
- System Spec：v1.2へ更新
- Implementation Handoff：v1.3へ更新
- Unity変更：なし

### Prototype 01 Phase 1 Human Verification仕様更新

- Human Verification結果に基づき、Phase 1 System Specをv1.0からv1.1、Implementation Handoffをv1.1からv1.2へ更新した。
- 両文書のStatusを`Approved`、Approvedを`2026-08-02`とした。
- Game Viewでグリッド外周、セル境界、Start Cell、Destination Cell、同一セル両指定を識別できるPhase 1専用最小表示を正式採用した。
- Spaceを、Pause直前のNormalまたはFastへ復帰するPause／Resume Toggleへ変更した。
- Simulation TimeとGame View Gridの自動テスト、Human Verification、Completion Evidenceを追加した。
- Decision LogへD-013を追加した。
- Prototype ScopeおよびPhase Scopeは変更していない。
- PDDは影響確認のみで本文変更していない。
- Unity RepositoryおよびUnity Draft PR #2は変更していない。

### Specification impact

- GDD変更：なし
- PDD変更：なし
- Prototype Scope変更：なし
- Phase Scope変更：なし
- System Spec：v1.1へ更新
- Implementation Handoff：v1.2へ更新
- Unity変更：なし

### Prototype 01 Phase 1 Implementation Handoff v1.1実装運用確定

- Phase 1 Implementation Handoffをv1.0からv1.1へ更新した。
- Statusは`Approved`、Approvedは`2026-08-02`、Implementation Useは`Permitted`を維持した。
- Codexの終了地点を、実装、自動テスト、Human Verification、Completion Evidence整理、実装コミット、Draft PR作成までとした。
- Draft PR作成後に停止し、Draft解除、Ready化、Merge、`main`への直接反映、自動Merge設定を禁止した。
- Camera初期XYをグリッド中心`(32, 32)`、Initial Orthographic Sizeを`10`とした。Zは既存2D Camera構成に適した値とした。
- Scene Hierarchy、GameObject名、Component接続、検証表示方式に制約内の最小実装裁量を与えた。
- Console Error、新規Warning、既存Warningの完了基準を確定した。
- Completion EvidenceへDraft PR URL、実装コミットSHA、Warning状況を追加した。
- Unity実装リポジトリの`main` HEAD `7c52d3e2089eb080577f7779c2f5d5e6c42eb95a`、`AGENTS.md`、Package構成を確認した。
- ゲーム機能、GDD、PDD、System Spec、Phase Scopeは変更していない。
- Unityファイルは変更しておらず、Unity実装は開始していない。

### Specification impact

- 新たなゲーム仕様変更：なし
- GDD変更：なし
- PDD変更：なし
- System Spec変更：なし
- Implementation Handoff運用事項：更新
- Unity変更：なし

### Prototype 01 Phase 1 Implementation Handoff v1.0承認

- Prototype 01 Phase 1 Implementation Handoff v1.0を正式承認した。
- Statusは`Approved`である。
- Versionは`1.0`である。
- Approvedは`2026-08-02`である。
- Implementation Useは`Permitted`である。
- Unity Implementationは`Prohibited`を維持した。
- Blocking Open DecisionsおよびOpen Decisionsはない。
- Codex Prompt Preparationを`Permitted`とした。
- PR #14をmainへマージした。
- Handoff本文、File Plan、Acceptance Mapping、Human Verification、Completion Evidenceを正式確定した。
- Unityリポジトリは変更していない。
- Unity実装は開始していない。
- 次工程はCodex実装プロンプトの作成と、ユーザーによる明示的な実装開始許可である。

### Traceability

- PR #14: [docs: draft Prototype 01 Phase 1 Implementation Handoff](https://github.com/undershot0704/Project-IYASAKA/pull/14)
- PR #14 Merge Commit: [`e1e36ae977f4bab59c3b14c0e369fac000d4659c`](https://github.com/undershot0704/Project-IYASAKA/commit/e1e36ae977f4bab59c3b14c0e369fac000d4659c)
- PR #14 Source Head: `b4632662864670932b44e454cb31d24415522b74`
- Approved Handoff: [Prototype 01 Phase 1 Implementation Handoff v1.0](../04-implementation-handoffs/prototype-01/phase-01-foundation.md)

### Specification impact

- 新たなゲーム仕様変更：なし
- GDD変更：なし
- PDD本文変更：なし
- System Spec本文変更：なし
- Implementation Handoff本文変更：なし
- Unity変更：なし

## 2026-07-27

### Prototype 01 PDD v1.0 Active化

- Prototype 01 PDD v1.0を正式承認し、Active化した。
- Statusを`Draft`から`Active`へ変更した。
- Versionを`1.0-rc1`から`1.0`へ変更した。
- Approvedを`Pending`から`Approved`へ変更した。
- Phase Statusを`Awaiting PDD Activation`から`Awaiting Phase 1 System Spec`へ変更した。
- Implementation Useを`Prohibited`から`Allowed for Specification`へ変更した。
- Unity Implementationは`Prohibited`を維持した。
- Prototype README、Roadmap、Open QuestionsのPDD状態を同期した。
- PDD仕様本文、Phase構造、Scopeは変更していない。
- GDDは変更していない。
- Phase 1 System Specは未作成である。
- Implementation Handoffは未作成である。
- Unityファイルは変更していない。
- 次工程はPhase 1 System Specの作成、レビュー、承認である。

### Traceability

- PR #10: [docs: finalize Prototype 01 PDD v1.0](https://github.com/undershot0704/Project-IYASAKA/pull/10)
- PR #10 Merge Commit: [`d08d431dfe9f458905fd14fd8ac2d498d2aee23a`](https://github.com/undershot0704/Project-IYASAKA/commit/d08d431dfe9f458905fd14fd8ac2d498d2aee23a)
- PR #10 Source Head: `37664a9edd4c4a7db174019b13483a99e708e586`
- PR #11: [docs: activate Prototype 01 PDD v1.0](https://github.com/undershot0704/Project-IYASAKA/pull/11)
- PR #11 Merge Commit: [`5718da2aeff7639e0448bb15d61f34a2faa1eedb`](https://github.com/undershot0704/Project-IYASAKA/commit/5718da2aeff7639e0448bb15d61f34a2faa1eedb)
- PR #11 Source Head: `47bebbad0692e145355924dafc699f6ec5ffcb2f`
- Changed Document: [Prototype 01 PDD](../02-prototypes/prototype-01/pdd.md)

### Specification impact

- 新たなゲーム仕様変更：なし
- PDD仕様本文変更：なし
- Phase構造変更：なし
- Scope変更：なし
- GDD変更：なし
- Unity変更：なし


## 2026-07-26

### GDD — 領主権限・課題・布令への再構成

- GDDの間接統治方針を再構成した。
- 政治・政策を、上位領主からの課題、領主権限、布令・施策へ変更した。
- 町づくり、交易・備蓄、領主施策の問題解決上の優先順位を明確化した。
- 基本的な税率設定を、ゲーム開始時から持つ領主の基本権限として明確化した。
- 町を成立させる基本行動と、研究・発展による個別コンテンツ解放を分離した。
- 将来の委任・自動化をEarly Access対象外のBacklog候補として整理した。

### Traceability

- PR: [#6 — docs: rework lord authority system](https://github.com/undershot0704/Project-IYASAKA/pull/6)
- Merge Commit: [`1d73ec77d0d53a6027b44841dcd6a677883f4291`](https://github.com/undershot0704/Project-IYASAKA/commit/1d73ec77d0d53a6027b44841dcd6a677883f4291)
- Source Head: `8f93aade325b79b76b5b0ba8c01fee2b1156e1dd`
- Changed Specification: [Game Design Document](../01-gdd/gdd.md)

### Specification impact

- ゲーム仕様変更：あり
- GDDメタデータ変更：なし
- PDD影響：なし
- Unity変更：なし


### GDD v1.0 Active化

- GDD v1.0を正式承認した。
- Statusを`Active`とした。
- Versionを`1.0`とした。
- Approvedを`Approved`とした。
- Implementation UseをGDDに限り`Allowed`とした。
- 仕様本文は変更せず、メタデータのみ変更した。
- PDDはDraftのまま維持した。
- Unityファイルは変更していない。

### Traceability

- PR: [#8 — docs: activate GDD v1.0](https://github.com/undershot0704/Project-IYASAKA/pull/8)
- Merge Commit: [`2c7800123c809819865fecfcf01a08932f2d2484`](https://github.com/undershot0704/Project-IYASAKA/commit/2c7800123c809819865fecfcf01a08932f2d2484)
- Source Head: `dcb85dc1f2b001570e0c501828b8cc9b51fabb53`
- Changed Document: [Game Design Document](../01-gdd/gdd.md)

### Specification impact

- 新たなゲーム仕様変更：なし
- GDD本文変更：なし
- PDD変更：なし
- Unity変更：なし


## 2026-07-25

### Documentation foundation

- 正式仕様と文書履歴のSingle Source of TruthをGitHubへ変更した。
- Notionを補助用途へ変更した。
- PDDとSystem Specの責務を分離した。
- 旧統合PDDをArchiveとして保存し、実装要件として使用しない方針を定めた。
- Prototype 01 PDDをPhase 1〜7の構成で再構成した。
- Prototype 01 PDD v1.0-rc1をDraftとしてGitHubへ反映した。
- Prototype 01 PDDの `Implementation Use` を `Prohibited` とした。
- Active Phaseを明示する運用を定めた。
- System Specを対象Phaseの実装直前に作成する運用を定めた。

### Specification impact

- ゲーム仕様変更：なし
- Phase構成変更：なし
- Scope変更：なし
- Out of Scope変更：なし
