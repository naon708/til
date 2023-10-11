python3 text_renderer_train_val_maker.py --help
usage: text_renderer_train_val_maker.py [-h] -t_labels TMP_LABELS

Run the TensorRT optimized object detecion model on an input video and save
BBoxed overlaid output as another video.

optional arguments:
  -h, --help            show this help message and exit
  -t_labels TMP_LABELS, --tmp_labels TMP_LABELS
                        image 一覧ファイル


---

## Dockerコンテナ内のvimでyankした際、クリップボードにコピーされなかった
- Vimにも種類があって、クリップボードと連携できるものとできないものがある

> - vim-tiny(デフォルトでインストールされている)
> - vim
> - vim-gtk ◎
> - vim-athena ◎
> - vim-gnome ◎
> - vim-nox
>
> このうちクリップボードを利用できるものは◎の付いているvim-gtk、vim-athena、vim-gnomeになります。
この中から好きなものをapt-getしましょう。

https://sy-base.com/myrobotics/vim/vim_use_clipboard/

---

## `.ttf`ファイル (TrueType Font)
- フォントファイル
  - テキストの表示方法(= 各文字がどのように見えるか)を定義するグラフィック情報を持つ(各文字の形、大きさ、太さなど)
  - フォントファイルの内容を視覚的に確認したい場合は、フォントビューアやフォントエディタを使用する(vimだとバイナリデータとして解釈されるため文字化けする)

---

## GASデプロイ時のアクセス権限設定に注意
- SlackのEvent Subscriptionsを有効にするためにエンドポイントの検証が必要だった
- Slack側から検証のためGASのエンドポイントにアクセスを試みるが、GASデプロイ時のアクセス権限を「自分のみ」に設定していたためVerifiedできず詰まった

```
curlでアクセス
↓
「現在、ファイルを開くことができません。</p><p>アドレスを確認して、もう一度試してください」みたいなのが返ってくる
↓
デプロイ時のアクセス権限設定を「全員」に変更して解決
```

---

## Deadlock
- マルチタスクやマルチスレッド・システムにおいて、リソースの排他制御の問題などにより、プログラムの動作が事実上停止してしまう状態のこと
- 確保済みのリソースを強制的に解放したり、プログラムを強制終了させたりする必要がある

> 1. あるプログラム1と2があり、それぞれが共有リソースAとBを利用する
> 2. プログラム1はリソースAを確保後、リソースBを確保する
> 3. プログラム2はリソースBを確保後、リソースAを確保するものとする
> 4. この2つのプログラムを同時に起動すると、まずプログラム1はリソースAを確保し、プログラム2はリソースBを確保する(この時点でリソースAもBもそれぞれのプログラムから確保済みである)
> 5. プログラム1がリソースBを確保しようとするが、すでにプログラム2によって確保済みなので、それが解放されるまで待たされることになる
> 6. プログラム2はリソースAを確保しようとするが、すでにプログラム1によって確保済みなので、やはり待たされることになる
> 7. 結果、両方のプログラムがお互いに解放されることのないリソースを待ち続ける状態になる
> 
> これがデッドロック状態である。

https://atmarkit.itmedia.co.jp/icd/root/30/71009330.html

---

## CQRS
wip

---

## `nvidia-smi`コマンド
```bash
$ nvidia-smi

# smi -> System Management Interface
```
- NvidiaのGPUの使用率、温度、メモリ使用量など、GPUの詳細情報を取得するコマンド
- コマンド実行して情報が返ってくれば、GPUにアクセスできているということ
- GPUマシン上に構築したコンテナ内で実行すれば、コンテナがホストのGPUに正しくアクセスできているか確認できる

learn more… https://qiita.com/miyamotok0105/items/1b34e548f96ef7d40370

---

## GPU
<img width="740" alt="スクリーンショット 2023-10-04 21 54 55" src="https://github.com/naon708/til/assets/77439261/325537cc-9078-4c62-ab68-d7afb410e7ab">

https://www.d3.ntt-east.co.jp/00101/

- もともとは画像や動画、そしてゲームや3Dグラフィックスのレンダリングを高速に行うためのハードウェア
- 並列処理能力が高いため、機械学習の計算処理、特に大量のデータやパラメータを扱うディープラーニングのモデル学習においても使われるようになった

---

## Nvidia
- GPUを中心とした製品を開発・販売している企業
- 一般消費者向けGPU、データセンター向けGPU、機械学習用の高性能GPUを提供
- ハードウェアだけでなく、CUDAというGPU上での並列処理を容易にするプログラミングプラットフォームやDeepLearningのライブラリなどのソフトウェアも提供している

---

## 【Docker】コンテナ作成時の`gpus`オプション
```bash
docker container create --gpus=all --name use_gpu_project [Image]
```
- NVIDIAのGPUを搭載したマシン上にDockerコンテナを作成する際、`gpus`オプションを指定しないとホストマシンのGPUにアクセスできない
- GPUにアクセスしたい場合は指定が必要
- どのGPUを使うか指定する。値の指定がなければ、利用可能なGPU全てを使う
- `all`は利用可能なすべてのGPUを使う

https://docs.docker.jp/engine/reference/commandline/run.html?highlight=it#nvidia-gpu

learn more… https://qiita.com/ttsubo/items/c97173e1f04db3cbaeda

---

## 【GitHub】自分が担当したPRで、マージされたものがどれくらいあるか確認
```
is:pr is:merged assignee:@me
```

---

## LinuxでOSのバージョン確認
```bash
lsb_release -a
```

---

## LinuxでCPUアーキテクチャ確認
```bash
lscpu
```

---

## Zod (※上辺だけで全然理解してない)
- TypeScriptの検証ライブラリ
- TSは開発時の型安全性は担保されるけど、JSにコンパイルされてプラウザで動く時にTSの型情報は存在しないため、外部入力やAPIレスポンスなどの型安全は担保されていない
- 背景: GraphQLの型システムとTypeScriptの型システムは異なるため型情報と実態の不整合が起きていた

```ts
// 雰囲気だけのコード
import { z } from 'zod'

export const DateScalarSchema = z.string().brand<'DateScalar'>()
export type DateScalar = z.infer<typeof DateScalarSchema>
```
### 導入後
- asによる型キャストの頻度が減少
- 開発時にデータのスキーマ違反を検知できるようになった
- バリデーションによって詳細な型を利用できるようになった
- らしい

---

## Docker環境でRailsのデバッグをしたい
```bash
# バックグラウンドで起動
% docker-compose up -d

# Railsが動いているコンテナのコンテナID or コンテナ名を調べる
% docker ps

# Attachする
% docker attach [コンテナのID or コンテナ名]

# これでOK
docker compose up -d && docker attach [Container Name]
```

`Ctr+C`で抜けるとコンテナが終了してしまうので`Ctrl+P` or `Ctrl+Q`でDetach

---

## JSDoc
```js
/**
* How to use
* @param {string} name - 挨拶したい相手の名前
* @return {string} - 「Hello, [名前]」で返る
* @example
*/
const greet = name => `Hello, ${name}!`
```
- JavaScriptのソースコードにアノテーションを追加するために使われるマークアップ言語
- 変数や関数の使い方をドキュメントとして残したいときに使う
- `/**  */`で囲う
- `@param`とか`@return`は Block Tags と呼ばれるもので、JSDocで定義されているものを使うっぽい

https://jsdoc.app/

---

## 【GraphQL】Apollo Clientを介してQueryを叩く
```ts
const { result, loading, error } = useCurrentCustomerQuery()
```
- `use**Query`: Apollo Client を通じて GraphQL Queryを実行するための Composition API 用の関数
  - `result`: Apollo ClientでFetchしたデータを含むオブジェクト(Object)
  - `loading`: データの読み込み状態(boolean)
    - falseなら読み込み完了
  - `error`: エラーが発生した際のエラーオブジェクト(Object)

https://v4.apollo.vuejs.org/guide-composable/query.html#query-status

---

## 【GraphQL】Fragments
```graphql
query GetUser {
  user(id: "7") {
    ...userFields
  }
}

fragment userFields on user {
  name
  age
}
```
- フラグメント(fragments)という機能を使用して、QueryまたはMutationの一部を再利用可能な単位として切り出すことができる
- 同じfieldのセットを1つのFragmentとして定義し、それを各Queryで使い回せる
- https://graphql.org/learn/queries/#fragments

---

## `git log -- [Filename]`
- 削除されたファイルの履歴を追いたいときとか… 💭

---

## WebP
- 画像フォーマット
- PNGやJPEGなどの従来のフォーマットに比べて、同じ画質でより小さなファイルサイズ
  - Webページのロード時間が短縮
  - `Lossless`なら高画質で圧縮可能
  - 帯域の節約: サーバーとクライアント間で転送されるデータ量が削減されるため、帯域使用量を節約できる
  - ストレージの節約: サーバー上での画像ファイルの保存スペースを節約できる
- 特徴
  - JPEGと比較して、効率的な圧縮アルゴリズムを使用していてファイルサイズは25-34%減
  - サポートする色の深さ: 最大24-bitのRGB色空間
  - `Lossless`と`Lossy`の両方をサポート: 画質を維持しつつ圧縮するLossless圧縮と、一部の画質を犠牲にしてさらに圧縮するLossy圧縮の両方をサポート
  - GIFや透過画像も対応
- ブラウザはほぼほぼ対応済み

---

## 健全に動くURLの長さ
- ブラウザによって異なるが、2000文字超えなければまあ大丈夫

---

## 長いクエリパラメータを持つURL
```url
# e.g. 商品一覧ページ
https://www.example.com/products?category=electronics&price_range=100-500&sort=popularity&page=2


# クエリパラメータ #####################################################
# category: 商品のカテゴリとして「electronics（電子製品）」を指定
# price_range: 価格帯を$100から$500の間を指定
# sort: 商品のソート基準として「人気度」を選択
# page: 商品リストの2ページ目を指定
```

- 特定の検索条件や設定(検索やソート、ページ表示)でページを共有したりブックマークするのに便利
- 読みにくさはあるが、管理画面などのエンドユーザーが操作しない画面ではあまり問題にならない
- セキュリティ: クエリパラメータはブラウザの履歴やサーバーのログ、リファラURLなど、多くの場所に記録される可能性があるため、機密情報を渡してはいけない
- フロントエンドフレームワークやライブラリを使用している場合、URLのクエリパラメータを用いることでUIの状態を保存・復元しやすい

> RESTfulな設計原則に従う場合、リソースの状態はURLにエンコードされるべきです。上記の例では、`/products`というリソースの特定の状態（フィルタされた状態）がURLにエンコードされています。これはRESTful設計の原則に従っています。

---

## Slug
- URLでリソースを一意に識別するための文字列
  - 記事タイトルやカテゴリー、タグなどのコンテンツ
  - 顧客企業名など、URLに直接含めるとセキュリティ上のリスクになるような情報(`uuid`の代替にもなる)
- アルファベット、数字、ハイフン、アンダースコアなどのURLで安全な文字のみ使える
- 目的: 人間が読めるURLを提供することで、SEO（検索エンジン最適化）やUXの向上に役立つ
```plain
Slugを使用しないURL: https://example.com/articles/12345

Slugを使用したURL: https://example.com/articles/introduction-to-slug
```

---

## `strftime()`
- 日付や時刻を指定した文字列でフォーマットして出力するときに使う
  ```ruby
  # Ruby
  
  Time.current.strftime("%Y-%m-%d %T")
  # => "2023-09-23 23:59:59"
  ```
- String Format Time の略

---

## 日本標準時(JST)の`+0900`表記
- UTCとの差分を表し、９時間進んでいるという意味
- `2023-01-01 12:00:00 +0900`の場合、UTCは`2023-01-01 03:00:00`

---

## 【Rails】`add_column`メソッドのafterオプション
```ruby
add_column :users, :middle_name, :string, after: :first_name

#############################################
 id | first_name | last_name
 ↓
 id | first_name | middle_name | last_name
#############################################
```
- 指定したカラムの直後に配置される
- MySQLでサポートされている(他はわからない)



---

## Monorepo / Polyrepo
- リポジトリ構成のこと
- Monorepo(モノレポ)
  - 複数プロジェクトやコンポーネントを単一のリポジトリで管理する手法
  - 同一リポジトリ内にBackendディレクトリとFrontendディレクトリがあるイメージ
- Polyrepo
  - プロジェクトやコンポーネント毎にリポジトリを分割して管理する手法
  - Pinapはこっち

---

## コンストラクターとは
- インスタンスを生成するタイミングで実行されるメソッドのこと
- オブジェクト指向言語の文脈
- Rubyでいうところの`Object#initialize`

---

## スプシで1行ごとに行の背景色を変える (テスト仕様書書くときに使った)
1. 範囲選択
2. 表示形式→条件付き書式
3. カスタム数式の「値または数式」に`=MOD(ROW(),2)=0`を入力→色を選択して完了

---

## ステージング状態のrubyファイルのみrubocopをかけたい
```shell
git diff --name-only --cached | grep '\.rb$' | pbcopy && bundle exec rubocop -A $(pbpaste | tr '\n' ' ')
```
- xargsを使うやり方だとうまくいかなかったため無理やりコピペしてる
- コマンド解説
  - `--name-only`オプションでファイル名のみ表示。デフォルトだとワークツリーの差分ファイルのみ絞り込まれるので、`--cached`オプションでステージングの差分ファイルのみ絞り込む
  - 末尾が`.rb`のファイルをgrep
  - したものをクリップボードにコピー
  - `tr '\n' ' '` で複数行の文字列を1行のスペース区切りに置換

---

## メソッド名について: `verify_**`
- 配列を渡して何かしらの検証を行って配列で返すメソッドに`verify_**_uuids`という命名をしたが結局`filter_**_uuids`にした
```ruby
def filter_any_uuids(hoge_uuids, foo_uuids)
  return [] if hoge_uuids.empty?

  hoge_uuids.select { |uuid| some_kind_of_verify }
end
```
- `verify_**`だと検証結果がbooleanで返ることを先見させてしまうため適してなさそうとの指摘をいただいた
- 「何かしらの検証をして絞り込んだものを返す」というニュアンスを持たせて`filter_**`を採用した

---

## `PostgreSQL 12.14 (Debian 12.14-1.pgdg110+1) on x86_64-pc-linux-gnu, compiled by gcc (Debian 10.2.1-6) 10.2.1 20210110, 64-bit`
- 上記コマンドの返り値
```shell
# PostgreSQLのバージョン
PostgreSQL 12.14

# この部分は、Debianパッケージとしてどのバージョンが使用されているかを示す
(Debian 12.14-1.pgdg110+1)

# アーキテクチャ（x86_64）とOS（Linux）に関する情報
on x86_64-pc-linux-gnu

# どのコンパイラ（gcc）とそのバージョン（10.2.1）を使ってコンパイルされたかを示す
compiled by gcc (Debian 10.2.1-6) 10.2.1 20210110

# 64ビットシステムで動作していることを示す
64-bit
```

### `(Debian 12.14-1.pgdg110+1)`
- `Debian`: Linuxディストリビューション
- `12.14-1`: 12.14 はPostgreSQLのメジャーバージョンとマイナーバージョン。後ろの -1 はDebian固有のパッケージリビジョン番号。
- `pgdg110+1`: このパッケージが`PostgreSQL Global Development Group（PGDG）`からリリースされたことを示す。
- `110`: はおそらくリポジトリやパッケージのバージョンを示している。
- 全体: Debianとしてパッケージ化されているので、Debianパッケージ管理ツール（`apt-get`、`dpkg`など）で管理できるという意味もある。

### `on x86_64-pc-linux-gnu`
- `x86_64`: CPUアーキテクチャを示す。`x86_64`64ビットのIntelまたはAMDのプロセッサに対応している。
- pc: パーソナルコンピュータ
- linux: OSがLinux
- gnu: GNUツールチェーン（コンパイラ、ライブラリなど）を使用していることを示す
- 全体: このPostgreSQLが64ビットのLinuxシステム、特にGNUツールチェーンとともに、パーソナルコンピュータで動作するようにコンパイルされたものであるという説明

### `64-bit`
- システムが64ビットで動作していることを明示している
- 64ビットシステムではメモリアドレスが64ビット幅であり、通常より多くのRAMをサポートできる
- この情報は、データベースが大規模なデータセットや高度な計算を処理する能力があるかどうかに関連する

### まとめるとこんな感じ
```
PostgreSQL 12.14 (Debian 12.14-1.pgdg110+1) on x86_64-pc-linux-gnu, compiled by gcc (Debian 10.2.1-6) 10.2.1 20210110, 64-bit
↓↓
「PostgreSQLのバージョンは12.14です。x86_64のCPUアーキテクチャのLinux上で動き、gccでコンパイルされています。64bitシステムで動作しています。」
```

---

## `ActiveRecord::Base.connection.select_value('SELECT version()')`
- このコマンドでやっていることは、ActiveRecordを通して、PostgreSQLデータベースに対して`SELECT version()`というSQLを実行し、その結果を取得している
```ruby
# Railsアプリケーションとデータベースの接続管理情報にアクセス
ActiveRecord::Base.connection

# SQLクエリを実行してPostgreSQLのバージョンを取得
select_value('SELECT version()')
```

---

## PostgreSQLのバージョン確認
### rails console
```ruby
# 確認コマンド
ActiveRecord::Base.connection.select_value('SELECT version()')
  TRANSACTION (0.2ms)  BEGIN
   (2.3ms)  SELECT version()

# 結果
=> "PostgreSQL 12.14 (Debian 12.14-1.pgdg110+1) on x86_64-pc-linux-gnu, compiled by gcc (Debian 10.2.1-6) 10.2.1 20210110, 64-bit"
```
**`select_value`メソッド**
```ruby
# Returns a single value from a record
def select_value(arel, name = nil, binds = [], async: false)
  select_rows(arel, name, binds, async: async).then { |rows| single_value_from_rows(rows) }
end
```
- ActiveRecordを介してSQLを直接実行するためのメソッド
- 最初の行の最初のカラムのみ返すため、単一の値を高速に取得したいときのみ使う(DBのメタデータが知りたいときや集約関数を使用する場合など)
- varidationやcallbackは適用されない


### システムのコマンドライン
```shell
docker-compose run --rm rails psql --version
Creating rails_run ... done

psql (PostgreSQL) 11.20 (Debian 11.20-0+deb10u1)
```

### SQL
```
docker compose run --rm rails bundle exec rails dbconsole

db=# SELECT version();

=> PostgreSQL 12.14 (Debian 12.14-1.pgdg110+1) on x86_64-pc-linux-gnu, compiled by gcc (Debian 10.2.1-6) 10.2.1 20210110, 64-bit
```

---

## PORO (Plain Old Ruby Object)
- ActiveRecord等を継承しない単なるRubyオブジェクトのこと
- 特定のデザインパターンを示すものではない
- ビジネスロジック等を切り出してファットコントローラーやファットモデルを解消するために使われる
