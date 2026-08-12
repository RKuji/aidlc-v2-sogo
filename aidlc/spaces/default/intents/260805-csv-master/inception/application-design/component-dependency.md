# Component Dependency — 単位数表マスタ(CSV)の取り込み・表示

Sources: `components.md`, `services.md`。

## 依存関係マトリクス

| コンポーネント | 依存先 | 通信パターン |
|---|---|---|
| ImportUploadComponent | CsvImportRoute | 同期(HTTP, openapi-fetch) |
| CsvImportRoute | CsvParserService | 同期(関数呼び出し) |
| CsvImportRoute | CsvValidationService | 同期(関数呼び出し) |
| CsvImportRoute | MasterDataRepository | 同期(関数呼び出し) |
| CsvValidationService | ColumnMappingTable, MasterDataSchema(Zod) | 同期(参照) |
| MasterDataRepository | ColumnMappingTable | 同期(参照、Prismaモデル定義) |
| SearchFilterComponent | FilterOptionsRoute | 同期(HTTP, openapi-fetch) |
| SearchFilterComponent | SearchRoute | 同期(HTTP, openapi-fetch) — 条件確定時にデータ取得を行い、結果をSearchResultsComponentへpropsとして渡す |
| SearchResultsComponent | (なし。HTTP呼び出しを行わない表示専用コンポーネント) | — |
| SearchRoute | MasterDataRepository | 同期(関数呼び出し) |
| FilterOptionsRoute | MasterDataRepository | 同期(関数呼び出し) |

## データフロー

**取込フロー**: ImportUploadComponent → (HTTP) → CsvImportRoute → CsvParserService(構造検証) → CsvValidationService(21列値検証、collect-all) → MasterDataRepository(upsert) → Aurora PostgreSQL。検証失敗時はMasterDataRepositoryを呼び出さず、CsvImportRouteが違反リストをそのままImportUploadComponentへ返す。

**表示フロー**: SearchFilterComponent → (HTTP) → FilterOptionsRoute → MasterDataRepository(重複排除クエリ) → Aurora PostgreSQL(ドロップダウンの選択肢を取得、保険者番号・サービス種類コードは独立した2つのドロップダウンでカスケード絞り込みは行わない)。ユーザーが両条件を選択すると、SearchFilterComponent(データ取得責務を一元的に持つ) → (HTTP) → SearchRoute → MasterDataRepository(条件一致クエリ) → Aurora PostgreSQL。取得結果はSearchFilterComponentから表示専用のSearchResultsComponentへpropsとして渡される(SearchResultsComponent自身はHTTP呼び出しを行わない)。

## 共有リソース

- **MasterDataRepository**は取込・表示の両機能領域から共有される唯一のデータアクセスコンポーネントである(`components.md`参照)。
- **ColumnMappingTable**はCsvValidationService・MasterDataSchema・MasterDataRepository(Prismaモデル定義)の3箇所から参照される共有の単一情報源である(FR-E1)。
- **Aurora PostgreSQLデータベース**は両機能領域が共有する唯一の永続化ストアである。

## 依存の向き(循環依存なし)

フロントエンド → API層 → サービス層 → データ層、の一方向のみであり、下位層から上位層への依存(例: MasterDataRepositoryがCsvValidationServiceを呼ぶ等)は存在しない。取込機能領域と表示機能領域は互いに直接依存せず、MasterDataRepositoryとColumnMappingTableのみを介して間接的に結合する。

## Assumptions & Open Questions

None.
