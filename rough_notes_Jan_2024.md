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
