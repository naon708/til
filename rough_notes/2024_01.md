## 【Rails】`update!`で更新を試みた際にCallbackと衝突した

```ruby
wip
```

---

## 【Rails】レコードを物理削除する際に、関連先レコードが論理削除されているときの考慮点
### 結論
親レコードを物理削除する際は、関連先に論理削除されているレコードを持っていないか確認しよう

### 背景
`※前提として、論理削除をサポートするparanoiaというGemを使用しているものとする`<br>
とある親レコードを物理削除しようとした際、下記のように関連先レコードが存在しないことを確認した
```ruby
# 関連先レコードが存在するか確認
Parent.reflect_on_all_associations.map(&:name)
=> [:children, :hoge, :fuga]

# 何かしらの条件で絞り込んで削除を試みる
parent_records = Parent.where(id: filterd_ids)
parent_records.map(&:child)
=> [[], [], [], []]
```
そして親レコードを物理削除しようとしたらエラーが発生した。
```ruby
parent_record.each(&:really_destroy!)
```
```
DETAIL:  Key (id)=(2355640) is still referenced from table "child_tables"
=> 「親レコードのKeyが関連先から参照されています」
```
paranoiaのメソッドである`with_deleted`や`only_deleted`を使って論理削除済みのレコードが存在しないか確認するのを忘れないように
```ruby
## 論理削除されたレコードが存在する
parent_records.map(&:children).map(&:only_deleted).flatten.present?
=> true
```
```ruby
parent_records = Parent.with_deleted.where(id: filterd_ids)

parent_records.each do |parent|
  parent.children.with_deleted.each do |child|
    child.really_destroy!
  end
  parent.really_destroy!
end
```

---

## 【SQL】COALESCE
```ruby
some_tables_uniq_index   (hoge_id, COALESCE(deleted_at, 'infinity'::timestamp without time zone)) UNIQUE
```
### 何のための記述？
wip

---

## 【Vue】watch関数に配列を指定する
監視したい要素が、
### 単一のとき
```ts
watch(foo, (newFoo) => {
  // ....
})
```

### 複数のとき
```ts
watch(
  // 配列内の要素のいずれかに変更が加わったときに第2引数の処理を実行する
  () => [foo, bar],
  ([newFoo, newBar]) => {
    // ....
  },
)
```
### 例
- 1つのセクションに複数フォームがある場合に、フォームの組み合わせでバリデーションをかけたいときに使った
  - `フォームAが空の場合、フォームBとフォームCは入力できない`

https://ja.vuejs.org/guide/essentials/watchers

---

## 【Rails】関連先がないレコードを取得する
親モデルに紐づくレコードが存在しないオブジェクトを取得する
```ruby
# こう書く必要があったのが
ParentModel.left_joins(:child_model).where(child_model: { id: nil })

# こう書ける
ParentModel.where.missing(:child_model)
```
ActiveRecordメソッドチェーンで繋げることもできる

参考: https://techracho.bpsinc.jp/hachi8833/2021_05_26/107973

---

## 【Rails】〇〇文字以上の氏名ってどれくらいあるんだろう？を調べた
表示崩れのきっかけとなるデータが存在するかどうか知りたかった
```sql
# SQL
SELECT fullname
FROM customers
WHERE LENGTH(c.fullname) >= 20
```

```ruby
# ActiveRecord
Customer.where('LENGTH(fullname) >= 20').pluck(:fullname)
```
