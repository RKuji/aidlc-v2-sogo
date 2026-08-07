# 機能
介護予防・日常生活支援総合事業費の単位数表マスタ（CSV）の取り込み・表示

# 既存実装
無し

# 詳細
## 取り込み
ローカルにあるCSVファイルを取り込み、DBに保存する。
Validation は、次の項で記載するCSV定義に従う。
## 表示
取り込み済みの保険者番号と、ｻｰﾋﾞｽ種類ｺｰﾄﾞをユーザーに選択させ、その情報に一致する保存済みデータを表形式で表示する。

# CSV定義
|列名|定義|
|---|---|
|証記載保険者番号|6桁|
|ｻｰﾋﾞｽ種類ｺｰﾄﾞ|2桁|
|ｻｰﾋﾞｽ項目ｺｰﾄﾞ|4桁の指定ｺｰﾄﾞ|
|適用開始年月日|6桁YYYMMDD|
|適用終了年月日|6桁YYYMMDD|
|ｻｰﾋﾞｽ名称|なんでも|
|単位数|-9999～99999|
|算定単位|01:１回につき 02:１日につき 03:１月につき 05:１週間につき|
|制限日数・回数|0～99|
|算定回数制限期間|01:１月につき 08:１日につき 16:１週間につき 00:未制限|
|支給限度額対象区分|3:区分支給限度額管理の対象 0:区分支給限度額管理の対象外|
|予備項目||
|給付率|１～１００|
|利用者負担額|5桁まで|
|事業対象者実施区分|1　or　2|
|要支援１受給者実施区分|1　or　2|
|要支援２受給者実施区分|1　or　2|
|要介護１受給者実施区分|1　or　2|
|要介護２受給者実施区分|1　or　2|
|要介護３受給者実施区分|1　or　2|
|要介護４受給者実施区分|1　or　2|
|要介護５受給者実施区分|1　or　2|


# 技術スタック
## FRrontend
### Application
|Category|Technology|
|---|---|
|Framework|Next.js|
|UI Library|React|
|Language|TypeScript|
## UI System
### Component Foundation
|Category|Technology|
|---|---|
|Headless UI|Radix UI|
### Styling
|Category|Technology|
|---|---|
|CSS Framework|Tailwind CSS|
|Variant Management|class-variance-authority (CVA)|
|Utility|clsx|
|Utility|tailwind-merge|
## State Management
### State
|Category|Technology|
|---|---|
|Server State|SWR|
|Client State|Zustand|
|URL State|nuqs|
### Form
|Category|Technology|
|---|---|
|Form Management|React Hook Form|
|Validation|Zod|
## API Layer
### API
|Category|Technology|
|---|---|
|Framework|Hono|
|API Contract|OpenAPI|
|API Client|openapi-fetch|
## Data Layer
### Database
|Category|Technology|
|---|---|
|Database|Aurora PostgreSQL|
### ORM
|Category|Technology|
|---|---|
|ORM|Prisma|
## Quality Layer
### Testing
|Category|Technology|
|---|---|
|Unit Test|Vitest|
|Integration Test|Testcontainers|
|UI State Test|Storybook|
|API Mock|MSW|
|E2E Test|Playwright|
|Acceptance Test|MagicPod|
