# CI Pipeline — Questions

Source: `../build-and-test/{build-and-test-summary,build-test-results}.md`、`../csv-import-display/code-generation/code-summary.md`、`team-practices.md`(Testing Posture: 6ツール全ブロッキング)。

## Q1. どのCIツールを使用しますか?

- A. GitHub Actions
- B. AWS CodePipeline/CodeBuild
- C. Not yet defined
- X. Other (please specify)

[Answer]: A. GitHub Actions

## Q2. ブランチ戦略は?

- A. トランクベース開発(org.md/team.mdで既に確認済み): `main`への短命フィーチャーブランチ経由のマージ、Boltブランチはsquash-merge
- B. 別の戦略
- X. Other (please specify)

[Answer]: A. トランクベース開発(org.md/team.mdで既に確認済み): `main`への短命フィーチャーブランチ経由のマージ、Boltブランチはsquash-merge

## Q3. マージ前に必須の品質ゲートは?

- A. team-practices.mdで確認済みの6ツール(Vitest、Testcontainers、Storybook、MSW、Playwright、MagicPod)すべてをブロッキング。ただしStorybookは既知の互換性問題(project.md DECIDED)により、今回のCI設定では一時的に非ブロッキングとし、別途修正する
- B. 6ツールすべて厳密にブロッキング(Storybookの問題を今すぐ解決する)
- X. Other (please specify)

[Answer]: A. team-practices.mdで確認済みの6ツール(Vitest、Testcontainers、Storybook、MSW、Playwright、MagicPod)すべてをブロッキング。ただしStorybookは既知の互換性問題(project.md DECIDED)により、今回のCI設定では一時的に非ブロッキングとし、別途修正する

## Q4. アーティファクトリポジトリは使用しますか?(ECR、CodeArtifact、S3等)

- A. 使用しない(内部業務システムで、コンテナイメージ等の配布は今回のスコープでは不要)
- B. 使用する
- X. Other (please specify)

[Answer]: A. 使用しない(内部業務システムで、コンテナイメージ等の配布は今回のスコープでは不要)

## Assumptions & Open Questions

None.
