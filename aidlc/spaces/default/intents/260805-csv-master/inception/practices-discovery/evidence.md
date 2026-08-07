# エビデンス — 最終版(ステップ5: リード統合)

> ステータス: **最終(FINAL)**。intent `260805-csv-master` の
> practices-discovery ステージにおいて、各参加者が調査/推論した内容と、
> 人間によるインタビュー(ステップ4)がすべてのギャップをどう解決したかを
> 記録する。

## ステップ2 — リード自身による調査

1. `aidlc/spaces/default/memory/org.md` — 関連する5つのセクション
   (`## Way of Working`、`## Walking Skeleton`、`## Testing Posture`、
   `## Deployment`、`## Code Style`)をすべて精読。フレームワークの
   デフォルトであり、チーム承認済みの事実ではないため、明示的な提案として
   `team-practices.md` へコピーした。
2. リポジトリルート(`/home/mgdoc/work/src/sogo`) — 純粋なグリーンフィールド
   であることを確認: アプリケーションのソースツリーなし、`package.json`
   なし、CI 設定なし、テストツール設定(`vitest.config.*`、
   `playwright.config.*`、Testcontainers/MSW/Storybook/MagicPod の
   セットアップ)なし、デプロイ/IaC 設定なし、リンター/フォーマッター
   設定なし。

## ステップ3 — 支援エージェントの貢献

**aidlc-quality-agent**(`contributions/aidlc-quality-agent.md`):
テストツール選定のギャップが非デフォルトとして正しくフラグ付けされている
ことに AGREE(同意)。ドラフトが「ツールリストを承認する」だけで止まっており、
(a) カバレッジ計測の仕組み、(b) 6つのテストツールのうちどれが CI を
ブロックし、どれが据え置き実行なのか、(c) Playwright/MagicPod の E2E
所有範囲の重複、を提示していない点に OBJECT(異議)。→ **解決済み**:
Q3 で CI ブロッキングの問題を直接尋ね、人間は最も厳格な選択肢(6ツール
すべてが CI をブロックする)を選んだため、両方が現在プリマージをブロック
することになり、Playwright/MagicPod の実行サイクル分割に関する懸念も
解消された。カバレッジ計測の仕組みの詳細(例: `@vitest/coverage-v8`)は、
practices-discovery ステージの決定事項ではなく、ci-pipeline ステージの
実装詳細のままとする。

**aidlc-developer-agent**(`contributions/aidlc-developer-agent.md`):
ドラフトが org.md の値を提案として正しく扱っている点に AGREE(同意)。
`## Code Style` が汎用的な定型文にとどまり、日本語/カタカナの CSV 列名に
対する命名/マッピング規約、レイヤー境界(Hono → サービス → Prisma)、
フェイルファスト対全件収集のエラーハンドリング決定、ファイル構成、
TypeScript 固有のスタイル(strict モード、`.safeParse`)が欠けている点に
OBJECT(異議)。→ **解決済み**: Q4 で全件収集(collect-all)の
エラーハンドリングを確定し、Q6 で3層構造と camelCase マッピング表の要件を
確定した。ファイル構成の詳細(フィーチャーフォルダ対レイヤーフォルダ)と
TypeScript の strict モード/`.safeParse` の規約は、支援エージェントも
人間もこのステージのブロッカーとしてフラグ付けしなかったため、
practices-discovery のインタビュー項目に昇格させず、build-and-test/
code-generation ステージの実装上の選択として残す。

**aidlc-devsecops-agent**
(`contributions/aidlc-devsecops-agent.md`): グリーンフィールドの
セキュリティ態勢に関する所見に AGREE(同意)。`## Code Style` が
セキュリティ関連のリントツールを示すべきであるという点、および
SAST/DAST/シークレットスキャン/依存関係スキャンのいずれもドラフト内で
プレースホルダーとしてすら言及されていない点の2点について OBJECT(異議)。
→ **解決済み**: Q7 でセキュリティ/サプライチェーンに関する問いを直接
尋ね、人間は CI 内(ブロッキング)での依存関係スキャン+シークレット
スキャンを選択し、SAST/DAST はこの社内システムでは明示的に対象外とすることを
選んだ。`eslint-plugin-security`/厳格な TS リントルールの具体的内容は、
スキャンに関する方針レベルの決定(スキャン: あり(依存関係+シークレット)、
SAST/DAST: なし)が確定した以上、code-style/ci-pipeline ステージで具体的な
ツールを選定する際に委ねる。

## ステップ4 — 人間によるインタビューでの解決

`practices-discovery-questions.md` の全7問すべてに回答が得られた:

- **Q1**(Way of Working): org.md のデフォルト(トランクベース、
  スカッシュマージ)をそのまま確認。
- **Q2**(Walking Skeleton): このスコープでは `skeleton: on` を確認。
  Bolt 1 は単独かつゲート付き、その後にはしごプロンプト。
- **Q3**(Testing Posture — CI ゲート): 人間は**より厳格な**選択肢Bを
  選んだ — 6つのテストツール(Vitest、Testcontainers、Storybook、MSW、
  Playwright、MagicPod)すべてが CI をブロックし、選択肢Aで提案された
  org デフォルトに沿った分割案は採用しなかった。
- **Q4**(Testing Posture — CSV エラーハンドリング): 人間は
  **全件収集(collect-all)**(選択肢B)を選び、developer-agent の
  `Result<T, ValidationError[]>` という枠組みが未決のまま残していた
  フェイルファストの選択肢Aを退けた。
- **Q5**(Deployment): org.md のデフォルト(staging へのマージ時
  デプロイ、本番への手動ゲート)をそのまま確認。
- **Q6**(Code Style & レイヤリング): org.md のデフォルトに加えて、
  3層構造(Hono → サービス → Prisma)と日本語/カタカナ →
  camelCase のマッピング表要件を、選択肢Aとして確認。
- **Q7**(Security & Supply-Chain): CI 内での依存関係スキャン+
  シークレットスキャン、SAST/DAST は対象外を、選択肢Aとして確認。

いずれの問いも「C. 未定義」のまま残されておらず、「X. その他」で回答された
ものもない。インタビュー自体の「前提事項と未解決の問い」セクションには
「なし」と記載されている。

## 結論

リードのドラフトおよび3つの支援エージェントの貢献において提起された
全7項目は、ステップ4のインタビューにおける人間の明示的な回答によって
解決された。**本 practices-discovery ステージにおいて未解決の不確実性は
残っていない。** `team-practices.md` と `discovered-rules.md` は、最終的に
確定した決定(Q3 と Q4 が元の org.md デフォルトのドラフトよりも厳格/
異なることに留意)を反映するよう書き直された。
