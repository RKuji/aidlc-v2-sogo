# Quality Gates — csv-import-display

Source: `ci-config.md`、`../build-and-test/build-test-results.md`、`team-practices.md`(Testing Posture)。

## マージ前必須ゲート(ブロッキング)

| ゲート | 基準 | 根拠 |
|---|---|---|
| 型チェック | `tsc --noEmit`がエラー0件 | construction phase guardrail(unresolved import errors等の禁止) |
| リント | `eslint`がエラー0件 | team-practices.md Code Style |
| ユニットテスト | 全テストパス、ライン カバレッジ80%以上 | NFR-1、org.md デフォルト |
| 統合テスト | Testcontainers統合テストがすべてパス | team-practices.md Testing Posture(Q3で確認済みの6ツールの一つ) |
| E2Eテスト | Playwrightテストがすべてパス | team-practices.md Testing Posture |
| 依存関係スキャン | `npm audit --audit-level=high`で高深刻度の脆弱性0件 | project.md Mandated(Q7) |
| シークレットスキャン | gitleaksでシークレット検出0件 | project.md Mandated(Q7) |

## 一時的に非ブロッキングなゲート

| ゲート | 状態 | 理由 |
|---|---|---|
| Storybookビルド | 非ブロッキング(`continue-on-error`) | 既知の互換性問題(project.md DECIDED)。解決後にブロッキングへ復帰 |

## 未有効化のゲート

| ゲート | 状態 | 理由 |
|---|---|---|
| MagicPod受入テスト | 無効化(`if: false`) | MagicPodの組織/プロジェクト/APIトークンが未設定。設定完了後に有効化が必要 |

## SAST/DAST

project.md Forbiddenにより本プロジェクトのスコープでは明示的に対象外(内部業務システムのため追加のパイプライン投資は不要と判断済み、Q7)。

## Assumptions & Open Questions

- MagicPodの受入テストは、実際の組織・プロジェクト・APIトークンの設定が完了するまで本パイプラインでは実行できない。運用フェーズでの対応が必要 [assumption]
