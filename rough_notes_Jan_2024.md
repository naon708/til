## 【SQL】COALESCE
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
