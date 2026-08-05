# Intent Statement — 単位数表マスタ(CSV)の取り込み・表示

## Problem Statement

介護予防・日常生活支援総合事業費の単位数表マスタ(サービス種類・項目ごとの単位数、算定単位、制限回数、給付率等を定める規制対象マスタデータ)を、配布されるCSVファイルから正確に取り込む必要がある [desc]。現在は既存実装が無く、CSVは定義された検証ルールに反する場合は取り込みを行わないことが求められている [desc]。目的は、CSVで配布される単位数表マスタを定義された検証ルールに従って正確に取り込み・保存し、後続の請求・サービス計画業務等が正しい単位数データを参照できるようにすることである [Q1]。成功の定義は、定義に反するCSVが確実に取り込み拒否されること(バリデーション遵守率100%)、および保険者番号・サービス種類コードでの検索結果が常に正確であることである [Q3]。

## Target Customer

主要な利用者は介護サービス事業者・ケアマネジャー等、単位数を参照して算定を行う利用者である [Q2]。ステークホルダーはサービス事業者のみを想定し、保険者事務担当者は直接の利用者として想定しない [Q5]。CSVファイルをDBへ取り込む操作も、このエンドユーザー(サービス事業者・ケアマネジャー等)自身が行う [Q9]。

## Success Metrics

- 定義に反するCSVが確実に取り込み拒否されること(バリデーション遵守率100%) [Q3]
- 保険者番号・サービス種類コードでの検索結果が常に正確であること [Q3]

## Initiative Trigger

既存実装が無いため、これは新規業務システムの立ち上げの一部である [Q4]。

## Initial Scope Signal

- **Workflow-selected scope**: `csv-master-import-display`(12ステージ構成のカスタムスコープ) [scope]。
- **User-confirmed product boundary**: このスコープ(CSV取り込み・検証・保存・条件検索表示のみ、運用/デプロイ関連ステージは対象外)は意図した範囲と一致することが確認された [Q8]。

## Assumptions & Open Questions

None.

## Review

**Verdict: READY**

**Reviewer:** aidlc-product-lead-agent
**Date:** 2026-08-05T06:18:17Z
**Iteration:** 2

### Findings

- Both iteration-1 Critical findings are resolved: the fabricated Success Metrics exclusion bullet and the unsupported "pain" sentence in Target Customer are gone; remaining Success Metrics/Target Customer sentences trace cleanly to Q2/Q3.
- The iteration-1 Major stakeholder gap (who performs the CSV import) is resolved via Q9, whose confirmed answer ("サービス事業者・ケアマネジャー等...自身がCSVを取り込む") literally supports the `[Q9]`-tagged sentences in both intent-statement.md (Target Customer) and stakeholder-map.md (Interest row). No unselected Q9 option was smuggled in as fact.
- Q9 is coherent with Q2/Q5: the same end-user role is consistently identified as both the reference-user and the importer; no contradiction introduced.
- `Assumptions & Open Questions` correctly shows "None." in both artifacts now that the prior assumption was converted to Q9 and answered — consistent with the `Assumption Confirmation: B` choice in the questions file.
- Minor, non-blocking: stakeholder-map's 発注者 row adds "範囲の統制" (scope control) alongside the `[Q6]`-sourced "決定する" — a mild paraphrase beyond Q6's literal wording, not a fabrication. Does not affect readiness.
