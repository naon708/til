プログラミング版、ワークアウト 💪 の記録

テストケース: https://www.dropbox.com/sh/nx3tnilzqz7df8a/AAAYlTq2tiEHl5hsESw6-yfLa?e=2&dl=0

## A - Buildings / 2024-06-20
お題: https://atcoder.jp/contests/abc353/tasks/abc353_a
```ruby
# 入力: 4
# 出力: 3 2 5 2

# ビルの数を整数で受け取る
building_count = gets.to_i

# 各ビルの高さを整数の配列で受け取る
building_heights = gets.split(' ').map(&:to_i) # [3, 2, 5, 2]

# 配列の数でループ
# 一番左の要素と比較し、より大きい数が見つかったらその index + 1 を出力する
building_count.times do |i|
  if building_heights[0] < building_heights[i]
    p(i + 1)
    exit
  end
end

# 最後まで見つからなかったら、 -1 を出力する
p -1
```

## A - Subsegment Reverse / 2024-06-19
お題: https://atcoder.jp/contests/abc356/tasks/abc356_a
```ruby
# [1, 2, 3, 4, 5]

# A: [1]
# B: [5]
# C: [2, 3, 4] → [4, 3, 2]
# A + C + B

# 整数を受け取る (N, L, R)
N, L, R = gets.split(' ').map(&:to_i)

# 1 ~ N の等差数列マスターを配列で定義
base_arr = [*(1..N)]

# L = R の場合、マスターをそのまま出力する
if L == R
  puts base_arr.join(' ')
  exit
end


# 反転部分、先頭部分、末尾部分 の配列をそれぞれ切り出す
reversed_arr = base_arr[(L-1)..(R-1)].reverse
head_arr = base_arr[..(L-2)]
tail_arr = base_arr[R..]

if L == 1 && R == N
  puts reversed_arr.join(' ')
elsif L == 1
  puts (reversed_arr + tail_arr).join(' ')
elsif R == N
  puts (head_arr + reversed_arr).join(' ')
else
  puts (head_arr + reversed_arr + tail_arr).join(' ')
end
```
### メモ
配列内の特定の範囲に代入できるの知らなかった…
```ruby
arr = [1, 2, 3, 4, 5, 6, 7]
arr[2..4] = arr[2..4].reverse

arr
=> [1, 2, 5, 4, 3, 6, 7]
```

## A - Welcome to AtCoder Land / 2024-06-18
お題: https://atcoder.jp/contests/abc358/tasks/abc358_a
```ruby
# 文字列を2つ入力
S, T = gets.split(' ')

# S が「AtCoder」かつ T が「Land」の場合は「Yes」を出力。それ以外の場合は「No」を出力。
if S == 'AtCoder' && T == 'Land'
  puts 'Yes'
else
  puts 'No'
end
```

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
### メモ
久しぶりの AtCoder。コード書き始める前に自然言語で書けるように。
