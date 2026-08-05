# Intent Backlog — 単位数表マスタ(CSV)の取り込み・表示

Backlog derived from `../intent-capture/intent-statement.md` (target customer, success metrics) and this stage's scope decisions (`scope-document.md`).

## Proto-Units (MoSCoW)

| # | Proto-Unit | MoSCoW | Rationale | Source |
|---|---|---|---|---|
| 1 | CSV検証ルールエンジン(19列の形式・値域チェック) | Must | 最もリスクが高く、成功指標(バリデーション遵守率100%)の中核 [Q4][Q6] | Q4, Q6 |
| 2 | CSV取り込み・DB保存パイプライン | Must | MVPの前提。表示機能はこれに依存する [Q1][Q3] | Q1, Q3 |
| 3 | 保険者番号・サービス種類コードによる検索表示UI | Must | intent-statementで確認された成功指標(検索結果の正確性)の中核 | intent-statement.md |
| 4 | 予備項目の検証除外ハンドリング | Must | 定義欄が空の列を誤って拒否しないための明示的な例外処理 [Q6] | Q6 |
| 5 | ページング・並び替え等のUX改善 | Won't (this scope) | nice-to-haveとして初期スコープ外 [Q2] | Q2 |
| 6 | 他システム(既存保険者マスタ等)との連携 | Won't (this scope) | 依存関係なしと確認済み [Q3] | Q3 |
| 7 | 証記載保険者番号マスタテーブルの追加 | Won't (this scope) — Future candidate | 今回のスコープでは対象外だが、将来的な拡張として想定する | 承認ゲートでのユーザーフィードバック、2026-08-05 |

## Sequencing

1. CSV検証ルールエンジン(#1) — リスク優先で最初に実装 [Q4]
2. CSV取り込み・DB保存パイプライン(#2、#4を含む)
3. 検索表示UI(#3)

## Assumptions & Open Questions

None.
