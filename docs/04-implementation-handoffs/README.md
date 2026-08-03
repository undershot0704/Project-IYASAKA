# Implementation Handoffs

Status: Draft  
Owner: Project IYASAKA  
Implementation Use: Prohibited

Implementation Handoffは、承認済みSystem Specを、実装担当へ渡す作業範囲・検証方法・停止条件へ変換する文書である。

- 承認前のHandoffは実装判断に使用しない。
- Handoffのmainへの保存だけではUnity実装を開始しない。
- Unity実装には、承認済みSystem Spec、承認済みImplementation Handoff、ユーザーによる明示的な実装許可が必要である。

## Prototype 01

| Phase | Document | Status | Version | Approved | Implementation Use | Unity Implementation |
|---|---|---|---|---|---|---|
| Phase 1 — Foundation | [Implementation Handoff](prototype-01/phase-01-foundation.md) | Approved | 1.5 | 2026-08-02 | Permitted | Completed |

Phase 1 Implementation Handoff v1.5に基づくUnity実装は完了している。Phase 2 Implementation Handoffは未作成であり、承認済みのPhase 2 System Specを入力として新規作成する。Phase 2のHandoff承認とユーザーによる明示的な実装開始許可が揃うまで、Phase 2のUnity実装は開始しない。
