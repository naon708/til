プログラミング版、ワークアウト 💪 の記録

## A - Who Ate the Cake? / 2024-06-17
お題: https://atcoder.jp/contests/abc355/tasks/abc355_a
```ruby
# A と B の標準入力を整数で受け取る
A, B = gets.split(' ').map(&:to_i)

# A と B が同じ値の場合、 -1 を出力して終了
if A == B
  pp -1
  exit
end

# 下記でもOK
# pp(-1) && exit if A == B

# マスターとなる配列 X を用意
X = [1, 2, 3]

# 配列から A と B を除く
X.delete_if { |x| x == A || x == B }

# 残った要素が解答
pp X[0]
```
ひと言: 久しぶりの AtCoder。コード書き始める前に自然言語で書けるように。
