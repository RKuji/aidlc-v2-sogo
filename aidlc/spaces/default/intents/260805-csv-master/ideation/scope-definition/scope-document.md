# Scope Document — 単位数表マスタ(CSV)の取り込み・表示

This scope builds on the intent captured in `../intent-capture/intent-statement.md`: importing and validating the CSV-distributed 単位数表マスタ so that downstream billing/care-planning reference correct data, for service providers/care managers as the confirmed end users.

## In Scope

- ローカルCSVファイルの取り込み(証記載保険者番号、ｻｰﾋﾞｽ種類ｺｰﾄﾞ、ｻｰﾋﾞｽ項目ｺｰﾄﾞ、適用開始/終了年月日、ｻｰﾋﾞｽ名称、単位数、算定単位、制限日数・回数、算定回数制限期間、支給限度額対象区分、給付率、利用者負担額、事業対象者実施区分、要支援1~2受給者実施区分、要介護1~5受給者実施区分の19列すべての検証を含む) [Q2/Q6]
- 定義されたCSV検証ルールに反する取り込みの拒否(予備項目は定義が示されていないため検証対象外) [Q6]
- DBへの取り込み済みデータの保存
- 取り込み済みの保険者番号・サービス種類コードをユーザーに選択させる検索UI
- 選択条件に一致する登録済みデータの表形式表示

## Out of Scope

- ページング・並び替え等の付加的なUX改善(nice-to-have、初期スコープ外) [Q2]
- 既存の保険者マスタ等、他システムとの連携(依存関係は無いことを確認済み) [Q3]
- 定期報告・運用/デプロイ関連の作業(運用/デプロイ関連ステージはワークフロースコープ`csv-master-import-display`から除外済み) — intent-capture stage参照(`../intent-capture/intent-statement.md`)
- **証記載保険者番号のマスタテーブル追加**: 今回のスコープでは対象外。将来的な拡張として想定する(承認ゲートでのユーザーフィードバック、2026-08-05)

## Minimum Viable Scope

まずCSV取り込み・検証・保存を実装し、その後に検索表示機能を実装する(取り込み優先) [Q1]。検索表示機能は取り込み機能に依存するため、この順序が唯一実行可能な順序でもある [Q3]。

## Sequencing

リスク優先(risk-first): 最も複雑な20列の検証ロジックを最初に実装し、リスクを早期に潰す [Q4]。

## Dependencies

検索表示機能は取り込み機能(取り込み済みデータの存在)に依存する。それ以外の外部システム依存は無い [Q3]。

## Timeline

特定の期限は無い [Q5]。

## Assumptions & Open Questions

None.
