# Outcomes Pack

**Scope**: `csv-master-import-display`
**Stages delivered**: 11 approved / 12 total (per `aidlc-runtime.ts summary`; see §7 note — the workflow's own state file and the orchestrator's terminal `next` call both confirm all 12 in-scope stages are actually `[x]` completed/approved as of 2026-08-12)
**Duration**: 9963 min (workflow spanned 2026-08-05 → 2026-08-12 across multiple sessions)

## 1. What Was Built

介護予防・日常生活支援総合事業費の**単位数表マスタ(CSV)の取り込み・表示**機能。既存実装なしの新規グリーンフィールド機能。

- **取込**: ローカルCSVファイル(Shift-JIS)を1件アップロードし、22列中21列(予備項目を除く)を検証。1件でも違反があれば取り込みを行わず、collect-all方式で全違反(行番号・列名・ルール・実際の値)を画面に表示する。検証に成功した行は、保険者番号+サービス種類コード+サービス項目コード+適用開始年月日を複合キーとしてupsert保存する。
- **表示**: 取込済みの保険者番号・サービス種類コード(それぞれ独立したドロップダウン、カスケード絞り込みなし)を選択すると、一致する登録済みデータを表形式で表示する。主要列のみ表示し、詳細は行クリックで展開する。

**スコープ**: `csv-master-import-display`(Standard深度、composerによるカスタムスコープ。12/32ステージ実行、他はfold/skip)。

**ユニット**: units-generationがSKIPのため単一ユニット。取込・表示の2つの機能領域を、共有の`MasterDataRepository`と`ColumnMappingTable`を介して結合する構成。

**主要なアーキテクチャ決定**(`aidlc/spaces/default/intents/260805-csv-master/inception/application-design/decisions.md`より):
- ADR-1: 取込・表示を2つの独立した機能領域に分割(テストしやすさ、NFR-1のカバレッジ目標のため)
- ADR-2: フロントエンド-バックエンド間はopenapi-fetch経由(技術スタックの選定に整合)
- ADR-3: CSV検証は専用のサービス層コンポーネント(CsvValidationService)に配置(3層アーキテクチャの原則)
- ADR-4: 単位数表マスタは単一モデル+upsert(履歴テーブルなし、要件のOut of Scopeと整合)
- ADR-5: コンポーネント間通信はすべて同期方式(YAGNI、性能目標が定義されていないため)

**技術スタック**(バージョン固定):
| カテゴリ | 技術 |
|---|---|
| フロントエンド | Next.js 14.2.35(App Router)、React、TypeScript |
| UIコンポーネント | Radix UI、Tailwind CSS、CVA、clsx、tailwind-merge |
| 状態管理 | SWR、Zustand、nuqs |
| フォーム/検証 | React Hook Form、Zod |
| API層 | Hono 4.6、OpenAPI、openapi-fetch |
| データ層 | Prisma 5.20、PostgreSQL(本番: Aurora PostgreSQL) |
| テスト | Vitest 2.1、Testcontainers、Storybook 8.6、MSW、Playwright |

## 2. Repository Structure

実装は本ワークフローのAI-DLC管理リポジトリ(`sogo/`)とは別の**sibling repository** `aidlc-v2-sogo-package/` に配置されている(Bolt worktreeの技術的制約により、code-generationステージで意図的に分離。詳細は§8参照)。

```
aidlc-v2-sogo-package/
├── .github/workflows/ci.yml       # GitHub Actions CI設定
├── prisma/
│   ├── schema.prisma               # 単位数表マスタのデータモデル
│   └── migrations/                 # 初回マイグレーション
├── src/
│   ├── shared/
│   │   ├── column-mapping.ts       # 日本語列名↔camelCase の単一情報源(FR-E1)
│   │   ├── types.ts / api-types.ts
│   ├── server/
│   │   ├── app.ts, prisma.ts       # Honoアプリ、Prisma Clientセットアップ
│   │   ├── schema/master-data-schema.ts   # Zodによる21列の形式検証
│   │   ├── services/               # CsvParserService, CsvValidationService(サービス層)
│   │   ├── repositories/           # MasterDataRepository(データ層、唯一のDBアクセス経路)
│   │   └── routes/                 # CsvImportRoute, FilterOptionsRoute, SearchRoute(API層)
│   ├── components/                 # ImportUpload, SearchFilter, SearchResults(フロントエンド)
│   └── app/                        # Next.jsページ(/, /import, /search)
├── test/                           # ユニットテスト(43件)、統合テスト(Testcontainers)
├── e2e/                            # Playwright E2Eテスト
└── docs/{openapi.yaml,column-mapping.md}
```

AI-DLCのワークフロー記録(要件・設計・決定事項・レビュー履歴の全文)は `sogo/aidlc/spaces/default/intents/260805-csv-master/` 配下に保存されている。

## 3. Setup Guide

### 前提条件
- Node.js 20系
- npm
- Docker(統合テスト実行時のみ、Testcontainers用)
- PostgreSQL接続先(ローカルDBまたはAurora PostgreSQL)

### ローカル開発セットアップ
```bash
cd aidlc-v2-sogo-package
npm install
cp .env.example .env        # DATABASE_URLを実際の接続文字列に設定
npm run prisma:generate
npm run prisma:migrate
npm run dev                 # または npm run build && npm start
```

### 必須環境変数
- `DATABASE_URL` — PostgreSQL接続文字列(`.env.example`参照)

### テストの実行方法
```bash
npx tsc --noEmit                                    # 型チェック
npx eslint .                                        # リント
npx vitest run --coverage                           # ユニットテスト(43件、カバレッジ目標80%以上)
npx vitest run --config vitest.integration.config.mts  # 統合テスト(Docker必須)
npx playwright install --with-deps && npx playwright test  # E2E
npx storybook build                                 # ⚠️既知の互換性問題で失敗する(§8参照)
```

## 4. Build and Deploy

### ビルド
```bash
npm run build   # Next.js本番ビルド(tsc/eslintを内包)
```

### テストスイート全体
`.github/workflows/ci.yml`(GitHub Actions)が、PRおよびmainへのpush時に以下を自動実行する: type-check, lint, unit-tests(カバレッジゲート付き), integration-tests, e2e-tests, storybook-build(非ブロッキング), dependency-scan(`npm audit`), secret-scan(gitleaks)。

### インフラデプロイ

infrastructure-design/environment-provisioning/deployment-pipelineステージは本スコープではすべてSKIP。デプロイ方針はorg.md/team.mdの一般方針(マージ時にstaging自動デプロイ、production は手動承認)のみが確認されており、本ワークフローでは実際のデプロイ設定(IaC等)は生成していない。運用フェーズで別途構築が必要。

## 5. Architecture Decisions

`aidlc/spaces/default/intents/260805-csv-master/inception/application-design/decisions.md`にADR-1〜5(Context/Decision/Consequences/Alternatives Rejected/Security-Compliance付き)を全文記録済み。要点は§1参照。

**制約となった主な決定・練習事項**(practices-discovery、`team.md`):
- トランクベース開発、Boltブランチのsquash-merge
- 全6テストツール(Vitest/Testcontainers/Storybook/MSW/Playwright/MagicPod)をブロッキングCIとする方針 — ただしStorybookは既知の互換性問題により一時例外(§8)
- CSV検証はフェイルファストではなくcollect-all必須
- 日本語/カタカナ列名は単一のマッピング表を介してcamelCaseに変換(3層すべてが参照)

## 6. What to Commit vs Archive

| Artifact | Action | Destination |
|---|---|---|
| `inception/application-design/decisions.md` | Commit | `aidlc-v2-sogo-package/docs/decisions.md` |
| アーキテクチャ概要(1ページ) | 新規作成して commit | `aidlc-v2-sogo-package/docs/architecture.md`(未作成 — 推奨事項) |
| `inception/requirements-analysis/requirements.md`のNFRセクション | Commit(要約) | `aidlc-v2-sogo-package/docs/nfr-summary.md`(未作成 — 推奨事項) |
| `<record>/audit/*.md` | コミットしない — コンプライアンス用に別途アーカイブ | 監査アーカイブ |
| 各ステージの`*-questions.md` | 破棄可 | — |
| `aidlc-state.md` | 破棄可 | — |
| アプリケーション/テストコード | 既にコミット済み | `aidlc-v2-sogo-package`(コミット`27908a5`) |
| `.github/workflows/ci.yml` | 既にコミット済み | `aidlc-v2-sogo-package`(コミット`27908a5`) |

## 7. Workflow Footprint

`aidlc-runtime.ts summary --json`より:
- ステージ: 11 approved、0 failed、1 pending(全12ステージ中)
  - **注記**: この「1 pending」はサマリーツールの集計と、ワークフロー本体の状態(`aidlc-state.md`の`Status: Completed`、全12ステージ`[x]`、オーケストレーターの`next`が`"Workflow complete"`を返した事実)との間に食い違いがある。実際にはci-pipelineを含む全12ステージが承認済みであり、この差異はツール側の集計タイミングの問題と見られる(数値の食い違いを隠さず、そのまま報告する)。
- フェーズ別: Initialization 3/3、Ideation 3/3、Inception 3/3、Construction 2/3(pending表記、上記注記参照)
- メモリエントリ: 合計12件(解釈3、逸脱6、トレードオフ0、未解決事項3)
- 学び: オーケストレーターから7件記録(project.md/team.mdへ昇格済み)、ユーザー追加0件
- センサー: 455回発火、422件パス、33件失敗(主にintent-capture初期のclaim-sourcesの出典不一致など、advisoryレベルの指摘であり大部分は後続の修正で解消済み)

## 8. Known Limitations and What to Tackle Next

- **Storybookビルド不可**(既知のアップストリーム互換性問題、`@storybook/nextjs` 8.6.18 × Next.js 14.2.35、`SB_BUILDER-WEBPACK5_0002`)。CI上は一時的に非ブロッキング。Next.jsダウングレードまたはStorybookメジャーアップグレード(9.x/10.x)での解決が必要(`project.md` DECIDED参照)。
- **MagicPod受入テスト未設定**。`.github/workflows/ci.yml`にジョブは用意済みだが`if: false`で無効化中。実際の組織/プロジェクト/APIトークンの設定が必要。
- **Testcontainers統合テストがこのワークフロー実行環境では未実行**(Dockerデーモンへの権限なし)。CI環境(GitHub Actions)での実行結果の確認が次の検証ポイント。
- **サービス項目コードの値の妥当性検証が未定**(桁数・数値形式のみ検証、外部の指定コードマスタへの照合は本スコープに含まれていない、`requirements.md` Open Questions参照)。
- **証記載保険者番号マスタの追加**は将来の拡張候補として記録済み(`scope-document.md`)、今回のスコープでは対象外。
- **デプロイ・インフラ構成が未着手**(infrastructure-design/environment-provisioning/deployment-pipelineはすべてSKIP)。運用フェーズで別途必要。
- **AI-DLCフレームワーク自体の既知の不具合**: `aidlc-worktree.ts`/`aidlc-bolt.ts`のBolt worktree機構が、`aidlc/`ディレクトリがgit管理されているリポジトリ内でBolt worktreeを作成すると`audit-fork`の一発限りガードと衝突する(今回はsibling repoへの切り出しで回避)。フレームワーク保守者への報告が推奨される。
