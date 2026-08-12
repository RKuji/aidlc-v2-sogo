# Security Test Instructions — csv-import-display

Source: `../csv-import-display/code-generation/code-summary.md`、`../../inception/practices-discovery/team-practices.md`(Code Style: SAST/DASTは対象外、dependency/secretスキャンはCIレベル)。

本ステージ(build-and-test)のテスト戦略はStandardであり、正式なNFRセキュリティ要件はrequirements.mdに定義されていない。しかし、CSVファイルアップロードという入力境界の性質上、devsecops-agentの観点から以下の軽量なセキュリティ関連テストをunit-test-instructions.mdの対象に追加することを推奨する(既存のcsv-parser-service.test.ts/csv-import-route.test.tsで一部カバー済み)。

## 対象と根拠

- **入力境界のサニタイズ検証**(construction phase guardrail: 「Validate and sanitize all inputs at system boundaries」): `CsvParserService`が構造検証(列数不一致・空ファイル・Shift-JISデコード失敗、FR-A5)で不正な入力を確実に拒否することは、既存のユニットテスト(`csv-parser-service.test.ts`)で検証済み。
- **認証・認可のバイパスなし**: 本スコープでは認証・認可要件自体が定義されていない(requirements.mdに記載なし)。既存コードもこれをバイパスする実装を含まない(該当する検証対象がない)。
- **SAST/DAST**: project.md Forbiddenにより本プロジェクトのスコープでは明示的に対象外。本ステージでは実施しない。
- **依存関係・シークレットスキャン**: CIレベルの関心事(ci-pipelineステージの責務)であり、本ステージ(ユニット/統合テスト)の対象外。

## 実行方法

追加のテストランナー設定は不要。既存の`npx vitest run`実行内で、`csv-parser-service.test.ts`の構造検証ケース(列数不一致・空ファイル・デコード失敗)がこの観点をカバーしている。

## Assumptions & Open Questions

None.
