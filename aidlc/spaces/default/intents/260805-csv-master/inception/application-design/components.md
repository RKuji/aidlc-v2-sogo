# Components — 単位数表マスタ(CSV)の取り込み・表示

Sources: `../requirements-analysis/requirements.md` (FR-A〜E, NFR-1〜6)、`../practices-discovery/team-practices.md`(3層アーキテクチャ)、`application-design-questions.md`(Q1〜Q5)。units-generationステージはSKIPのため、コンポーネント境界はこのステージで直接確定する。

2つの独立した機能領域(取込・表示)(Q1)で構成し、共通の3層(Hono API層→サービス層→Prismaデータ層)を貫通する。

## 取込機能領域

| コンポーネント | 層 | 責務 |
|---|---|---|
| **ImportUploadComponent** | フロントエンド(Next.js) | CSVファイル選択・アップロードUI。成功メッセージ/行数(FR-A6)、失敗時の全違反リスト(FR-B4/B5)を表示する |
| **CsvImportRoute** | API層(Hono) | `POST /api/csv-import`。マルチパートでアップロードされたファイルを受け取り、CsvParserService→CsvValidationService→MasterDataRepositoryの順にオーケストレーションする |
| **CsvParserService** | サービス層 | Shift-JISデコード(FR-A2)、行/列への分割、構造検証(列数22列・空ファイル・デコード失敗をFR-B2の値検証より前にチェック、FR-A5) |
| **CsvValidationService** | サービス層 | 21列(予備項目を除く)の値検証をcollect-all方式で実行し(FR-B1〜B2)、違反があれば行番号・列名・ルール・実際の値を含む違反リストを返す(FR-B5)。1件でも違反があれば取り込み不可と判定する(FR-B3) |
| **MasterDataRepository** | データ層(Prisma) | 検証済み行の保存(upsert、FR-C1〜C2)、および表示機能領域からの参照クエリを提供する共有コンポーネント |

## 表示機能領域

| コンポーネント | 層 | 責務 |
|---|---|---|
| **SearchFilterComponent** | フロントエンド(Next.js、コンテナ) | 取り込み済みの保険者番号・サービス種類コードを選択させる独立した2つのドロップダウンUI(FR-D1〜D2、カスケード絞り込みなし)。両条件が確定するとSearchRouteを呼び出してデータを取得し、SearchResultsComponentへpropsとして渡す — 表示機能領域内でHTTP呼び出しを行う唯一のコンポーネント |
| **SearchResultsComponent** | フロントエンド(Next.js、表示専用) | `records`をpropsとして受け取り、主要列を表示し、行クリックで詳細列を展開するテーブルUI(FR-D4)。自らAPIを呼び出さない |
| **FilterOptionsRoute** | API層(Hono) | `GET /api/filter-options`。登録済みデータから重複排除した保険者番号・サービス種類コードの一覧を返す |
| **SearchRoute** | API層(Hono) | `GET /api/master-data`。保険者番号・サービス種類コードのクエリパラメータを受け取り、一致する登録済みデータを返す(FR-D3) |
| **MasterDataRepository** | データ層(Prisma) | (取込機能領域と共有)フィルタ選択肢の重複排除クエリ、条件一致検索クエリを提供する |

## 共有コンポーネント

| コンポーネント | 層 | 責務 |
|---|---|---|
| **ColumnMappingTable** | 全層で参照される共有モジュール | CSVの日本語/半角カタカナ列名と、コード内で使うcamelCase識別子の対応表(FR-E1)。TypeScriptの型・Zodスキーマ・Prismaモデルの3層すべてがこの単一の定義を参照する |
| **MasterDataSchema(Zod)** | サービス層/API層で共有 | 各列の形式検証ルール(桁数・数値範囲・コード値等、FR-B2の表)をZodスキーマとして表現し、CsvValidationServiceが参照する |

## コンポーネント境界の原則

- **ColumnMappingTableは単一の情報源**であり、CsvValidationService・MasterDataSchema・MasterDataRepository(Prismaモデル)はすべてこれを参照する(独自に再導出しない)(`team-practices.md` Code Style, project.md Mandated)。
- **MasterDataRepositoryは取込・表示の両機能領域から共有される唯一のデータアクセスコンポーネント**であり、Prismaへの直接アクセスは他のコンポーネントから行わない(3層アーキテクチャの原則)。
- 通信パターンはすべて同期的なリクエスト/レスポンスである(Q5)。非同期メッセージング・イベント駆動の対象コンポーネントは存在しない。

## Assumptions & Open Questions

None.
