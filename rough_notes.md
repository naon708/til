## PORO (Plain Old Ruby Object)
- ActiveRecord等を継承しない単なるRubyオブジェクトのこと
- 特定のデザインパターンを示すものではない
- ビジネスロジック等を切り出してファットコントローラーやファットモデルを解消するために使われる

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


## `ActiveRecord::Base.connection.select_value('SELECT version()')`
- このコマンドでやっていることは、ActiveRecordを通して、PostgreSQLデータベースに対して`SELECT version()`というSQLを実行し、その結果を取得している
```ruby
# Railsアプリケーションとデータベースの接続管理情報にアクセス
ActiveRecord::Base.connection

# SQLクエリを実行してPostgreSQLのバージョンを取得
select_value('SELECT version()')
```

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

## スプシで1行ごとに行の背景色を変える (テスト仕様書書くときに使った)
1. 範囲選択
2. 表示形式→条件付き書式
3. カスタム数式の「値または数式」に`=MOD(ROW(),2)=0`を入力→色を選択して完了

---

## コンストラクターとは
- インスタンスを生成するタイミングで実行されるメソッドのこと
- オブジェクト指向言語の文脈
- Rubyでいうところの`Object#initialize`

---

