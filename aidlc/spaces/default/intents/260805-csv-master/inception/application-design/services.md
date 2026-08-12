# Services — 単位数表マスタ(CSV)の取り込み・表示

Sources: `components.md`, `component-methods.md`, `../requirements-analysis/requirements.md`。

## サービス定義

### CsvParserService

**責務**: アップロードされたCSVファイルの生バイト列を、構造検証済みの行データに変換する(FR-A2, FR-A5)。
**ライフサイクル/スケール特性**: ステートレス。リクエストごとに生成・破棄されるサーバーサイド関数(Honoのリクエストハンドラから呼び出される)。永続状態を持たない。

### CsvValidationService

**責務**: 21列(予備項目を除く全22列中)の値検証をcollect-all方式で実行する(FR-B1〜B3, B5)。
**ライフサイクル/スケール特性**: ステートレス。ColumnMappingTable/MasterDataSchemaという共有定義を参照するのみで、DBやファイルシステムへの依存はない。

### MasterDataRepository

**責務**: 単位数表マスタデータの永続化・照会を一元的に担う唯一のデータアクセス層(FR-C1〜C2, FR-D1〜D3)。
**ライフサイクル/スケール特性**: Prisma Client経由でAurora PostgreSQLに接続する。コネクションプールはアプリケーションプロセスのライフサイクルに紐づく。

## オーケストレーションパターン

**オーケストレーション方式(choreography ではなく orchestration)**: CsvImportRouteが中央のオーケストレーターとして、CsvParserService→CsvValidationService→MasterDataRepositoryを順に同期呼び出しする(Q5「すべて同期」)。各サービスは互いを直接呼び出さず、呼び出し順序と分岐(構造エラー時は早期リターン、検証エラー時はDB保存をスキップ)はすべてCsvImportRouteが制御する。

表示機能領域も同様に、SearchRoute/FilterOptionsRouteがMasterDataRepositoryを直接呼び出すシンプルな1段オーケストレーションである。

## サービス通信契約

| 呼び出し元 | 呼び出し先 | 契約 |
|---|---|---|
| CsvImportRoute | CsvParserService | `parse(fileBuffer): ParseResult`(判別可能Union、例外を投げない) |
| CsvImportRoute | CsvValidationService | `validate(rows): ValidationResult`(判別可能Union、例外を投げない) |
| CsvImportRoute | MasterDataRepository | `upsertMany(records): Promise<{count}>`(Prisma例外はそのまま伝播) |
| SearchRoute | MasterDataRepository | `findByFilter(insurerNumber, serviceTypeCode): Promise<MasterDataRecord[]>` |
| FilterOptionsRoute | MasterDataRepository | `findDistinctInsurerNumbers()`, `findDistinctServiceTypeCodes()` |

すべての契約は同期的なPromiseベースの関数呼び出しであり、プロセス間通信(HTTP/メッセージキュー等)は使用しない — フロントエンド(Next.js)とAPI層(Hono)の間のみHTTP(openapi-fetch経由)を用いる(Q2)。

## Assumptions & Open Questions

None.
