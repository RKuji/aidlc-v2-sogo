# Scope Definition & Prioritization — Questions

## Q1. What is the minimum viable scope that delivers value?

- A. CSV取り込み(検証・保存)と、保険者番号・サービス種類コードによる検索表示の両方が最小スコープ。取り込みだけ、または表示だけでは単独で価値を発揮しない
- B. まずCSV取り込み・検証・保存のみを実装し、表示は後続フェーズに回す
- C. Not yet defined
- X. Other (please specify)

[Answer]:

## Q2. What capabilities are must-have vs. nice-to-have?

- A. must-have: 20列すべてのCSV検証ルール(証記載保険者番号、ｻｰﾋﾞｽ種類ｺｰﾄﾞ、ｻｰﾋﾞｽ項目ｺｰﾄﾞ、適用開始/終了年月日、単位数、算定単位、制限日数・回数、算定回数制限期間、支給限度額対象区分、給付率、利用者負担額、各受給者実施区分等)の完全実装。nice-to-have: ページング・並び替え等の細かいUX
- B. 一部の列(例:予備項目)は検証を必須としない
- C. Not yet defined
- X. Other (please specify)

[Answer]:

## Q3. What are the dependencies between capabilities?

- A. 検索表示機能は取り込み機能に依存する(取り込まれたデータが無ければ表示できない)。それ以外の外部システム依存は無い
- B. 既存の保険者マスタ等、他システムとの連携に依存する部分がある
- C. Not yet defined
- X. Other (please specify)

[Answer]:

## Q4. What is the sequencing preference (risk-first, value-first, dependency-first)?

- A. 依存関係優先(dependency-first): 取り込み機能を先に実装し、データが存在する状態を作ってから表示機能を実装する
- B. リスク優先(risk-first): 最も複雑な20列の検証ロジックを最初に実装し、リスクを早期に潰す
- C. Not yet defined
- X. Other (please specify)

[Answer]:

## Q5. Are there hard deadlines tied to specific capabilities?

- A. 特定の期限は無い
- B. 制度改定・単位数表の更新サイクルに合わせた期限がある
- C. Not yet defined
- X. Other (please specify)

[Answer]:

## Assumptions & Open Questions

None.
