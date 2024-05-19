## 社内SQL講座
- `_`は厄介
- 部分一致のLIKEは重い
  - `item%`は問題ないが、`%item`とか`%item%`は処理が重くなる
  - 全文検索はこんなのある（相当レコード数が多ければ使う） https://pgroonga.github.io/ja/
- `count(*)`は慣例でアスタリスクが多い
- (慣れないうちは)select句は最後に読む(書く)とわかりやすい
- asの記載は省略できる
- having: group byの結果に対してwhereかけたい時
  - where → group by → having（順序がある）
- select文の結果も何らかの形でテーブルである
- UNION: 主に集計で使う（Webアプリではあんまり意識することはない）

### おまけ
- bnf記法 https://medium-company.com/bnf記法/
- スコット/タイガー: Oracleデータベースのデフォルトユーザー/パスワード
- シャーディング: DBの水平分割

---

## Linuxコマンドの`.`と`source`は同じ
```bash
. ~/.zshrc
source ~/.zshrc
```

---

## 【M1 Mac】下記の設定をしないとbrewコマンドが使えなかった
```zsh
# .zshrc
typeset -U path PATH
path=(
 /opt/homebrew/bin(N-/)
 /opt/homebrew/sbin(N-/)
 /usr/bin
 /usr/sbin
 /bin
 /sbin
 /usr/local/bin(N-/)
 /usr/local/sbin(N-/)
 /Library/Apple/usr/bin
)
```
https://zenn.dev/sprout2000/articles/bd1fac2f3f83bc
