# Application Design — Questions

units-generationステージはSKIPのため、コンポーネント境界はこのステージで直接確定します。以下はrequirements.md(FR-A〜E)とteam-practices.md(3層アーキテクチャ)を前提とした設計上の確認事項です。

## Q1. コンポーネント/機能境界の粒度は?

- A. 2つの機能領域(取込: 検証+保存、表示: 検索+一覧)を、それぞれ独立したAPIルート・サービス・画面として設計する
- B. 単一の機能領域として一体で設計する
- C. Not yet defined
- X. Other (please specify)

[Answer]:

## Q2. フロントエンドとバックエンドの通信方式は?

- A. Next.jsのフロントエンド(ページ/コンポーネント)から、openapi-fetch経由でHono APIを呼び出す(OpenAPI契約に基づく型安全な通信)
- B. Next.jsのAPI Routesを経由してHonoをラップする
- C. Not yet defined
- X. Other (please specify)

[Answer]:

## Q3. CSV検証ロジックの配置場所は?

- A. サービス層に21列すべての検証ルールを実装する専用のバリデーションサービス(例: CsvValidationService)を置き、Zodスキーマは個々の列の形式検証に、サービス層のロジックはcollect-all集約に用いる
- B. Honoのリクエストハンドラ内に直接検証ロジックを書く
- C. Not yet defined
- X. Other (please specify)

[Answer]:

## Q4. データ保存の構造は?

- A. 単位数表マスタを表す単一のPrismaモデル(テーブル)とし、保険者番号+サービス種類コード+サービス項目コード+適用開始年月日を一意キーとして上書き(upsert)する
- B. 取込履歴用の別テーブルも用意する
- C. Not yet defined
- X. Other (please specify)

[Answer]:

## Q5. サービス間・コンポーネント間の通信パターンは?

- A. すべて同期的なリクエスト/レスポンス(非同期メッセージング・イベント駆動は不要な規模)
- B. 一部を非同期(イベント駆動、キュー等)で処理する
- C. Not yet defined
- X. Other (please specify)

[Answer]:

## Assumptions & Open Questions

None.
