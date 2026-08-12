# Code Generation Plan — csv-import-display (Bolt 1: Walking Skeleton)

Sources: `../../../inception/application-design/{components,component-methods,services,component-dependency,decisions}.md`, `../../../inception/requirements-analysis/requirements.md`, `../../../inception/practices-discovery/team-practices.md`.

units-generationはSKIPのため、本Boltは単一ユニット(プロジェクト全体)としてウォーキングスケルトンを構築する。技術スタック: Next.js/React/TypeScript, Radix UI, Tailwind CSS, Hono, Prisma, Aurora PostgreSQL(ローカルはPostgreSQL互換で代替), Zod, React Hook Form, SWR/Zustand/nuqs, openapi-fetch。

## Steps

- [x] Step 1: プロジェクト構造セットアップ(Next.js + TypeScript + package.json、tsconfig、Prisma初期化、Hono統合、Tailwind/Radix UI設定)
- [x] Step 2: データモデル/DBスキーマ — Prisma schema.prisma に単位数表マスタモデルを定義(FR-C1〜C2, ADR-4)
- [x] Step 3: ColumnMappingTable — CSV日本語/カタカナ列名↔camelCase識別子の対応表(FR-E1)。単一の情報源として、Zodスキーマ・Prismaモデル定義がこれを参照する
- [x] Step 4: MasterDataSchema(Zod) — 21列(予備項目を除く)の形式検証ルール(FR-B2の表、6桁YYMMDD形式含む)
- [x] Step 5: ビジネスロジック層 — CsvParserService(Shift-JISデコード、構造検証、FR-A5)、CsvValidationService(collect-all集約、FR-B1〜B5)
- [x] Step 6: ビジネスロジック層のユニットテスト — CsvParserService・CsvValidationServiceのテスト(正常系、境界値、複数同時違反等)
- [x] Step 7: データアクセス層 — MasterDataRepository(Prisma経由のupsertMany、findDistinctInsurerNumbers、findDistinctServiceTypeCodes、findByFilter、FR-C1〜C2, FR-D1〜D3)
- [x] Step 8: API層(Hono) — CsvImportRoute(POST /api/csv-import)、FilterOptionsRoute(GET /api/filter-options)、SearchRoute(GET /api/master-data)。OpenAPI仕様定義
- [x] Step 9: APIテスト — 各ルートのユニット/統合テスト(成功、構造エラー、検証エラー、パラメータ欠落等)
- [x] Step 10: フロントエンドコンポーネント — ImportUploadComponent、SearchFilterComponent(コンテナ)、SearchResultsComponent(表示専用)、対応するNext.jsページ
- [x] Step 11: フロントエンドテスト — 各コンポーネントのコンポーネントテスト(Storybook)、インタラクションテスト
- [x] Step 12: 設定・環境セットアップ — .env.example、DATABASE_URL、openapi-fetch用のクライアント設定
- [x] Step 13: テスト設定 — Vitest設定、Testcontainers設定(Postgres統合テスト用)、Playwright設定、MSW設定、Storybook設定
- [x] Step 14: ドキュメント — READMEへのセットアップ手順記載、APIドキュメント(OpenAPI仕様から自動生成想定)

## テスト戦略(Standard)

- Standard戦略: 各コンポーネントにユニットテスト5〜8件 + 主要な境界に統合テストのスタブ(`stage-protocol.md` §8)
- CsvValidationServiceは21列すべてに対する正常値・境界値・異常値、および複数同時違反のケースを含める(`team-practices.md` Testing Posture)
- Testcontainersを用いたMasterDataRepositoryの統合テスト(実際のPostgreSQLコンテナに対するupsert/クエリ)

## Plan Approval

[Answer]:
