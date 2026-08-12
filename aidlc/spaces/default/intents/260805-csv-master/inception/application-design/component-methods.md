# Component Methods — 単位数表マスタ(CSV)の取り込み・表示

Sources: `components.md`, `../requirements-analysis/requirements.md`(FR-A〜E)。詳細なビジネスルールはFunctional Design相当だが、本スコープではfunctional-designステージがSKIPのため、実装に必要な最小限のメソッドシグネチャと入出力型・エラー方針をここで確定する。

## CsvImportRoute(API層)

```
POST /api/csv-import
  Request: multipart/form-data { file: File }
  Response 200: { success: true, importedRowCount: number }        // FR-A6
  Response 422: { success: false, violations: ValidationViolation[] } // FR-B4/B5
  Response 400: { success: false, structuralError: string }         // FR-A5(構造エラー)
```

エラー処理: 構造エラー(FR-A5)は列単位検証より前段でチェックし400を返す。列単位の検証違反(FR-B2)は422で全違反リストを返す(collect-all、FR-B1)。予期しない例外(DB接続断等)は500として上位にログを残す(致命的エラーとして扱う)。`importedRowCount`はMasterDataRepository.upsertMany()の戻り値`count`(=検証合格後の入力行数、ヘッダーを除く生データ行数と一致)を用いる。

## CsvParserService(サービス層)

```
parse(fileBuffer: Buffer): ParseResult

type ParseResult =
  | { ok: true; rows: RawCsvRow[] }
  | { ok: false; structuralError: "COLUMN_COUNT_MISMATCH" | "EMPTY_FILE" | "ENCODING_ERROR" }
```

入力: アップロードされたファイルの生バイト列。出力: Shift-JISデコード後、22列に分割された行の配列、または構造エラー種別(FR-A5の3ケースに対応)。エラー処理: 例外を投げず、判別可能なUnion型で結果を返す(collect-allの精神に合わせ、呼び出し側が分岐しやすい形にする)。

## CsvValidationService(サービス層)

```
validate(rows: RawCsvRow[]): ValidationResult

type ValidationResult =
  | { valid: true; records: MasterDataRecord[] }
  | { valid: false; violations: ValidationViolation[] }

type ValidationViolation = {
  rowNumber: number;   // ヘッダーを1行目とする物理行番号(FR-B5)
  columnName: string;
  rule: string;
  actualValue: string;
}
```

入力: CsvParserServiceが返した行配列。出力: 全21列(予備項目を除く)の検証をcollect-all方式で実行した結果。1件でも違反があれば`valid: false`とし、全違反を`violations`に含める(FR-B1〜B3、B5)。ColumnMappingTable/MasterDataSchemaを参照して列ごとの形式チェックを行う。

## MasterDataRepository(データ層)

```
upsertMany(records: MasterDataRecord[]): Promise<{ count: number }>
findDistinctInsurerNumbers(): Promise<string[]>
findDistinctServiceTypeCodes(): Promise<string[]>
findByFilter(insurerNumber: string, serviceTypeCode: string): Promise<MasterDataRecord[]>
```

入力/出力: `MasterDataRecord`はColumnMappingTableで定義されたcamelCaseフィールドを持つ型。`findDistinctInsurerNumbers`/`findDistinctServiceTypeCodes`はいずれも引数を取らない — FR-D1/D2は独立した2つのドロップダウン(カスケード絞り込みなし)と決定されているため、サービス種類コードの選択肢を保険者番号で絞り込む機能は設けない。`upsertMany`は保険者番号+サービス種類コード+サービス項目コード+適用開始年月日を一意キーとしてupsertする(FR-C2)。戻り値の`count`は実行したupsert呼び出し回数(=検証合格後の入力`records`の件数、ヘッダーを除く生データ行数と一致)を表し、FR-A6の「取り込まれた行数」はこの`count`をそのまま用いる。エラー処理: DB制約違反やコネクションエラーはPrismaの例外をそのまま上位(CsvImportRoute)に伝播させ、500として扱う(致命的エラー)。

## FilterOptionsRoute(API層)

```
GET /api/filter-options
  Response 200: { insurerNumbers: string[], serviceTypeCodes: string[] }
```

## SearchRoute(API層)

```
GET /api/master-data?insurerNumber={string}&serviceTypeCode={string}
  Response 200: { records: MasterDataRecord[] }
  Response 400: { error: "insurerNumber and serviceTypeCode are required" }
```

入力: クエリパラメータ(必須)。出力: 一致する登録済みレコードの配列(0件の場合は空配列、エラーではない)。エラー処理: パラメータ欠落は400。DBエラーは500。

## フロントエンドコンポーネント

- **ImportUploadComponent**: `onUpload(file: File): Promise<void>` — CsvImportRouteを呼び出し、成功/失敗に応じてFR-A6/FR-B4相当のメッセージを表示する。
- **SearchFilterComponent**(コンテナ): `onFilterSubmit(insurerNumber: string, serviceTypeCode: string): Promise<void>` — マウント時にFilterOptionsRouteを呼び出しドロップダウンの選択肢を表示する(FR-D1/D2は独立した2つのドロップダウンとし、カスケード絞り込みは行わない — requirements.mdはFR-D1/D2を互いに独立した選択操作として定義しており、一方の選択肢が他方に依存する仕様は要件化されていないため。カスケード実装は追加のAPI設計(絞り込みキーの受け渡し)を要するが、それを正当化する要件上の根拠がない)。ユーザーが両方の条件を選択し確定した時点(両方が選択済みになったタイミングで自動発火、明示的な「検索」ボタンは設けない)で**本コンポーネントがSearchRouteを呼び出し**、取得した`records`を親経由でSearchResultsComponentへpropsとして渡す。データ取得責務はSearchFilterComponentが一元的に持ち、SearchResultsComponentはHTTP呼び出しを行わない。
- **SearchResultsComponent**(表示専用): `records: MasterDataRecord[]` をpropsとして受け取るのみで、自らAPIを呼び出さない。主要列を表示し、行クリックで詳細列を展開する(FR-D4)。

## Assumptions & Open Questions

None.
