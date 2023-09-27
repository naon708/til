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
    
    ActiveModel::Type::Boolean.new.cast([])
    => true
    ```

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
