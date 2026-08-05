# Initiative Brief — 単位数表マスタ(CSV)の取り込み・表示

## Intent & Problem Statement

介護予防・日常生活支援総合事業費の単位数表マスタを、CSVで正確に取り込み・検証・保存し、後続の請求・サービス計画業務等が正しい単位数データを参照できるようにする(`../intent-capture/intent-statement.md`)。

## Target Customer & Stakeholders

主要な利用者は介護サービス事業者・ケアマネジャー等(保険者事務担当者は直接利用者として想定しない)。CSVの取り込み操作もこのエンドユーザー自身が行う。決定者は発注者(業務担当部門)、影響者は開発チーム(`../intent-capture/stakeholder-map.md`)。

## Market Validation

このスコープ(`csv-master-import-display`)ではmarket-researchステージはSKIPされており、市場調査は対象外(内部業務システムのため)。

## Feasibility & Risk Highlights

feasibilityステージはSKIPされているが、scope-definitionで最大のリスク(20列のCSV検証ロジックの正確性)を識別し、リスク優先(risk-first)の実装順序で緩和する方針を確認済み(`../scope-definition/scope-document.md`)。

## Scope Boundary

- In: CSV取り込み・19列の検証・DB保存、保険者番号・サービス種類コードによる検索表示
- Out: UX改善(ページング等)、他システム連携、証記載保険者番号マスタの追加(将来候補)
(`../scope-definition/scope-document.md`, `../scope-definition/intent-backlog.md`)

## Concept Visuals

rough-mockupsステージはSKIPされており、コンセプトビジュアルは未作成。

## Team Plan

team-formationステージはSKIPされており、チーム編成の詳細artifactは無い。発注者(業務担当部門)がスコープ・優先度を決定する体制であることのみ確認済み(`../intent-capture/stakeholder-map.md`)。

## Go/No-Go Recommendation

**Go** — Inceptionフェーズ(practices-discovery等)へ進む。全ステークホルダーが意図・スコープに合意し、重大リスクは緩和方針とともに確認済み、予算・リソースの割り当てもある(`approval-handoff-questions.md` Q1-Q4)。

## Assumptions & Open Questions

None.
