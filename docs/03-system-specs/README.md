# Project IYASAKA — System Specs

Status: Draft  
Last Updated: 2026-07-27  
Owner: Project IYASAKA  
Implementation Use: Prohibited  

## Role

System Specは、承認済みPDDで定義された対象について、実装に必要な内部挙動、状態遷移、データ、アルゴリズム、例外処理などを管理する。

System Specは新しいゲーム仕様を決定する文書ではない。

## Responsibility boundary with PDD

- PDD：「何を作るか」「何を検証するか」「どこまでを対象とするか」を管理する。
- System Spec：PDDで承認された対象が「どのように動くか」を管理する。

PDDのScope、Out of Scope、Phase構成、完了条件をSystem Spec側で変更しない。変更が必要な場合は仕様監査へ戻す。

## Creation timing

System Specは対象Phaseの実装直前に、実装と検証に必要な範囲だけ作成する。

未着手Phaseの詳細を先行して確定せず、完成版向けの汎用設計を追加しない。

## Draft operation

- 新規System SpecはDraftとして作成する。
- Draftはレビューと承認が完了するまで実装判断に使用しない。
- Draftには `Implementation Use: Prohibited` を明記する。
- 承認前にApprovedまたは `Implementation Use: Permitted` へ変更しない。
- `Status: Approved`かつ`Implementation Use: Permitted`の文書のみをImplementation Handoff作成の正式な入力として使用する。
- System Specが承認済みであっても、承認済みImplementation Handoffがない状態ではUnity実装を開始しない。

## Prototype 01

| Document | Status | Version | Approved | Implementation Use | Unity Implementation |
|---|---|---|---|---|---|
| [Phase 1 Foundation System Spec](./prototype-01/phase-01-foundation.md) | Approved | 1.0 | 2026-07-27 | Permitted | Prohibited |

Phase 1 Foundation System Specは承認済みであり、Phase 1 Implementation Handoff作成の正式な入力として使用できる。Phase 1 Implementation Handoffが承認されるまでUnity実装を開始しない。
