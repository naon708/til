## 【Rails】`gem 'ancestry'`
- ActiveRecordモデルで階層構造を表現するGem

### e.g. 部署レコード
```ruby
class Department < ApplicationRecord
  has_ancestry cache_depth: true, orphan_strategy: :restrict
end
```
```ruby
#<Department:0x0000aaab01035ed8
 id: 1,
 name: "東京支社",
 uuid: "3cf273e9-3b37-461f-9fb1-14b88dc7e259",
 ancestry: nil,
 ancestry_depth: 0>

#<Department:0x0000aaab03998280
 id: 7,
 name: "購買部",
 uuid: "44990366-ac5d-4b2d-843a-3c0b0c15cad1",
 ancestry: "1",
 ancestry_depth: 1>

#<Department:0x0000aaab04413ca0
 id: 27,
 name: "(子)購買部",
 uuid: "61ec4421-ff4b-4a1f-b2d7-1ca1b5308961",
 ancestry: "1/7",
 ancestry_depth: 2>

#<Department:0x0000aaab01bd7188
 id: 30,
 name: "(孫)購買部",
 uuid: "cab8f60e-adf0-4981-87c7-f723bac67082",
 ancestry: "1/7/27",
 ancestry_depth: 3>
```

### 提供されるメソッド群
```ruby
d = Department.find_by(display_name: '購買部')
cd = Department.find_by(display_name: '(子)購買部')
cd2 = Department.find_by(display_name: '(子2)購買部')
gcd = Department.find_by(display_name: '(孫)購買部')

# 親レコード
gcd.parent
=> Department Load (3.6ms)  SELECT "departments".* FROM "departments" WHERE "departments"."id" = $1 LIMIT $2  [["id", 27], ["LIMIT", 1]]

# 子レコードすべて
gcd.children
=> Department Load (4.1ms)  SELECT "departments".* FROM "departments" WHERE "departments"."deleted_at" IS NULL AND "departments"."ancestry" = '1/7/27/30'

# 最上位レコード
gcd.root
=> Department Load (3.2ms)  SELECT "departments".* FROM "departments" WHERE "departments"."id" = $1 LIMIT $2  [["id", 1], ["LIMIT", 1]]

# 直系のレコードすべて
gcd.path
=> Department Load (9.8ms)  SELECT "departments".* FROM "departments" WHERE "departments"."deleted_at" IS NULL AND "departments"."id" IN (1, 7, 27, 30) ORDER BY "departments"."ancestry" ASC NULLS FIRST

# レシーバー配下のレコードすべて(レシーバーも含む)
d.subtree
=> Department Load (23.0ms)  SELECT "departments".* FROM "departments" WHERE "departments"."deleted_at" IS NULL AND ("departments"."ancestry" LIKE '1/7/%' OR "departments"."ancestry" = '1/7' OR "departments"."id" = 7)

# depthが2以降のレコードすべて
d.subtree.from_depth(2)
=> Department Load (12.4ms)  SELECT "departments".* FROM "departments" WHERE "departments"."deleted_at" IS NULL AND ("departments"."ancestry" LIKE '1/7/%' OR "departments"."ancestry" = '1/7' OR "departments"."id" = 7) AND (ancestry_depth >= 2)
```

### 参考
- https://github.com/stefankroes/ancestry
- https://zenn.dev/kou_row_line/articles/48e2262b7b2e70

---

## 【Rails】ActiveRecordのmergeメソッドはARレコードを返すメソッドを指定すること
```ruby
company_ids = Company.joins(import_csv_setting: :import_csv_schedule).merge(ImportCsvSchedule.targets_at_the_time(Time.zone.now)).ids
```
- この場合、`ImportCsvSchedule.targets_at_the_time(Time.zone.now)`は`ActiveRecord::Relation`オブジェクトを返す必要がある
- 返り値が`ActiveRecord::Relation`オブジェクトであれば、scopeでもクエリメソッドでもクラスメソッドでも構わない

---

## 【Rails】`rails stats`
- アプリケーションのコードベースの統計情報を表示するコマンド

- Name: セクション(Controllers, Models, Helpers, Mailersなど)

```
Lines: コメントや空行を含むコードの合計行数
LOC (Lines of Code): コメントや空行を除くコードの行数
Classes: クラスの数
Methods: メソッドの数
M/C (Methods per Class): クラスごとの平均メソッド数(オブジェクトの責任や役割が大きくなりすぎていないかの指標)
LOC/M (Lines of Code per Method): メソッドごとの平均行数(メソッドが長くなりすぎていないかの指標)
```
---

## 【Ruby】のfindメソッド
```ruby
# find(ifnone = nil) -> Enumerator
[1, 2, 3, 4].find { |x| x.even? } # => 2
```
- ActiveRecordの`find`とややこしいが、モデルに対して実行していなかったり`find`にブロック渡してたらRubyのメソッドと思ってよさそう
- `Enumerable#find` / `Enumerable#detect` (エイリアス)
- 要素を評価してtrueになった最初の値を返す
- trueになる値が無い場合
  - ifnoneが指定されていない場合: `nil`を返す
  - ifnoneが指定されている場合: ifnoneをcallした結果を返す
 
    > ifnone には、call メソッドを持つ任意のオブジェクトを指定することができます。一般的に使われるのは Procオブジェクトやラムダ。
---

## 【Rails】日付操作
```ruby
# 日
Time.current.yesterday   # => 昨日の同時刻
Time.current.tomorrow   # => 明日の同時刻
Time.current.ago(1.day)   # => 1日前の同時刻
Time.current.since(1.day)   # => 1日後の同時刻

# 週
Time.current.prev_week   # => 前週の週初め(月曜)の00:00:00
Time.current.next_week   # => 翌週の週初め(月曜)の00:00:00
Time.current.ago(1.week)   # => 1週間前の同時刻
Time.current.since(1.week)   # => 1週間後の同時刻

# 月
Time.current.prev_month   # => 1ヶ月前の同時刻
Time.current.next_month   # => 1ヶ月後の同時刻

# 年
Time.current.prev_year   # => 1年前の同時刻
Time.current.next_year   # => 1年後の同時刻
```
```ruby
# その他
Time.current.beginning_of_day
Time.current.end_of_day

Time.current.beginning_of_week   # => 今週頭(月曜)の00:00:00
Time.current.end_of_week   # => 今週末(日曜)の23:59:59

Time.current.beginning_of_month   # => 月初の00:00:00
Time.current.end_of_month   # => 月末の23:59:59

Time.current.prev_week(:thursday)   # => 前週の木曜日の00:00:00
Time.current.next_week(:thursday)   # => 翌週の木曜日の00:00:00
```

---

## 【Rails】`Model#reset_column_information`
- マイグレーションファイルの中でモデルを触るときに付けるべき記述
```ruby
class AddSalaryToPeople < ActiveRecord::Migration[7.2]
  def up
    add_column :people, :salary, :integer
    Person.reset_column_information
    Person.all.find_each do |person|
      person.update_attribute :salary, SalaryCalculator.compute(person)
    end
  end
end
```
> マイグレーションでカラムを追加し、その直後にカラムにデータを投入したいことがあります。
> 
> その場合、モデルに新しいカラムが追加された後の最新のカラムデータがあることを確認するために、`Base#reset_column_information`を呼び出す必要があります。

https://github.com/rails/rails/blob/main/activerecord/lib/active_record/migration.rb#L448-L463

- 上記コードで、Personモデルを触るとDBの状態がキャッシュされる(ようにRailsができてる)
- 後続のマイグレーションファイルでキャッシュをリセットしないままPersonモデルを触るとエラーになる
- なので、`reset_column_information` でキャッシュをリセットして、 次に使うときにもう一度DBからcolumn情報を取得するようにする

https://blog.onk.ninja/2017/10/18/use_reset_column_information

---

## 【Rails】`ActiveModel::Type::Boolean.new.cast`
```ruby
# e.g. 印刷用かどうか
ActiveModel::Type::Boolean.new.cast(params[:for_print])
```
- クライアントサイドから渡された値をboolenに変換したいときに使う
 - `FALSE_VALUE`に含まれる値の場合、`false`を返す
   - `FALSE_VALUES = [ false, 0, "0", :"0", "f", :f, "F", :F, "false", :false, "FALSE", :FALSE, "off", :off, "OFF", :OFF, ].to_set.freeze`
 - 引数が空文字の場合は`nil`を返す
 - それ以外は`true`を返す
    ```ruby
    ActiveModel::Type::Boolean.new.cast('')
    => nil
    
    ActiveModel::Type::Boolean.new.cast(nil)
    => nil

    ActiveModel::Type::Boolean.new.cast([])
    => true
    ```

https://api.rubyonrails.org/classes/ActiveModel/Type/Boolean.html

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

## 【Gem】discard
- https://github.com/jhawthorn/discard
- 論理削除
- アーカイブを表現したり

---

## `SecureRandom.urlsafe_base64`
- URL-safeでランダムなbase64文字列を生成して返す
- URL-safeとは、URLに含まれても問題ない状態のこと

---

## Webhookとは
- 何かしらのイベントきっかけに、別のシステムに通知を送る仕組みのこと
  - HTTPのPOSTリクエストで特定のURL（Webhook URL）に対してデータを送信する。
  - e.g. Slack通知
