# Project IYASAKA — Changelog

Status: Draft  
Last Updated: 2026-07-27  
Owner: Project IYASAKA  
Implementation Use: Prohibited  

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
