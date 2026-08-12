# Code Summary — csv-import-display (Bolt 1: Walking Skeleton)

## 作成ファイル

`code-generation-plan.md`の全14ステップ完了時のファイルツリー(`/home/mgdoc/work/src/sogo/.aidlc/worktrees/bolt-csv-import-display/`配下):

- 設定: `package.json`, `tsconfig.json`, `next.config.mjs`, `tailwind.config.ts`, `postcss.config.js`, `.eslintrc.json`, `.prettierrc.json`, `.env.example`, `README.md`
- テスト設定: `vitest.config.mts`, `vitest.integration.config.mts`, `playwright.config.ts`, `.storybook/{main.ts,preview.ts}`
- ドキュメント: `docs/openapi.yaml`, `docs/column-mapping.md`
- データ層: `prisma/schema.prisma`, `prisma/migrations/20260807000000_init/migration.sql`
- 共有: `src/shared/{column-mapping.ts,types.ts,api-types.ts}`(FR-E1: 単一の列名マッピング情報源)
- サーバー: `src/server/{app.ts,prisma.ts}`, `src/server/schema/master-data-schema.ts`(Zod、FR-B2の21列)
- サービス層: `src/server/services/{csv-parser-service.ts,csv-validation-service.ts}`
- データアクセス層: `src/server/repositories/master-data-repository.ts`
- API層(Hono): `src/server/routes/{csv-import-route.ts,filter-options-route.ts,search-route.ts}`, `src/app/api/[[...route]]/route.ts`
- フロントエンド: `src/components/{ImportUpload,SearchFilter,SearchResults}/*`, `src/app/{layout.tsx,page.tsx,import/page.tsx,search/*}`
- テスト: `test/unit/**`(21列検証・パーサー・ルート・コンポーネント)、`test/integration/master-data-repository.integration.test.ts`(Testcontainers)、`e2e/navigation.spec.ts`(Playwright)

## 主要な実装判断

- ColumnMappingTable(`src/shared/column-mapping.ts`)を唯一の情報源とし、Zodスキーマ・Prismaモデル定義の両方がこれを参照する構成とした(FR-E1、project.md Mandated)。
- CsvValidationServiceは21列すべてに対しcollect-all方式で違反を収集し、`ValidationViolation[]`(行番号・列名・ルール・実際の値)を返す判別可能Union型で表現(FR-B1〜B5)。
- 構造検証(列数22・空ファイル・Shift-JISデコード失敗)はCsvParserServiceで値検証より前段に分離し、FR-A5の要求どおりcollect-all方針の対象外として実装。
- MasterDataRepository.upsertManyは保険者番号+サービス種類コード+サービス項目コード+適用開始年月日を複合一意キーとしたupsertを実装(FR-C2, ADR-4)。

## テストカバレッジ

- `npx vitest run`: 43/43件パス、ライン カバレッジ80%以上を確認(NFR-1)。
- `npx tsc --noEmit`: クリーン。
- `npx eslint .`: クリーン。
- `npm run build`(Next.js本番ビルド): 成功。

## プランからの逸脱

- Storybookの`build`/`dev`が、`@storybook/nextjs`とNext.js 14.2.35のwebpack5ビルダー間の既知の互換性問題(`SB_BUILDER-WEBPACK5_0002`)により失敗する。ストーリーファイル自体(`.stories.tsx`)は妥当なTSXであり、コードの欠陥ではない。ci-pipelineステージでのフォローアップ課題として記録する。

## Assumptions & Open Questions

None.

## Review

**Verdict: READY**

**レビュアー:** aidlc-architecture-reviewer-agent
**日付:** 2026-08-12T01:32:36Z
**イテレーション:** 1

### 検証方法

以下はすべて実際にワークツリー(`/home/mgdoc/work/src/sogo/.aidlc/worktrees/bolt-csv-import-display/`)で再実行し、コード本体(column-mapping.ts、master-data-schema.ts、csv-validation-service.ts、csv-parser-service.ts、master-data-repository.ts、3ルート、prisma/schema.prisma、フロントエンド3コンポーネント)を読み、requirements.md(FR-A〜E、NFR-1〜6)・component-methods.md・services.md・component-dependency.md・team-practices.mdと逐一照合した。

### ビルド・テストの再実行結果(claim検証)

| コマンド | 結果 | code-summary.mdの主張との一致 |
|---|---|---|
| `npx tsc --noEmit` | クリーン(エラー0件) | 一致 |
| `npx eslint .` | クリーン(exit code 0) | 一致 |
| `npx vitest run` | 9 test files / 43 tests すべてpass | 一致 |
| `npx vitest run --coverage` | 全体ライン カバレッジ84.98%(≥80%) | 一致(ただしmaster-data-repository.tsは単体では10.81%——複合キーupsertの主要検証はTestcontainers統合テスト側が担っており、これは意図された分離である) |
| `npm run build` | 成功(6ルートを正常に生成) | 一致 |
| `npx storybook build`(claim検証のため実行) | **失敗**: `SB_BUILDER-WEBPACK5_0002` | code-summary.mdの「プランからの逸脱」記載どおり、実際に再現することを確認した |

### 所見

| # | 深刻度 | 場所 | 所見 | 推奨対応 |
|---|---|---|---|---|
| 1 | Major | package.json(`storybook: ^8.3.4`, `next: 14.2.35`), code-summary.md「プランからの逸脱」 | Storybookのbuild/devが現在の依存バージョン組み合わせで実際に失敗することを確認した(`SB_BUILDER-WEBPACK5_0002`)。team-practices.md Testing Posture(project.md Mandated)は「確認済みの6ツールすべてをマージ前にブロッキングなCIゲートとして実行する」を明記しており、非ブロッキング/据え置きの例外は存在しないと宣言している。Storybookがビルドすら成功しない現状では、この6ツール全ブロッキングというプロジェクト必須方針をci-pipelineステージで満たせない。builderはこれを既知の課題として诚実に記録しているが、未解決のまま次のBoltに引き継がれるリスクがある。 | ci-pipelineステージ着手前に、Storybook/Next.jsの互換バージョン組み合わせ(例: `@storybook/nextjs`のマイナーバージョン固定、またはNext.js側の調整)を検証し、解決策(または正式な例外の承認)をci-pipelineステージの入力として明示的に引き渡すこと。 |
| 2 | Minor | component-methods.md「SearchFilterComponent」の`onFilterSubmit(insurerNumber, serviceTypeCode): Promise<void>`という記法、対 `SearchFilterComponent.tsx`の実装(`onResultsChange: (records) => void`のみをpropsとして受け取り、内部でSearchRouteを自前で呼び出す) | 設計文書内の先頭の型シグネチャと、直後の説明文(「本コンポーネントがSearchRouteを呼び出し…データ取得責務はSearchFilterComponentが一元的に持ち」)が矛盾しており、実装は説明文の挙動には正確に一致するが、先頭のメソッドシグネチャ表記とは一致しない。コード自体の欠陥ではなく、application-designの成果物側の表記不整合である。 | 次回のcomponent-methods.md更新時に、先頭のシグネチャ行を実装済みの`onResultsChange`ベースの記法に揃える。 |

### 個別要件の確認結果

- **FR-B2(21列)**: `column-mapping.ts`の22エントリ(列12=予備項目のみ`isValidated: false`)と`master-data-schema.ts`の`MASTER_DATA_COLUMN_RULES`が1:1で対応し、起動時アサーション(90-96行目)で未定義列の混入を防いでいる。日付列(4/5列目、YYMMDD 6桁)、単位数(-9999〜99999)、給付率(1〜100)、利用者負担額(5桁まで)を含む全21ルールをrequirements.mdの表と逐一突き合わせ、すべて一致することを確認した。列12(予備項目)は`isValidated: false`で検証対象外だが、`toMasterDataRecord`では`reservedItem`として値そのものは保持されており、「検証対象外」と「データとして保存しない」が正しく区別されている。
- **collect-all(FR-B1)**: `CsvValidationService.validate()`は全行をforループで走査し、`validateRow`内でも全21列を走査してから結果を返す。早期return/breakは存在しない。ユニットテスト(`csv-validation-service.test.ts`)は同一行内の複数同時違反、複数行にわたる違反の両方を明示的に検証しており、team-practices.mdの「フィクスチャは複数の同時違反も検証しなければならない」という要求を満たしている。
- **ColumnMappingTable単一情報源(FR-E1)**: `master-data-schema.ts`・`shared/types.ts`・`prisma/schema.prisma`のいずれも日本語列名を独自に再定義しておらず、camelCaseキーのみを個別に列挙する形で`column-mapping.ts`の`camelCaseKey`と1:1対応している。Prismaモデルはコメントで明示的にColumnMappingTable経由であることを記載。
- **FR-C2複合キーupsert**: `master-data-repository.ts`の`upsertMany`は`@@unique([insurerNumber, serviceTypeCode, serviceItemCode, applicableStartDate], name: "masterDataUniqueKey")`を`where`条件に使用し、Testcontainers統合テストで新規作成・上書きの両方を検証している。`tsc --noEmit`のクリーンはPrisma生成型との整合(複合キー名の一致)も含めて確認済み。
- **3ルートのレスポンス形状(component-methods.md)**: `POST /api/csv-import`(200/{success,importedRowCount}、422/{success,violations}、400/{success,structuralError})、`GET /api/filter-options`(200/{insurerNumbers,serviceTypeCodes})、`GET /api/master-data`(200/{records}、400/{error: "insurerNumber and serviceTypeCode are required"})はいずれも文言・ステータスコードとも設計文書と完全一致することを確認した。
- **3層アーキテクチャ(NFR-6)**: Prismaへの直接アクセスは`master-data-repository.ts`に閉じており、ルートはサービス/リポジトリ経由でのみデータにアクセスする。依存の向きはフロントエンド→API→サービス→データの一方向で、循環依存は確認されなかった。
- **セキュリティ/シークレット**: `.env.example`はローカル開発用のダミー接続文字列のみで、実際の資格情報のハードコードは見られない。DB呼び出し(`upsertMany`, `findByFilter`等)・ファイル解析(`CsvParserService.parse`)はいずれも例外を握り込まず、判別可能Union型またはHTTP 500への変換という形で呼び出し元に伝播させている(construction phase guardrails準拠)。

### 総括

component-methods.md/requirements.mdに対する主要な契約(21列検証ルール、collect-all、列名マッピング単一情報源、複合キーupsert、3ルートのレスポンス形状)はすべてコードと文書照合・実行検証の両方で確認でき、致命的な逸脱は見つからなかった。唯一の実質的な懸念はStorybookビルドの既知の互換性問題で、これはteam-practices.mdの「6ツール全ブロッキング」という必須方針に対する未解決の技術的リスクだが、builderが正直に開示し次ステージへの引き継ぎ課題として明記している。Critical所見0件、Major所見1件のため、判定はREADYとする。
