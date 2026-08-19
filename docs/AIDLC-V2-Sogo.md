# 総合事業マスター取り込み画面

## Initialization

### 最初のインプット
![alt text](image.png)

### どれぐらいちゃんとステージを踏むか
アダプティブコンポーザー
どのステージを実行し、どのステージを省略するべきかを選別する仕組み
![alt text](image-1.png)

翻訳
![alt text](image-2.png)

インプットした仕様は薄い
最初期の実装では検証の複雑度が高くなるらしい。
![alt text](image-3.png)
昨日話題に上がったスキップ戦略と概ね一致している気がする
![alt text](image-4.png)
Operation は丸々スキップ
![alt text](image-5.png)

## Ideation

### Intent Capture & Framing
#### どんなサービスを作りたいか
![alt text](image-6.png)
#### ビジネス・フロー上での要件
![alt text](image-7.png)
#### 自由記述をしたことで、再質問が発生
![alt text](image-8.png)
![alt text](image-9.png)

#### 追加学習
意味わからん質問された
> セマンティクスごとの見直し：回答されていない選択肢の文言を事実として言及させないよう、アーティファクト作成後に自己チェックする

などの意味ではないかとCopilot
![alt text](image-10.png)
なんかAIとWorkflowが戦ってくれていたよう
![alt text](image-11.png)

#### 完了
![alt text](image-12.png)

#### Intent Capture & Framing 最終成果物
![alt text](image-19.png)

### Scope Definition & Prioritization
#### 実装順
![alt text](image-13.png)
Q1～4の検証結果をQ5の回に混ぜてくるのは地味に偉い
![alt text](image-14.png)
:::note info
**リネージング**
データの流れや依存関係を追跡・可視化するプロセス
生成 ⇒ 変換 ⇒ 移動 ⇒ 利用
:::
#### 追加学習
![alt text](image-15.png)
#### 完了
一部修正のフィードバッワを追加した。
![alt text](image-16.png)
![alt text](image-17.png)
![alt text](image-18.png)

#### Scope Definition & Prioritization 最終成果物
![alt text](image-20.png)

### Approval & Handoff
#### ビジネスとしての障壁の確認
![alt text](image-21.png)
#### 追加学習
無し
![alt text](image-22.png)
#### 完了
![alt text](image-23.png)

### Ideation 最終成果物
Phase全体のサマリー
![alt text](image-24.png)

## Inception

### Practices Discovery
#### 開発方式
内部設計やセキュリティ、Git使用などの方針
![alt text](image-25.png)
![alt text](image-26.png)
:::note info
**Bolt**
構築の実行単位
:::
:::note info
**ウォーキングスケルトン**
始めにモジュールの結合を全て通す手法。
モジュールごとに作り、最後に接続するとした場合、最後まで接続部の不具合が発見されない場合があり、それを防ぐ。
:::
:::note info
**dependency scanning**
依存するパッケージに、既知のセキュリティ脆弱性が無いかを確認する。
:::
:::note info
**secret scanning**
シークレットキーなどが含まれていないかを確認する。
:::
:::note info
**SAST**
ソースコードやバイナリを解析して、脆弱性が無いかを確認する。
**Static** Application Security Testing
:::
:::note info
**DAST**
実際に稼働しているアプリケーションに対し、疑似的な攻撃を行う。
**Dynamic** Application Security Testing
:::
#### 追加学習
なし
![alt text](image-27.png)

#### Practices Discovery 最終成果物
![alt text](image-28.png)
![alt text](image-29.png)

### Requirements Analysis
#### 外部設計
![alt text](image-30.png)
![alt text](image-31.png)
![alt text](image-32.png)
#### 追加学習
![alt text](image-33.png)
「実際に数え直して検証する」とは何かを質問
今後「個数を数え直す」ということはしそうだが、全てを包括するソリューションは提示されなかった
![alt text](image-34.png)
確かに Scope Definition & Prioritization の最終成果物に20行として記載されていた。
![alt text](image-20.png)
訂正が行われた
![alt text](image-35.png)

#### Requirements Analysis 最終成果物
Review 以降のセクションが英語だったため、日本語化させた。
追記させたセクションは英語のままになる傾向がある。= 共通の指示が反映されないことがありそう。
![alt text](image-36.png)
![alt text](image-37.png)
![alt text](image-38.png)


### Application Design
#### 詳細設計
![alt text](image-39.png)
![alt text](image-40.png)

#### 追加学習
「学びがあります」とだけ出力され、内容が確認できないのが不便。
![alt text](image-41.png)

#### Application Design 最終成果物
![alt text](image-42.png)
その他、詳細設計書
- components
- component-methods
- services
- component-dependency

## Construction

### Code Generation
#### 実装管理方法
ウォーキングスケルトンでの実装を前提としている。
![alt text](image-43.png)
aidlc を git 管理しているため、干渉するという警告。
新しくリポジトリを作成し、その中で実装するように指示した。
![alt text](image-44.png)

#### 実装プラン
![alt text](image-45.png)
![alt text](image-46.png)

#### 実装
Storybook でビルドエラー
![alt text](image-47.png)
Q. 詳しく
![alt text](image-48.png)
Q. すぐに解決できる問題か
![alt text](image-49.png)
とりあえずいいことにした
![alt text](image-50.png)

#### 完了
![alt text](image-54.png)

#### Code Generation 最終成果物
![alt text](image-51.png)
![alt text](image-52.png)
![alt text](image-53.png)

:::note info
**ソースコードの配置について**
![alt text](image-55.png)
:::

### Build and Test

#### 追加学習
2件
![alt text](image-56.png)
![alt text](image-57.png)

#### 完了
![alt text](image-59.png)

#### Build and Test 最終成果物
![alt text](image-58.png)

### CI Pipeline
#### 恒常的なテストの失敗の扱い
![alt text](image-60.png)

#### CIの方法
![alt text](image-61.png)
![alt text](image-62.png)

#### 完了
![alt text](image-63.png)

#### Build and Test 最終成果物
![alt text](image-64.png)
![alt text](image-65.png)

## ワークフロー完了
![alt text](image-66.png)

### 要約資料
![alt text](image-70.png)
![alt text](image-67.png)
![alt text](image-68.png)
![alt text](image-69.png)