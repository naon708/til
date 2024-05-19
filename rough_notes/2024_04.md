## 【Rails】1000件以上のレコードを一括処理するときの考慮
### 選択肢
```ruby
each
find_each
find_in_batches
in_batches
```
### memo
- 一括処理したいレコードの数が1000件に満たない場合は `each` で問題ない
- 1000件以上となった場合に `each` 以外のメソッドを選択する
- `find_each` はDBから一括取得してメモリに展開した上で、レコードを1つずつ処理する
  - ので、ブロック内の処理が（メールを送るだけのように）単純な場合はこれで良さそう
- `find_in_batches` はDBから一括取得してメモリに展開した上で、レコードをまとめて処理する（モデルの配列で受け取る）
  - レコードに対して何かしら処理した上でCSVインポートする、など、　`find_*`　メソッドのブロック内でさらに一括処理を書きたいとき
- `in_batches` は ActiveRecord::Relation の配列で受け取るみたいだがユースケースはよくわかってない

### サンプルコード
```ruby
# find_each
users.find_each do |user|
  SomeMailer.send_mail(user.email, title, body).deliver_now
end

# find_in_batches
users.find_in_batches do |users|
  insert_data = users.map do |user|
    # 何かしらの処理
  end

  Interview.import(insert_data)
end
```

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
