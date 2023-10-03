### 【Rails】ActiveRecordのmergeメソッドはARレコードを返すメソッドを指定すること
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
