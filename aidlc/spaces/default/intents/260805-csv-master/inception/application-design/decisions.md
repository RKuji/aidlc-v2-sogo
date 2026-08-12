# Architecture Decision Records — 単位数表マスタ(CSV)の取り込み・表示

Sources: `application-design-questions.md`(Q1〜Q5)、`../requirements-analysis/requirements.md`、`../practices-discovery/team-practices.md`。

## ADR-1: 取込・表示を2つの独立した機能領域に分割する

**Context**: units-generationステージがSKIPされているため、コンポーネント境界をこのステージで直接決定する必要がある。要件(requirements.md)は取込(FR-A/B/C)と表示(FR-D)という性質の異なる2つの操作を定義している。

**Decision**: 取込機能領域と表示機能領域を、それぞれ独立したAPIルート・サービス・画面として設計する(Q1選択肢A)。両者はMasterDataRepositoryとColumnMappingTableのみを介して間接的に結合する。

- Option A — 2つの独立した機能領域: pros: 各領域が独立してテスト・変更可能、責務が明確。cons: 共有コンポーネント(MasterDataRepository等)の設計にやや注意が必要。Reversibility: 高い(後から統合するより、最初から分離しておく方が変更コストが低い)。
- Option B — 単一の機能領域: pros: 初期実装がやや単純。cons: 取込と表示の関心が混在し、Testing Postureで求められる高いテストカバレッジ(80%以上、6ツールのCIブロッキング)の達成が難しくなる。Reversibility: 低い(後から分離するのはリファクタリングコストが高い)。
- **Recommendation**: Option A。NFR-1(テストの全ツールCIブロッキング)を満たすには、独立してテスト可能な境界が重要。

**Consequences**: 取込・表示それぞれに独立したルート・サービスファイルが必要になるが、責務が明確になりテストが書きやすい。

**Alternatives Rejected**: Option B(単一機能領域)は、要件の異なる2つの操作(書き込み系と読み取り系)を1つのハンドラ/サービスに混在させることになり、3層アーキテクチャの「サービス層はビジネスロジックを持つ」という原則(team-practices.md Code Style)に反するため却下。

**Security/Compliance**: 取込(書き込み)と表示(読み取り)を機能領域として分離することで、将来的に書き込み系操作にのみ追加の認可制御を導入する場合の影響範囲を限定できる。現行スコープでは認証・認可要件自体は定義されていない(requirements.mdに記載なし)ため、これは将来の拡張に対する構造的な備えであり、本ステージで認可の具体的な実装を決定するものではない。

---

## ADR-2: フロントエンド-バックエンド間通信はopenapi-fetch経由とする

**Context**: 技術スタックはOpenAPI契約とopenapi-fetchクライアントを含む(intent-statement.md)。Next.jsからHono APIを呼び出す方式を確定する必要がある。

**Decision**: Next.jsのフロントエンドから、openapi-fetch経由でHono APIを直接呼び出す(Q2選択肢A)。

- Option A — openapi-fetch経由の直接呼び出し: pros: OpenAPI契約から生成された型定義により型安全性が保証される。技術スタックで明示的に選定されているツール。cons: なし(技術スタックの選定と整合)。Reversibility: 中程度。
- Option B — Next.js API Routesでラップ: pros: フロントエンドと同一オリジンでの呼び出しになる。cons: 技術スタックで明示的に選定されたopenapi-fetchの型安全性の恩恵を活かせない、不要な中間層が増える。Reversibility: 中程度。
- **Recommendation**: Option A。技術スタックの選定(Hono/OpenAPI/openapi-fetch)を素直に活かす。

**Consequences**: Hono API側でOpenAPI仕様を定義し、フロントエンドがそれを型として利用する。CORS設定が必要になる場合はHono側で許可する。

**Alternatives Rejected**: Option B(Next.js API Routes経由)は技術スタックで明示的に選定されたopenapi-fetchの型安全性を活かせず、不要な中間層となるため却下。

**Security/Compliance**: openapi-fetch経由のAPI呼び出しは同一アプリケーション内の通信であり、外部公開APIではない。認証・認可は現行スコープの要件に含まれていない(requirements.mdのOut of Scope・Assumptionsに明記なし、次のnfr-requirementsステージがSKIPのため、追加のセキュリティ要件は本ステージのスコープ外)。

---

## ADR-3: CSV検証ロジックは専用のサービス層コンポーネントに配置する

**Context**: 21列の検証をcollect-all方式で行う必要があり(project.md Mandated)、このロジックをどこに置くかを決定する必要がある。

**Decision**: サービス層に専用のCsvValidationServiceを置き、Zodスキーマ(列単位の形式検証)とサービス層のロジック(collect-all集約)を組み合わせる(Q3選択肢A)。

- Option A — 専用バリデーションサービス: pros: Honoのルートハンドラがトランスポート関心事のみに専念できる(team-practices.md Code Style、3層アーキテクチャの原則)、ユニットテストが書きやすい。cons: なし。Reversibility: 高い。
- Option B — ハンドラ内に直接実装: pros: ファイル数が少なくて済む。cons: 3層アーキテクチャの原則(Hono API層はトランスポート関心事のみ)に反する、テストがHTTPレイヤーを経由する必要が生じる。Reversibility: 低い(後から分離するのはリファクタリングコストが高い)。
- **Recommendation**: Option A。team-practices.mdで既に3層アーキテクチャが決定事項として確認されている。

**Consequences**: CsvValidationServiceは単体でテスト可能になり、NFR-1(全テストツールのCIブロッキング、80%カバレッジ)を満たしやすくなる。

**Alternatives Rejected**: Option B(ハンドラ内直接実装)はteam-practices.mdで既に確認済みの3層アーキテクチャの原則(Hono API層はトランスポート関心事のみ)に反するため却下。

**Security/Compliance**: 検証ロジックを独立したサービスとして分離することで、21列すべての検証が確実に実行されることをユニットテストで機械的に保証できる(NFR-1)。これは、規制対象の介護保険マスタデータ(`intent-statement.md`)の取込において、検証漏れによる不正データ混入を防ぐという直接的なコンプライアンス上の意義を持つ。ハンドラ内実装ではHTTPレイヤーを経由しないと検証ロジックをテストできず、この保証の機械的な検証が難しくなる。

---

## ADR-4: 単位数表マスタは単一モデルとしupsertで管理する

**Context**: 重複レコード(同一保険者番号+サービス種類コード+サービス項目コード+適用開始年月日)を検出した場合、上書きする方針が既にrequirements-analysisで確定している(FR-C2)。

**Decision**: 単位数表マスタを表す単一のPrismaモデルとし、上記4項目を一意キーとしてupsertする(Q4選択肢A)。

- Option A — 単一モデル+upsert: pros: FR-C2の決定(上書き、履歴保持なし)と直接整合する、スキーマがシンプル。cons: なし(要件が既に履歴保持を対象外としている)。Reversibility: 中程度(後から履歴テーブルを追加することは可能)。
- Option B — 取込履歴用の別テーブルも用意: pros: 将来的な監査要件に対応しやすい。cons: requirements.mdのOut of Scope(重複レコードのバージョン管理・履歴保持は対象外)と矛盾する、過剰設計(YAGNI)。Reversibility: 低い(不要なテーブルを設計に含めると変更コストが増す)。
- **Recommendation**: Option A。要件で明示的に対象外とされた機能(履歴保持)のための設計を先取りしない。

**Consequences**: スキーマは単位数表マスタの1テーブルのみで完結する。将来、証記載保険者番号マスタ(scope-document.mdに将来の拡張候補として記録済み)を追加する場合は、別途外部キー関係を設計する。

**Alternatives Rejected**: Option B(履歴テーブル)はrequirements.mdのOut of Scopeと直接矛盾するため却下。

**Security/Compliance**: 本設計は上書き(upsert)により旧データを破棄し、監査証跡(誰が・いつ・どの値から・どの値へ変更したか)を残さない。単位数表マスタは介護保険の給付・請求に関わる規制対象データ(`intent-statement.md`)であるため、本来であれば変更履歴の保持が監査上望ましい場合がある。しかし、この論点はrequirements-analysisステージのQ2で明示的に検討され(重複レコード検出時の挙動)、ユーザーが「上書き・履歴保持なし」を選択している(FR-C2)。したがって本ADRはその決定を実装可能な設計に落とし込むものであり、監査要件の再検討はこのステージのスコープではない。将来的に監査証跡が必要になった場合は、Out of Scopeとして記録された履歴テーブル(Option B)の追加を再検討する。

---

## ADR-5: コンポーネント間通信はすべて同期方式とする

**Context**: 想定データ量・性能目標は特定されておらず(NFR-2)、本システムは内部業務システムである。

**Decision**: すべてのコンポーネント間通信を同期的なリクエスト/レスポンスとする(Q5選択肢A)。非同期メッセージング・イベント駆動アーキテクチャは採用しない。

- Option A — すべて同期: pros: シンプルで実装・テストが容易、内部業務システムの規模(NFR-2で特定の性能目標なしと確認済み)に見合う。cons: 将来的に大量データ処理が必要になった場合は再設計が必要。Reversibility: 中程度。
- Option B — 一部非同期: pros: 将来の大量データ処理に備えられる。cons: 現時点で性能要件が存在しないため過剰設計、キュー基盤等の追加インフラが必要になり、infrastructure-designステージがSKIPされている本スコープでは導入する妥当性がない。Reversibility: 低い。
- **Recommendation**: Option A。YAGNI原則、および現行スコープでinfrastructure-design(SKIP)が対象外であることと整合する。

**Consequences**: 実装がシンプルになる。1ファイルのみのアップロード制限(FR-A1, Out of Scope)とも整合する。

**Alternatives Rejected**: Option B(非同期)は現行スコープの性能要件・インフラ設計ステージの状況と整合しないため却下。

**Security/Compliance**: 同期方式では、CSV取込のような時間のかかりうる処理も含めてリクエスト元(ユーザー)が処理結果を直接受け取るため、処理の成否が非同期処理のように追跡不能になることがなく、FR-A6(成功時のフィードバック)・FR-B4(失敗時のフィードバック)の即時性・確実性を担保しやすい。追加のキュー基盤等を導入しないため、新たな攻撃対象領域(メッセージブローカーの認証・可用性等)も発生しない。

## Assumptions & Open Questions

None.

## Review

**Verdict: READY**

**レビュアー**: aidlc-architecture-reviewer-agent(イテレーション3、最終確認レビュー)
**日時**: 2026-08-10T08:16:33Z

### イテレーション2指摘事項の解消確認

- **[Major解消] カスケード非採用の根拠のADR誤引用**: `component-methods.md` 79行目は、もはや「ADR-5のYAGNI方針と整合」という誤ったADR引用に依拠していない。現在は「requirements.mdはFR-D1/D2を互いに独立した選択操作として定義しており、一方の選択肢が他方に依存する仕様は要件化されていないため」と、要件を直接の論拠として明示しており、`decisions.md`にADR-5への言及は残存していない(`grep -n "ADR-5" component-methods.md`はヒットなし)。カスケード非採用の設計判断が要件ベースで自己完結して説明されており、記述として一貫性がある。**解消済み**。
- **[Minor解消] ADR-3・ADR-4のSecurity/Complianceセクションの出典誤り**: いずれも`project.md DECIDED`ではなく`intent-statement.md`を正しく引用するよう修正されている(ADR-3: 55行目「規制対象の介護保険マスタデータ(`intent-statement.md`)」、ADR-4: 73行目「介護保険の給付・請求に関わる規制対象データ(`intent-statement.md`)」)。**解消済み**。
- **[Minor解消] `services.md`と`component-methods.md`の`upsertMany`戻り値型の不一致**: `component-methods.md` 51行目は`Promise<{ count: number }>`に修正され、`services.md` 34行目の契約表`Promise<{count}>`と完全に一致する。ファイル横断で`uniqueKeyCount`を検索したところ、本レビューセクション自身に残る過去の指摘記録テキスト以外に該当箇所はなく、仕様本文(component-methods.md、services.md、decisions.md本文)には一切残存していないことを確認した。**解消済み**。
- **[Minor解消] `uniqueKeyCount`フィールドの用途未定義**: フィールド自体が削除されたため、用途未定義という指摘は前提ごと解消している。FR-A6の`importedRowCount`は`upsertMany`の戻り値`count`をそのまま用いる旨が15行目・57行目で明記されており、単一の値のみが一貫して使われている。**解消済み**。

### 新規指摘

なし。今回確認した3ファイル(component-methods.md、decisions.md、services.md)間で、シグネチャ・出典・設計根拠の新たな不整合は検出されなかった。

### 総評

イテレーション2で指摘した1件のMajorと3件のMinorはすべて解消されており、修正過程で新たな不整合も導入されていない。Application Designステージの成果物はREADYと判定する。

