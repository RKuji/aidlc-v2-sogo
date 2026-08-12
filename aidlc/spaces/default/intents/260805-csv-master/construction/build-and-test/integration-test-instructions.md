# Integration Test Instructions — csv-import-display

Source: `../csv-import-display/code-generation/code-summary.md`。テスト戦略: Standard — 主要な境界に統合テスト。

## テストフレームワーク設定

Vitest(`vitest.integration.config.mts`)+ Testcontainers(`postgres:16-alpine`)。

## 実行方法

```bash
npx vitest run --config vitest.integration.config.mts
```

**前提条件**: Dockerデーモンへのアクセスが必要(Testcontainersがローカルコンテナを起動する)。

## カバレッジ目標

`master-data-repository.ts`のupsert/クエリロジックは、ユニットテストではなく本統合テストで検証する方針(ユニットテスト単体でのカバレッジは意図的に低い、`code-summary.md` Review参照)。

## テスト対象境界

- `test/integration/master-data-repository.integration.test.ts` — `MasterDataRepository`の実際のPostgreSQLコンテナに対する動作:
  - 新規レコードのupsert(insert)
  - 既存レコードと同一複合キー(保険者番号+サービス種類コード+サービス項目コード+適用開始年月日)での上書き(FR-C2)
  - `findDistinctInsurerNumbers`/`findDistinctServiceTypeCodes`の重複排除クエリ
  - `findByFilter`の条件一致クエリ

## テストデータ管理

各テストで独立したPostgreSQLコンテナを起動・破棄(`beforeAll`/`afterAll`)。テストデータはテストファイル内で都度生成。

## Assumptions & Open Questions

None.
