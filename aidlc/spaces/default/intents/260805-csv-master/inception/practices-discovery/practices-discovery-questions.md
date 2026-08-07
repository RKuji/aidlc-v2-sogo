# Practices Discovery — Questions

Greenfield project. `org.md`'s five matching sections are shown as suggested defaults, not established team facts. Support agent reviews (`contributions/`) raised additional stack-specific gaps, folded into the relevant question below.

## Q1. Way of Working — confirm trunk-based development?

- A. org.mdのデフォルトを採用: トランクベース開発。短命なfeatureブランチでmainへ、Boltブランチはsquash-mergeで1コミットに統合
- B. 別の方式を採用する
- C. Not yet defined
- X. Other (please specify)

[Answer]: A. org.mdのデフォルトを採用: トランクベース開発。短命なfeatureブランチでmainへ、Boltブランチはsquash-mergeで1コミットに統合

## Q2. Walking Skeleton — confirm the stance for this scope?

- A. スコープファイル(`csv-master-import-display`)の`skeleton: on`を採用: Bolt 1はウォーキングスケルトンとして単独実行・ゲート、承認後にラダープロンプトで残りのBoltの進め方を決める
- B. スケルトンセレモニーは不要(スコープ上書き)
- C. Not yet defined
- X. Other (please specify)

[Answer]: A. スコープファイル(`csv-master-import-display`)の`skeleton: on`を採用: Bolt 1はウォーキングスケルトンとして単独実行・ゲート、承認後にラダープロンプトで残りのBoltの進め方を決める

## Q3. Testing Posture — coverage floor & which tools gate CI?

- A. org.mdのデフォルトを採用: コード横並びでテストを書く、最低80%行カバレッジ、CIでテスト実行してマージ前にブロック。Vitest+Testcontainersを結合テストとしてCIでブロッキング実行し、Playwright/MagicPodのE2Eは別カデンス(マージブロックはしない)で実行する
- B. 全テストツール(Vitest, Testcontainers, Storybook, MSW, Playwright, MagicPod)をCIでブロッキング実行する
- C. Not yet defined
- X. Other (please specify)

[Answer]: B. 全テストツール(Vitest, Testcontainers, Storybook, MSW, Playwright, MagicPod)をCIでブロッキング実行する

## Q4. Testing Posture — CSV 19-column validation error-handling & fixtures?

- A. Fail-fast: 検証ルール違反を1件検出した時点で取り込み全体を拒否する。テストフィクスチャはCSV定義表の19列それぞれについて正常値・境界値・異常値のケースを用意する
- B. Collect-all: すべての違反を収集してエラーリストとして返す
- C. Not yet defined
- X. Other (please specify)

[Answer]: B. Collect-all: すべての違反を収集してエラーリストとして返す

## Q5. Deployment — confirm deploy-on-merge to staging?

- A. org.mdのデフォルトを採用: マージ時にstaging環境へ自動デプロイ、production環境へは別途の手動承認(テックリード+プロダクトオーナー承認)を経てデプロイ
- B. 別の方式を採用する
- C. Not yet defined
- X. Other (please specify)

[Answer]: A. org.mdのデフォルトを採用: マージ時にstaging環境へ自動デプロイ、production環境へは別途の手動承認(テックリード+プロダクトオーナー承認)を経てデプロイ

## Q6. Code Style & layering — confirm conventions and layer boundaries?

- A. org.mdのデフォルト(Prettier/ESLint、リンター設定優先)を採用。加えて、Hono(API層)→サービス層→Prisma(データ層)の3層構造を守り、CSV列名(証記載保険者番号等の日本語・カタカナ表記)はTypeScript/Zod/Prismaスキーマ上では英語のcamelCase識別子にマッピングし、対応表をコード内コメントまたはドキュメントに明記する
- B. 別の命名・層構造の方針を採用する
- C. Not yet defined
- X. Other (please specify)

[Answer]: A. org.mdのデフォルト(Prettier/ESLint、リンター設定優先)を採用。加えて、Hono(API層)→サービス層→Prisma(データ層)の3層構造を守り、CSV列名(証記載保険者番号等の日本語・カタカナ表記)はTypeScript/Zod/Prismaスキーマ上では英語のcamelCase識別子にマッピングし、対応表をコード内コメントまたはドキュメントに明記する

## Q7. Security & Supply-Chain — SAST/secret/dependency scanning expectations?

devsecopsレビューにより、org.mdの5セクションにはSAST/DAST/シークレットスキャン/依存関係スキャンの言及が無いことが指摘されました。このプロジェクト(CSVアップロード、Hono、Prisma、Aurora)での期待水準を確認させてください。

- A. CIにdependency scanning(例:npm audit/Dependabot相当)とsecret scanningを組み込む。SAST/DASTは今回のスコープでは対象外とする(内部業務システムのため)
- B. SAST/DASTも含めて本格的なセキュリティパイプラインを今回のスコープで構築する
- C. Not yet defined
- X. Other (please specify)

[Answer]: A. CIにdependency scanning(例:npm audit/Dependabot相当)とsecret scanningを組み込む。SAST/DASTは今回のスコープでは対象外とする(内部業務システムのため)

## Assumptions & Open Questions

None.
