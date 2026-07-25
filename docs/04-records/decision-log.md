# Project IYASAKA — Decision Log

Status: Draft  
Last Updated: 2026-07-25  
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
