## 【Rails】カラム追加時、既存レコードにはデフォルト値を入れたくないが、レコードが新規作成された時はデフォルト値が入るようにしたい
- 顧客が使う、とある基準値マスターテーブルがあり、そこに項目を追加するときの話
- 具体的な背景説明は複雑なのでスキップ
- 4パターンで検証
  - そもそもマイグレーションしたときにデフォルト値を指定したら既存レコードにも反映されるかどうかの確認からした

```ruby
class AddColumnsHoge < ActiveRecord::Migration[7.0]
  def change
    # 1. 何も指定しない
    add_column :some_thresholds, :test_hoge, :float, comment: "ほげ"
    # 2. デフォルト値のみ設定
    add_column :some_thresholds, :test_fuga, :float, default: 100.0, comment: "ふが"
    # 3. NOT NULL制約とデフォルト値を設定
    add_column :some_thresholds, :test_piyo, :float, default: 100.0, null: false, comment: "ぴよ"
    # 4. デフォルトNULLで設定　→　デフォルトを100に設定
    add_column :some_thresholds, :test_muge, :float, default: nil, comment: "むげ"
    change_column_default :some_thresholds, :test_muge, from: nil, to: 100.0
  end
end
```
```ruby
# 既存レコードの確認
SomeThreshold.first
 .... 
 test_hoge: nil,
 test_fuga: 100.0,
 test_piyo: 100.0,
 test_muge: nil
```
```ruby
SomeThreshold.create!(....)

# マイグレーション実行後に作成したレコードの確認
SomeThreshold.last
 ....
 test_hoge: nil,
 test_fuga: 100.0,
 test_piyo: 100.0,
 test_muge: 100.0
```
### 結論
- 4番目の方法で実現できた。
- デフォルト値を指定してマイグレーションすると既存レコードにもカラムにも値が入ることが確認できた
- `change_column_default`メソッドは from と to の指定をしないとRollbackできないので注意（[参考](https://railsguides.jp/active_record_migrations.html#%E3%82%AB%E3%83%A9%E3%83%A0%E3%82%92%E5%A4%89%E6%9B%B4%E3%81%99%E3%82%8B)）
