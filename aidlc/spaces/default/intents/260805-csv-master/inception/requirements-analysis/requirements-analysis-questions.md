# Requirements Analysis — Questions

Prior stages already established: 19列のCSV検証(予備項目を除く)、collect-all方式のエラー収集、CSV取り込み+条件検索表示の最小スコープ、3層アーキテクチャ、全テストツールのCIブロッキング。以下は完全性分析(6次元)で残った具体的なギャップです。

## Q1. What is the expected CSV file character encoding?

- A. Shift-JIS(この文書系統の単位数表マスタCSVで一般的なエンコーディング)
- B. UTF-8
- C. Not yet defined(取り込み時に自動判定する)
- X. Other (please specify)

[Answer]:

## Q2. What happens when a CSV row duplicates an existing record (same 保険者番号+サービス種類コード+サービス項目コード+適用開始年月日)?

- A. 上書き(最新の取り込み内容で既存レコードを置き換える)
- B. バージョン管理(既存レコードを保持しつつ新しいレコードも保存し、履歴として残す)
- C. 拒否(重複を検出したらそのファイル全体の取り込みを拒否する)
- D. Not yet defined
- X. Other (please specify)

[Answer]:

## Q3. When a CSV is rejected due to validation errors, what should the user see?

- A. 全違反(collect-all方式で収集した全ての違反)を列挙したエラーレポートを画面に表示する
- B. エラーレポートを画面表示に加えて、ダウンロード可能な形式(CSV/テキスト)でも提供する
- C. Not yet defined
- X. Other (please specify)

[Answer]:

## Q4. What is the file upload mechanism and are there size/volume limits?

- A. 1回につき1ファイルのみアップロード可能。ファイルサイズ上限は特に設けない
- B. 複数ファイルの一括アップロードに対応する
- C. Not yet defined
- X. Other (please specify)

[Answer]:

## Q5. Which columns should the search/display table show?

- A. 19列すべて(予備項目を除く)を表示する
- B. 主要な列(保険者番号、サービス種類コード、サービス項目コード、サービス名称、単位数、適用期間)のみを表示し、詳細は行クリックで展開する
- C. Not yet defined
- X. Other (please specify)

[Answer]:

## Q6. What are the expected data volume and search response time targets?

- A. 特定の性能目標は設けない(内部業務システムとして妥当な応答性能であれば良い)
- B. 具体的な目標がある(例:検索結果は3秒以内に表示、1回の取り込みは最大10,000行想定)
- C. Not yet defined
- X. Other (please specify)

[Answer]:

## Q7. Are there specific accessibility requirements beyond the default component library behavior?

- A. 特別な要件はない。Radix UI(アクセシビリティ対応済みのheadless UI)のデフォルト挙動に従う
- B. 追加のアクセシビリティ要件がある(例:WCAG準拠レベルの指定)
- C. Not yet defined
- X. Other (please specify)

[Answer]:

## Assumptions & Open Questions

None.
