- rails/activesupport/lib/active_support/core_ext
  - Ruby本体のメソッドをRails用に拡張したもの（馴染みがあるメソッドが多いのでわかりやすい）
  - https://github.com/rails/rails/tree/main/activesupport/lib/active_support/core_ext
- Rubyはあらゆるものがオブジェクト。メソッドもオブジェクトなので　Methodオブジェクト が存在する

 
## string/conversions.rb

### `to_date`
```ruby
def to_date
  ::Date.parse(self, false) unless blank?
end
```
- Date クラスの parse メソッドを呼び出してるだけ
- レシーバーが空(`""`)の場合は `nil` が返る
  - のに、 `::Date.parse("", false)` を実行すると DateError が返る
  - nil はどこから来た？？
  - unless 修飾子の仕様だった
    >```
    > 式 unless 式
    >```
    > 右辺の条件が成立しない時に、左辺の式を評価してその結果を返します。条件が成立すれば nil を返します。
    
    https://docs.ruby-lang.org/ja/latest/doc/spec=2fcontrol.html#unless
    ```ruby
    ::Date.parse("", false) unless "".blank?
    => nil
    ```
- `Date._parse` を使うと各要素を　Hash で返してくれる
  ```ruby
  Date._parse("2024-04-24")
  => {:year=>2024, :mon=>4, :mday=>24}
  ```
- ※メソッド名に `_` のプリフィックスが付いてるのは、利用者が使う前提ではないことを表す（ことが多い）
