# Project IYASAKA — Prototype 01 Roadmap

Status: Draft  
Prototype: Prototype 01  
Approved: Pending  
Implementation Use: Prohibited  
Owner: Project IYASAKA  

## Purpose

Prototype 01のPhase進行状況と、次Phaseへの移行条件を管理する。

Prototypeの目的、Scope、各Phaseの仕様および完了条件は[PDD](./pdd.md)を参照する。

## Current Status

| Item | Current value |
|---|---|
| Prototype | Prototype 01 |
| Current Phase | Phase 1 |
| Phase Status | Awaiting PDD Activation |
| PDD Status | Draft |
| PDD Version | 1.0-rc1 |
| Implementation Use | Prohibited |

Current Phaseは現在の計画上の対象を示す。PDDの最終レビューは完了しており、Active化待ちである。PDDがActive化されるまで実装開始を許可しない。

## Phase Overview

| Phase | Name | Status |
|---|---|---|
| Phase 1 | 基盤構築 | Awaiting PDD Activation |
| Phase 2 | 住民移動 | Planned |
| Phase 3 | 仕事システム | Planned |
| Phase 4 | 生活ループ | Planned |
| Phase 5 | 物流 | Planned |
| Phase 6 | 建築 | Planned |
| Phase 7 | ゲームループ完成 | Planned |

詳細スケジュールは本書で定義しない。

## Phase Transition Conditions

次Phaseへ移行するには、次を満たす必要がある。

- PDDで定める現在Phaseの完了条件を満たしている
- Unity上で人間による動作確認を完了している
- 正常動作、定義された主要な失敗、次Phaseに必要な出力を確認している
- 次Phaseの検証を妨げる既知の問題が残っていない

次PhaseでSystem Specが必要な場合は、実装開始前に必要な範囲だけ作成し、承認する。未承認または後続Phaseの実装を先行しない。
