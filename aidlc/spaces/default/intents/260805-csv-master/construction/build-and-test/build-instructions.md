# Build Instructions — csv-import-display

Source: `../csv-import-display/code-generation/{code-generation-plan,code-summary}.md`。対象リポジトリ: `aidlc-v2-sogo-package/`(sibling repo)。

## 依存関係インストール

```bash
cd aidlc-v2-sogo-package
npm install
```

## 環境セットアップ

- `.env.example`をコピーして`.env`を作成し、`DATABASE_URL`をローカルのPostgreSQL接続文字列に設定する。
- Prismaクライアントを生成: `npm run prisma:generate`
- 開発用DBにマイグレーションを適用: `npm run prisma:migrate`

## ビルドコマンド

```bash
npx tsc --noEmit          # 型チェック
npx eslint .              # リント
npm run build             # Next.js本番ビルド(型チェック+リント+静的生成を含む)
```

## ビルド検証手順

1. `npx tsc --noEmit`がエラー0件で終了することを確認する。
2. `npx eslint .`がエラー0件で終了することを確認する(exit code 0)。
3. `npm run build`が6ルートすべてを正常に生成することを確認する。

## トラブルシューティング

- **Prisma Client未生成エラー**: `npm run prisma:generate`を実行する。
- **DATABASE_URL未設定エラー**: `.env`が存在し、`DATABASE_URL`が設定されているか確認する。
- **Storybookビルド失敗(`SB_BUILDER-WEBPACK5_0002`)**: 既知のアップストリーム互換性問題(project.md DECIDED参照)。本ステージのビルド検証対象外。

## Assumptions & Open Questions

None.
