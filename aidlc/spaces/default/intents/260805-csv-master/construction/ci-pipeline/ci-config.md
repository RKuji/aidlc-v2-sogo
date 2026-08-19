# CI Pipeline Configuration — csv-import-display

Source: `../build-and-test/{build-and-test-summary,build-test-results}.md`、`../csv-import-display/code-generation/code-summary.md`、`ci-pipeline-questions.md`(Q1〜Q4)。

## CIツール

GitHub Actions(Q1)。設定ファイル: `aidlc-v2-sogo-package/.github/workflows/ci.yml`。

## ブランチ戦略

トランクベース開発(Q2、org.md/team.md確認済み)。`main`への`pull_request`と`push`でワークフローを起動する。

## ジョブ構成

| ジョブ | 内容 | ブロッキング |
|---|---|---|
| type-check | `npx tsc --noEmit` | ✅ |
| lint | `npx eslint .` | ✅ |
| unit-tests | `npx vitest run --coverage`、カバレッジ80%以上を機械的に検証(NFR-1) | ✅ |
| integration-tests | `npx vitest run --config vitest.integration.config.mts`(Testcontainers、GitHub Actions runnerはDocker利用可能) | ✅ |
| e2e-tests | `npx playwright test`(ビルド後) | ✅ |
| storybook-build | `npx storybook build` | ⚠️ **非ブロッキング**(`continue-on-error: true`) — 既知の互換性問題(project.md DECIDED参照)。解決後にブロッキングへ戻す |
| magicpod-acceptance-tests | MagicPodテストプラン実行 | **無効化中**(`if: false`)。実際のMagicPod組織/プロジェクト/APIトークンが未設定のため。設定完了後に有効化すること |
| dependency-scan | `npm audit --audit-level=high` | ✅ |
| secret-scan | gitleaks | ✅ |
| all-required-checks | 上記ブロッキングジョブすべての成功を集約(ブランチ保護の必須ステータスチェックとして設定する) | ✅(集約) |

## アーティファクトリポジトリ

使用しない(Q4)。内部業務システムであり、コンテナイメージ等の配布は本スコープでは不要。

## Storybookの一時的な非ブロッキング化についての明示的な例外

team-practices.mdのTesting Postureは「確認済みの6つのテストツールすべてをブロッキングなCIチェックとして実行する」と定めているが、Storybookは`@storybook/nextjs`(8.6.18)とNext.js(14.2.35)の既知の互換性問題(`SB_BUILDER-WEBPACK5_0002`)によりビルド自体が失敗する状態にある(project.md DECIDED参照)。ユーザーの明示的な承認により、本CI設定ではStorybookジョブのみ`continue-on-error: true`として一時的に非ブロッキングとし、問題解決後にブロッキングへ戻すこととする(Q3)。

## デプロイについて

deployment-pipelineステージは本ワークフロースコープではSKIPされているため、本CI設定はビルド・テスト・品質ゲートの検証のみを対象とし、実際のデプロイステップは含まない。

## Assumptions & Open Questions

None.
