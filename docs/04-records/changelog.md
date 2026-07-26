# Project IYASAKA — Changelog

Status: Draft  
Last Updated: 2026-07-25  
Owner: Project IYASAKA  
Implementation Use: Prohibited  

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
