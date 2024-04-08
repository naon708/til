## 【Rails】とある1日を指定する `all_day` メソッド
- CS依頼で、〇〇日に作られたクラスを更新して欲しいとの要件があった。
- 「〇〇以前に…」とかはよくあるが特定の日付を指定する方法がパッと浮かばなかったので調べていたら `all_day` というメソッドが使えることがわかった。
- Range で書くよりずっと読みやすいしスッキリ。
```ruby
# 2024/04/01(00:00:00から23:59:59まで) に作成された資料を取得する
target_documents = SomeDocument.where(created_at: Time.zone.parse('2024/04/01').all_day)
target_documents.each do |doc|
  doc.update!(hoge: fuga)
end
```
- 参考: https://techracho.bpsinc.jp/hachi8833/2023_04_12/24876
