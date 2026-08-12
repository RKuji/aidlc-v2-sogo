# Build and Test Results — csv-import-display

実行日時: 2026-08-12。対象: `aidlc-v2-sogo-package`(main、コミット`4ea8c7a`、squash-merge後)。

## ビルド結果

| コマンド | 結果 |
|---|---|
| `npm install` | 成功 |
| `npx tsc --noEmit` | 成功(エラー0件) |
| `npx eslint .` | 成功(エラー0件) |
| `npm run build`(Next.js本番ビルド) | 成功。6ルート(`/`, `/_not-found`, `/api/[[...route]]`, `/import`, `/search`)を正常に生成 |

## テスト結果

| テスト種別 | コマンド | 結果 |
|---|---|---|
| ユニットテスト | `npx vitest run` | **43/43件パス**(9テストファイル) |
| カバレッジ | `npx vitest run --coverage` | **全体ライン カバレッジ84.98%**(NFR-1の80%以上を満たす) |
| 統合テスト(Testcontainers) | `npx vitest run --config vitest.integration.config.mts` | **環境要因により未実行(4件スキップ)** — 本サンドボックス環境ではDockerデーモンへの接続が権限エラー(`permission denied ... docker.sock`)で拒否されるため。テストコード自体は妥当であり(csv-import-display/code-generationのレビューで確認済み)、Docker daemonにアクセス可能なCI/開発環境での実行が必要 |
| Storybookビルド | `npx storybook build` | **失敗**(既知の課題、project.md DECIDED参照。本ステージのビルド検証対象外として明示的に除外) |

## カバレッジ詳細(主要コンポーネント)

- `server/services/csv-validation-service.ts`: 100%(21列検証・collect-all集約の中核)
- `server/services/csv-parser-service.ts`: 91.78%
- `server/schema/master-data-schema.ts`: 93.84%
- `server/routes/*`: 91.66%(平均)
- `server/repositories/master-data-repository.ts`: 10.81%(意図的 — upsert/クエリの主要検証はTestcontainers統合テスト側が担う設計、`integration-test-instructions.md`参照)

## 失敗の診断・修正

本ステージでの新規のビルド/テスト失敗は無し(code-generationステージのレビューで既にREADY判定を得た状態を再確認したもの)。統合テストはコードの欠陥ではなく環境制約(Docker daemonアクセス不可)により実行できなかった。

## Assumptions & Open Questions

- 統合テスト(Testcontainers)は本サンドボックス環境では実行不可。CI環境(Docker利用可能)での実行結果を、ci-pipelineステージまたはその後の運用フェーズで別途確認する必要がある [assumption]
