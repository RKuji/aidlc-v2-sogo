# Intent Capture & Framing — Questions

## Sources

- [desc] Initial description: "# 機能\n介護予防・日常生活支援総合事業費の単位数表マスタ（CSV）の取り込み・表示\n\n# 既存実装\n無し\n\n# 詳細\n## 取り込み\nローカルにあるCSVファイルを取り込み、DBに保存する。\nValidation は、次の項で記載するCSV定義に従い、それに反する場合は取り込みを行わない。\n## 表示\n取り込み済みの保険者番号と、ｻｰﾋﾞｽ種類ｺｰﾄﾞをユーザーに選択させ、その情報に一致する保存済みデータを表形式で表示する。\n\n# CSV定義\n証記載保険者番号(6桁)、ｻｰﾋﾞｽ種類ｺｰﾄﾞ(2桁)、ｻｰﾋﾞｽ項目ｺｰﾄﾞ(4桁の指定ｺｰﾄﾞ)、適用開始年月日(6桁YYYMMDD)、適用終了年月日(6桁YYYMMDD)、ｻｰﾋﾞｽ名称(なんでも)、単位数(-9999～99999)、算定単位(01/02/03/05のコード)、制限日数・回数(0～99)、算定回数制限期間(01/08/16/00のコード)、支給限度額対象区分(3または0)、予備項目、給付率(1～100)、利用者負担額(5桁まで)、事業対象者実施区分(1or2)、要支援1～2受給者実施区分(1or2)、要介護1～5受給者実施区分(1or2)。\n\n# 技術スタック\nFrontend: Next.js/React/TypeScript, Radix UI, Tailwind CSS, CVA, clsx, tailwind-merge。State: SWR, Zustand, nuqs, React Hook Form, Zod。API: Hono, OpenAPI, openapi-fetch。Data: Aurora PostgreSQL, Prisma。Quality: Vitest, Testcontainers, Storybook, MSW, Playwright, MagicPod。"
- [scope] Workflow-selected scope: `csv-master-import-display`.

> **Note (deviation, logged in `memory.md`):** This intent was born through the compose flow (`intent-birth --scope csv-master-import-display --label "csv master"`) without passing `--arguments`, so `aidlc-state.md`'s `Project` field still holds the birth-time placeholder `[Project description]` rather than this text. The `[desc]` value above is the actual initial description carried in the conductor's conversation context (the authoritative source per this stage's Step 2: "Read user's project description from $ARGUMENTS or the audit shard"). The `claim-sources` sensor (advisory-only) is expected to flag one finding — `[desc] does not exactly match Project in aidlc-state.md` — as a result; this is a known birth-flow gap, not a content defect.

## Q1. What business problem are we solving?

介護予防・日常生活支援総合事業費の単位数表(サービス種類・項目ごとの単位数・算定ルール等を定めたマスタ)を、CSVで正しく取り込み、検索・参照できるようにする必要があります。この取り組みの本質的な目的について確認させてください。

- A. CSVで配布される単位数表マスタを、定義された検証ルールに従って正確に取り込み・保存し、後続の請求・サービス計画業務等が正しい単位数データを参照できるようにする [desc]
- B. 既存の手作業・Excel等での単位数表管理を、システム化された取り込み・参照プロセスに置き換える
- C. 複数保険者・複数サービス種類にわたる単位数表を一元管理し、監査や履行確認をしやすくする
- D. Not yet defined
- X. Other (please specify)

[Answer]: A. CSVで配布される単位数表マスタを、定義された検証ルールに従って正確に取り込み・保存し、後続の請求・サービス計画業務等が正しい単位数データを参照できるようにする

## Q2. Who is the customer (internal/external)? What pain are they experiencing?

- A. 保険者(市区町村等)の内部事務担当者。手作業でのマスタ確認・入力ミスや、CSV配布ごとの反映作業負荷が課題 [desc]
- B. システムを運用するベンダー/情報システム部門。取り込み時のデータ不整合や検証漏れが課題
- C. 介護サービス事業者・ケアマネジャー等、単位数を参照して算定を行う利用者
- D. Not yet defined
- X. Other (please specify)

[Answer]: C. 介護サービス事業者・ケアマネジャー等、単位数を参照して算定を行う利用者

## Q3. What does success look like? What metrics matter?

- A. 定義に反するCSVは確実に取り込みが拒否される(バリデーション遵守率100%)こと、および保険者番号・サービス種類コードでの検索結果が常に正確であること [desc]
- B. 取り込み処理の速度・大量データ(全国規模)の処理可否
- C. 特定の定量指標は定めない。まずは正しく取り込み・表示できることが成功の定義
- D. Not yet defined
- X. Other (please specify)

[Answer]: A. 定義に反するCSVは確実に取り込みが拒否される(バリデーション遵守率100%)こと、および保険者番号・サービス種類コードでの検索結果が常に正確であること

## Q4. What is the trigger for this initiative (market pressure, tech debt, regulation, opportunity)?

- A. 介護保険制度・総合事業費の単位数表が定期的に改定され、その都度CSVマスタを最新化・反映する必要がある(制度要因) [desc]
- B. 既存実装が無いため、これは新規業務システムの立ち上げの一部である
- C. 特定の期限・改定サイクルに対応するための緊急対応
- D. Not yet defined
- X. Other (please specify)

[Answer]: B. 既存実装が無いため、これは新規業務システムの立ち上げの一部である

## Q5. Who are the key stakeholders and what does each care about?

- A. 保険者事務担当者(正確な取り込みとミス防止)、システム管理者/情報システム部門(運用・保守性)、サービス事業者(正しい単位数の参照)
- B. 保険者事務担当者のみが直接の利用者であり、他の関係者は現時点では想定しない
- C. Not identified
- D. Not yet defined
- X. Other (please specify)

[Answer]: X. Other — サービス事業者のみが使用する(介護サービス事業者・ケアマネジャー等が主要な利用者であり、保険者事務担当者は直接の利用者として想定しない)

## Q6. Who decides scope or priority, and who influences those decisions?

- A. 発注者(業務担当部門)がスコープ・優先度を決定し、開発チームが技術的な実現可能性で影響を与える
- B. Not yet defined
- C. Not identified
- D. Not applicable
- X. Other (please specify)

[Answer]: A. 発注者(業務担当部門)がスコープ・優先度を決定し、開発チームが技術的な実現可能性で影響を与える

## Q7. Are there communication requirements or a reporting cadence?

- A. 各ステージの承認ゲートでの確認のみで十分(定期報告は不要)
- B. 定期的な進捗報告(例:週次)が必要
- C. Not yet defined
- D. Not applicable
- X. Other (please specify)

[Answer]: A. 各ステージの承認ゲートでの確認のみで十分(定期報告は不要)

## Q8. The workflow was started with the scope in `csv-master-import-display` (a custom 12-stage plan: intent-capture, scope-definition, approval-handoff, practices-discovery, requirements-analysis, application-design, code-generation, build-and-test, ci-pipeline, plus initialization). Does that scope match the user's intended product boundary?

- A. Confirm — this scope (CSV取り込み・検証・保存・条件検索表示のみ、運用/デプロイ関連ステージは対象外)は意図した範囲と一致する [scope]
- B. Broaden — 追加のステージ(例:market-research, feasibility, infrastructure-design等)を含めたい
- C. Narrow — さらに範囲を絞りたい
- D. Not yet defined
- X. Other (please specify)

[Answer]: A. Confirm — this scope(CSV取り込み・検証・保存・条件検索表示のみ、運用/デプロイ関連ステージは対象外)は意図した範囲と一致する

## Assumptions & Open Questions

None.

## Assumption Confirmation

A. Accept assumptions
B. Convert to follow-up questions

[Answer]: B. Convert to follow-up questions

## Q9. CSVファイルをDBへ取り込む操作(ローカルCSVの投入)は、実際に誰が行いますか?

- A. サービス事業者・ケアマネジャー等(Q5で確認したエンドユーザー)自身がCSVを取り込む
- B. システム管理者・運用担当者など、エンドユーザーとは別の役割が取り込みを行う
- C. Not yet defined
- D. Not applicable
- X. Other (please specify)

[Answer]: A. サービス事業者・ケアマネジャー等(Q5で確認したエンドユーザー)自身がCSVを取り込む
