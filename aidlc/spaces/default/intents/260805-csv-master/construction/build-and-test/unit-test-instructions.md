# Unit Test Instructions — csv-import-display

Source: `../csv-import-display/code-generation/code-summary.md`。テスト戦略: Standard(`aidlc-state.md`)。

## テストフレームワーク設定

Vitest(`vitest.config.mts`)。jsdom環境、MSWによるHTTPモック(`test/msw/`)。

## 実行方法

```bash
npx vitest run                 # 全ユニットテスト実行
npx vitest run --coverage      # カバレッジ付き実行
npx vitest                     # watchモード
```

## カバレッジ目標

全体ライン カバレッジ80%以上(org.md デフォルト、team-practices.md Testing Postureで変更なし)。

## テスト対象コンポーネント(9ファイル、43テスト)

- `test/unit/shared/column-mapping.test.ts` — ColumnMappingTableの整合性
- `test/unit/server/services/csv-parser-service.test.ts` — Shift-JISデコード、構造検証(FR-A5)
- `test/unit/server/services/csv-validation-service.test.ts` — 21列のcollect-all検証(正常値・境界値・異常値、単一行複数違反、複数行にわたる違反)
- `test/unit/server/routes/{csv-import-route,filter-options-route,search-route}.test.ts` — 各Honoルートの成功/失敗レスポンス
- `test/unit/components/{ImportUploadComponent,SearchFilterComponent,SearchResultsComponent}.test.tsx` — フロントエンドコンポーネント

## テストデータ管理

各テストファイル内でインラインのフィクスチャデータを使用(外部データファイルへの依存なし)。DB呼び出しはVitestの`vi.mock`でモック化。

## Assumptions & Open Questions

None.
