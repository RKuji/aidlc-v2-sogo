# Team-Level Rules

> This team's affirmed practices and corrections. Loaded after `org.md` as
> strict-additive guidance; contradictions with broader policy are rejected.
> Populated by the practices-discovery affirmation gate. Edit at the gate,
> not directly.

## Way of Working

`org.md` のデフォルトどおり、トランクベース開発とする(Q1、そのまま確認)。
すべての作業は短命なフィーチャーブランチを経由して `main` にマージされる。
Construction のワークツリーについては、ワークツリーのベースブランチは
`main` であり、マージ先も `main` である。Bolt ブランチは `main` に
スカッシュマージする: 各 Bolt はトランク上で1つのコミットとなり、Bolt の
スラッグ名で命名され、ワークツリーが破棄されるまで、完全な Bolt のコミット
履歴がソースブランチ上に保持される。

## Walking Skeleton

このスコープ(`csv-master-import-display` は `skeleton: on` を宣言)では
Walking Skeleton は**有効(ON)**である(Q2)。Bolt 1 は単独かつゲート付きで
実行される — 以降の Bolt が実行される前に、ユーザーが明示的に承認する。
Bolt 1 のリリース後、オーケストレーターははしごプロンプト(「自律的に続行」
対「すべての Bolt をゲートする」)を発火する。チームの選択は
`aidlc-state.md` の `Construction Autonomy Mode` として永続化される。

## Testing Posture

**org.md のデフォルトより厳格。** 確認済みのすべてのテストツール — Vitest、
Testcontainers、Storybook、MSW、Playwright、MagicPod — は、マージ前に
**ブロッキング**な CI ゲートとして実行される(Q3、選択肢B)。本プロジェクトの
スタックにおいて、非ブロッキング/ナイトリー実行に据え置かれるテストツールは
存在しない。6つのツールすべてが通過しない限り、PR はマージできない。

19列の単位数表マスタ CSV に対する CSV 検証のエラーハンドリングは、
フェイルファストではなく**全件収集(collect-all)**とする(Q4、選択肢B):
バリデーターは、最初に検出した違反で停止するのではなく、19列すべてのルールを
各行に対して実行し、**違反の完全なリスト**を一度のパスで返さなければならない。
これにより、サービス境界における結果型の形状(例: 最初のエラーで例外を
スローする方式ではなく、型付けされた違反リストを返す結果型)、および
テストフィクスチャ戦略(フィクスチャは1行あたり単一違反のケースだけでなく、
複数の同時違反も検証しなければならない)が決まる。

最低80%のライン カバレッジは引き続き下限とする(org.md のデフォルトであり、
インタビューでは変更されていない)。カバレッジ計測の仕組みおよびツールごとの
CI 組み込みは、上記「6ツールすべてがブロックする」という決定に制約される、
ci-pipeline ステージでの実装詳細である。

## Deployment

`org.md` のデフォルトどおり、staging へはマージ時にデプロイする(Q5、
そのまま確認)。本番デプロイは、昇格前に別途の手動承認(テックリード+
プロダクトオーナーの承認)をゲートとする。

## Code Style

Prettier(フォーマッター)と ESLint(リンター)を用い、リンター設定は
汎用的なフレームワークの提案に優先する — org.md のデフォルトどおり、
そのまま確認(Q6)。加えて、本プロジェクトでは**3層アーキテクチャ**を
採用することを確認する:

- **Hono API 層** — トランスポート/HTTP に関する関心事のみ(ルーティング、
  リクエストのパース、レスポンスの整形)。
- **サービス層** — ビジネスロジック。19列の CSV 検証ルールおよび
  全件収集(collect-all)の違反集約を含む。
- **Prisma データ層** — 永続化のみ。

CSV の列名は、ソース形式では日本語/半角カタカナである(例: 証記載保険者
番号、ｻｰﾋﾞｽ種類ｺｰﾄﾞ、ｻｰﾋﾞｽ項目ｺｰﾄﾞ)。コード内 — TypeScript の型、Zod
スキーマのキー、Prisma のモデル/カラム名 — では、これらを英語の camelCase
識別子にマッピングする。マッピング表(ソースの列名 → camelCase 識別子)を
文書化し、スキーマ/検証コードと合わせて維持しなければならない。これにより、
マッピングは一度だけ導出され、3層すべてで再利用され、層ごとに個別に
再導出されることがないようにする。

依存関係スキャン(例: `npm audit`/Dependabot 相当)およびシークレット
スキャンは CI の一部とし、マージをゲートする(Q7、選択肢A)。SAST および
DAST は本プロジェクトでは明示的に**対象外**とする — 社内業務システムで
あり、チームはこのスコープに対して追加のパイプライン投資は不要と判断した。

## Forbidden

<!-- Team-specific forbidden patterns -->

## Mandated

<!-- Team-specific mandates -->

## Corrections

<!-- Self-learning loop appends here. -->
