# Project-Level Rules

> Project-specific specialisation and corrections. Loaded after `org.md` and
> `team.md` as strict-additive guidance; contradictions with broader policy
> are rejected. Populated by practices-discovery and the self-learning loop.
>
> Use sparingly: most teams don't need a project layer. Reach for it
> only when this specific project needs stable, durable guidance beyond the
> team practice (for example, package-specific release checks or an additional
> regression suite for a legacy component).

## Way of Working

<!-- Project-specific specialisation. Example: -->
<!-- This monorepo requires package-scoped branch names and a package owner -->
<!-- review in addition to the team's normal merge policy. -->

## Walking Skeleton

<!-- Project-specific specialisation. Example: -->
<!-- The walking skeleton must exercise the legacy service adapter as well -->
<!-- as the new service boundary. -->

## Testing Posture

<!-- Project-specific specialisation. -->

- Testcontainersを用いる統合テストはDockerデーモンへの実行権限が無い環境(ローカル開発機やサンドボックス等)では実行できずスキップされうる。これはコードの欠陥ではなく環境制約である。そのためCI環境(Dockerアクセス可能)での実行結果を、Testcontainers統合テストに関する正式な検証結果として扱う (learned 2026-08-12, Stage build-and-test) (learned 2026-08-12) <!-- cid:build-and-test:c2 -->
## Deployment

<!-- Project-specific specialisation. -->

## Code Style

<!-- Project-specific specialisation. -->

## Tech Stack

<!-- Technology choices locked for this project. -->

## Decided

<!-- Decisions made in earlier stages that should not be re-asked. -->
<!-- Format: DECIDED: [decision] (Stage [slug], [date]) -->

- DECIDED: 単位数表マスタCSVの検証は全22列中21列(予備項目を除くすべての列)に適用する。予備項目(CSV定義表で定義欄が空の列)のみ検証対象外とする — 定義が無い列には検証すべき形式的ルールが存在しないため (Stage scope-definition, 2026-08-05; 列数を19→21に訂正 2026-08-07) (learned 2026-08-05) <!-- cid:scope-definition:c1 -->
- DECIDED: Storybookのビルド/dev(build-storybook, storybook dev)は現在、@storybook/nextjs(8.6.18)とNext.js(14.2.35)の組み合わせで既知のアップストリーム互換性問題(SB_BUILDER-WEBPACK5_0002、webpackインスタンス不一致)により失敗する。作成済みの.stories.tsxファイル自体は正しいコードであり欠陥ではない。team-practices.mdのTesting Posture(6ツール全てCIブロッキング必須)を満たすには、ci-pipelineステージ着手前にNext.jsダウングレードまたはStorybookメジャーアップグレード(9.x/10.x)による解決が必要 (Stage code-generation, Bolt csv-import-display, 2026-08-12) (learned 2026-08-12) <!-- cid:code-generation:oq1 -->
- DECIDED: ci.ymlのmagicpod-acceptance-testsジョブは、実際のMagicPod組織/プロジェクト/APIトークン(MAGICPOD_API_TOKEN等のsecrets)が未設定のため`if: false`で無効化されている。運用フェーズでMagicPodアカウントを設定し、実際のテストプラン名を指定した上で有効化する必要がある (Stage ci-pipeline, 2026-08-12) (learned 2026-08-12) <!-- cid:ci-pipeline:oq-magicpod -->
## Scope Overrides

<!-- Custom scope rules for this project. -->

## Forbidden

<!-- Populated by practices-discovery affirmation gate. -->
<!-- Format: NEVER [behavior] (affirmed [date]) -->
<!-- Example: NEVER throw exceptions across service layer boundaries (affirmed 2026-05-17) -->

- NEVER CSV検証をフェイルファストとして扱わない(すなわち、最初の違反の後に行/ファイルの検証を中断しない) — 本プロジェクトのCSVインポートパイプラインでは全件収集(collect-all)が必須である (affirmed 2026-08-07, Q4)
- NEVER 現在のスコープにおいて、SASTまたはDASTツールを本プロジェクトのCI/CDパイプラインに含めない — この社内業務システムについては明示的に対象外と宣言されている (affirmed 2026-08-07, Q7)

## Mandated

<!-- Populated by practices-discovery affirmation gate. -->
<!-- Format: ALWAYS [behavior] (affirmed [date]) -->
<!-- Example: ALWAYS use Result<T,E> for fallible operations in service layer (affirmed 2026-05-17) -->

- ALWAYS 確認済みの6つのテストツール(Vitest、Testcontainers、Storybook、MSW、Playwright、MagicPod)すべてを、`main`へのマージ前にブロッキングなCIチェックとして実行する — 本プロジェクトにおいて、非ブロッキング/据え置きの実行サイクルで動作するテストツールは存在しない (affirmed 2026-08-07, Q3)
- ALWAYS CSVの1行における21列(予備項目を除く全列)すべての検証違反(および全行にわたる違反)を、最初に検出した違反で停止するのではなく、単一の違反リストへ収集する — インポートパイプラインはCSV検証においてフェイルファストであってはならない (affirmed 2026-08-07, Q4; 列数を19→21に訂正 2026-08-07)
- ALWAYS 日本語/半角カタカナのCSV列名を、TypeScript/Zod/Prismaのコード内で英語のcamelCase識別子にマッピングし、3層すべて(API、サービス、データ)が参照する単一の文書化されたマッピング表(ソースの列名 → camelCase識別子)を裏付けとする (affirmed 2026-08-07, Q6)
- ALWAYS 依存関係スキャンおよびシークレットスキャンをCI内で実行し、`main`へのマージをゲートする (affirmed 2026-08-07, Q7)

## Corrections

<!-- Project-specific corrections from human feedback. -->
<!-- Format: NEVER/ALWAYS [behavior] (learned [date]) -->
- ALWAYS pass --arguments "<short description>" when running intent-birth (including via the compose flow's birth step), so aidlc-state.md's Project field is populated instead of left at the birth-time placeholder [Project description] (learned 2026-08-05) (learned 2026-08-05) <!-- cid:intent-capture:c2 -->
- ALWAYS 仕様書やユーザー入力中の列挙リスト(CSV列、APIフィールド、要件一覧等)について要約の「N個」という数値を記載・引用する前に、実際に列挙項目を1件ずつ数え直して原本と照合する。特にその数値をproject.md/team.md等、複数の将来ステージが参照する永続メモリへ昇格する前には必ず照合する — 単位数表マスタCSVの列数が「19列」→正しくは「21列(全22列中)」と誤って複数ステージ・project.md/team.mdに伝播した実例から学んだ (learned 2026-08-10) (learned 2026-08-10) <!-- cid:requirements-analysis:c1 -->
- NEVER Storybookをブロッキングチェックのまま放置しない — CI設定(.github/workflows/ci.yml)ではStorybookジョブのみ`continue-on-error: true`として一時的に非ブロッキング化されている(既知の@storybook/nextjs + Next.js 14.2.35互換性問題、project.md DECIDED参照、ユーザー承認済み)。互換性問題の解決後は必ずブロッキングに戻すこと (learned 2026-08-12, Stage ci-pipeline) (learned 2026-08-12) <!-- cid:ci-pipeline:c1 -->
