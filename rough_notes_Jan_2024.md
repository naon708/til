## 【Rails】関連先がないレコードを取得する
```ruby
ParentModel.where.missing(:child_model)
```

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
